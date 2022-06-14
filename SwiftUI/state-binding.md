---
layout: post
title: "[SwiftUI] @State, @Binding "
tags: [SwiftUI, Combine]
comments: true
---

요즘 틈틈히 SwiftUI를 공부하고 있어서 겸사겸사 잊어버리기 전에 @State와 @Binding에 대해서 좀 정리해둘까 합니다.  

사실 공식문서도 봐보고 이런 저런 글을 봤습니다만, 이봉원 님의 [스윗한 SwiftUI](http://www.yes24.com/Product/Goods/89912849)의 글만큼 간략하고 이해하기 쉽게 설명된 요약이 없는 것 같아요. 

> #### _@State와 @Binding_
@State는 뷰 자체에서 가져야 할 상태 프로퍼티이자 원천 자료로, 어떤 데이터에 대한 영속적인 상태를 저장하고 관찰하는 역할을 수행합니다. 반면 상위 뷰가 가진 상태를 하위 뷰에서 사용하고 수정할 수 있게 해 주는 파생 자료에는 @Binding을 사용합니다. 이때 바인딩 프로퍼티는 연산 프로퍼티의 형태로 사용되어 그 자신이 직접 값을 보유하는 대신, 값을 읽고 수정하여 다른 뷰에 갱신된 데이터를 전달하는 역할을 합니다. 

이 요약을 좀 더 쉽게 표현하면, State는 SwiftUI 프레임워크 내의 뷰에서 상태값을 나타낼 때 사용하고, Binding은 상위뷰의 어떤 상태값을 하위뷰에서 사용하고 수정할 수 있게 할 때 사용한다고 볼 수 있을 것 같습니다. 이제부터는 공식문서와 함께 설명해볼게요. 

[공식문서](https://developer.apple.com/documentation/swiftui/state)에서는 @State를 이렇게 설명하고 있어요. _**"SwiftUI에서 관리하고 있는 값을 읽고, 쓸 수 있는 `property wrapper type`"**_. 그러면 이제 우리는 보려고 했던 State는 뒤로 미뤄두고 property wrapper부터 공부를 하러 갑니다. 그러다 Swift 5.1 Release를 또 공부하고, 그러다 또 다른 것을 공부하고...😂. 

어쨋건, 그런 이유로 property wrapper에 관해서는 이 글에서는 다루지 않을 생각입니다. 그저 property wrapper란, _**"그 특성을 사용하는 것만으로 미리 구현된 기능을 쉽고 깔끔하게 재사용할 수 있게 해주는 녀석이다."**_ 정도로만 이해하면 될 것 같아요. 이 부분에 관해서는 다음 글에서 조금 더 자세하게 언급하도록 하겠습니다. 😄 

```swift
@frozen @propertyWrapper struct State<Value>
```

이어서 문서를 볼까요 ? 

> SwiftUI manages the storage of a property that you declare as state. When the value changes, SwiftUI updates the parts of the view hierarchy that depend on the value. Use state as the single source of truth for a given value stored in a view hierarchy.

음, 내용을 읽어보면 SwiftUI에서 @State를 사용해서 선언하고 해당 프로퍼티의 값이 변경되면 해당 값에 의존하는 뷰가 업데이트 되는 느낌인 것 같습니다. 즉, 뷰 자신의 UI 상태를 저장하기 위한 데이터로 사용되고 있다는 거죠. 마치 Rx처럼 관찰?하는 느낌인 것 같아요. 

예시로 나와있는 코드와 실제 동작을 좀 볼까요 ?

![](https://velog.velcdn.com/images/dev_kickbell/post/830f2576-7c4c-439f-b924-8464e1eb4327/image.gif)   


```swift
import SwiftUI

struct PlayButton: View {
    @State private var isPlaying: Bool = false
    
    var body: some View {
        Button(isPlaying ? "Pause" : "Play") {
            isPlaying.toggle()
        }
    }
}
```

코드를 보면, 일단 isPlaying 는 저장 프로퍼티가 있고 @State 특성을 가지고 있네요. 또, body안에 버튼이 하나 있는데 기본적으로 제공되는 여러 타입 중에는 생성자로 title값을 받고 action이라는 클로저를 받는 타입을 사용하고 있네요. 

`Button(title: StringProtocol, action: () -> Void)` 

여기서의 @State는 딱히 무엇을 하는지 짐작이 가진 않습니다. 그럼 이 코드를 UIKit으로 구현한다면 어떻게 될까요 ? 

```swift
import UIKit

class ViewController: UIViewController {
  
  @IBOutlet weak var playButton: UIButton!
  
  private var isPlaying: Bool = false {
    didSet{
      updateUI()
    }
  }
  
  @IBAction func didTapped(_ sender: UIButton) {
    isPlaying = !isPlaying
  }
  
  private func updateUI() {
    isPlaying ? playButton.setTitle("Pause", for: .normal) : playButton.setTitle("Play", for: .normal)
  }
}
```
설명해보자면, toggle 부분은 didTapped()이 부분이 될테고, UIKit에서 isPlaying 값이 변경되면 didSet을 통해 updateUI()를 해주고 있다면, SwiftUI에서는 위에서 설명드렸던 것처럼 @State 특성으로 isPlaying을 관찰하고 있다가 해당 값이 변경되면 내부적으로 해당 값이 변경됐다고 알려주고 body 내의 Button 이라는 뷰를 새로 그려주고 있습니다. 

정리하자면, SwiftUI에서는 "어떤 이벤트가 발생하면 어떻게 처리해라." 라는 명령형 코드 대신에 "어떤 값에 따라서 뷰를 그려라"는 식의 선언형으로 코드가 구현되어 있는 거죠. 

> #### toggle() 
예제코드에서는 Swift 4.2부터 나온 toggle() 이 적용되어 있습니다. 별건 아니고, 아래처럼 구현되어 있어서 `isPlaying = !isPlaying`를 하는 대신 더 간단하게 값을 바꿔줄 수 있습니다. 
```swift
extension Bool {
  @inlinable
  public mutating func toggle() {
    self = !self
  }
}
```

이어서 공식문서의 샘플코드를 보겠습니다. 

```swift
struct PlayButton: View {
    @Binding var isPlaying: Bool

    var body: some View {
        Button(isPlaying ? "Pause" : "Play") {
            isPlaying.toggle()
        }
    }
}

struct PlayerView: View {
    @State private var isPlaying: Bool = false

    var body: some View {
        VStack {
            PlayButton(isPlaying: $isPlaying) // Pass a binding.
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
      PlayerView()
    }
}
```

크게 바뀐건 없죠? 동작도 똑같습니다. 바뀐 것은 PlayerView() 내에 PlayButton()이 들어 있는 것 뿐입니다. 또 자식뷰인 PlayButton()의 프로퍼티가 @Binding이라는 것 정도겠네요. 

위에서 설명했던 것처럼 @Binding은 자식뷰에 상태값을 전달하고 사용하기 위해 쓰입니다. 여기는 약간 그런느낌인거에요. 부모뷰인 PlayerView()의 상태값인 isPlaying을 마치 Rx처럼 옵져버블 형태로 갖고있는 isPlaying이 있습니다. 그리고 자식뷰인 PlayButton()의 프로퍼티에서 @Binding 이라는 특성을 사용하면, 부모뷰를 구독?하진 않아도 해당 스트림?이라고 해야할까요 그것의 의존성을 주입받을 수 있게 되는 거죠.

그러면 이런 생성자가 지원됩니다. `PlayButton(isPlaying: Binding<Bool>)` 그리고 여기에 $ 접두어를 이용해서 바인딩을 할 수 있게 되는 거에요. 

여기서 달러($) 접두어는 프로퍼티 래퍼 내부의 projectedValue라는 프로퍼티를 이용하게 되는데, 이 타입이 Binding 타입이기 때문에 Binding 타입의 매개변수에 상태 프로퍼티를 전달해 줄 수 있는 것이죠. 

이렇게 보면 조금 헷갈리실 수도 있을 것 같으니 다른 예제를 하나 더 봐보겠습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/f3616ddf-84a2-451a-8f19-73f7b3b8e81d/image.gif)   


```swift
import SwiftUI

struct ContentView: View {
  @State private var isFavorite = true
  
  var body: some View {
    Toggle(isOn: $isFavorite) {
      Text("isFavorite : \(isFavorite.description)")
    }.padding()
  }
}


struct ContentView_Previews: PreviewProvider {
  static var previews: some View {
    ContentView()
  }
}
```

코드는 아주 간단하죠 ? 공식문서 코드보다는 약간 보기 편하실 거에요. 차이점은 위에서는 PlayerButton을 저희가 구현했다고 하면, 지금은 기본으로 제공되는 템플릿인 Toggle을 사용했다는 것 정도입니다. 

Toggle을 보면 매개변수로 `Toggle(isOn: Binding<Bool>, label: () -> _)` isOn 이라는 바인딩 타입을 받고 있어요. 저희는 아까 직접 구현했었죠. Toggle 내부는 아까 저희가 구현한 것처럼 @Binding 타입으로 구현 되어 있을 것이고, isFavorite 은 @State 라는 프로퍼티 래퍼 타입으로 내부의 projectedValue 프로퍼티가 바인딩 타입이므로 $ 접두어를 통해 $isFavorite 과 같이 사용할 수 있는 겁니다. 

정리하자면, 우리는 부모뷰(ContentView)죠. 그리고 Toggle은 자식뷰이구요. 부모뷰 내의 isFavorite는 뷰 자체에서 가져야 할 상태값이죠. 그리고 이 부모뷰가 가진 상태값을 하위뷰에서 사용하게 하기 위해서 @State 특성을 사용했습니다. @State는 프로퍼티 래퍼 타입으로 그 내부에는 $isFavorite과 같이 사용하게 해 줄 수 있는 코드가 구현되어 있어요. 그리고 Toggle의 isOn은 @Binding 특성으로 설계되어 있을 것이고, 같은 Binding<Bool>타입이기 때문에 $isFavorite의 값을 넣어줄 수 있는 것이겠죠. 그리고 Toggle의 isOn은 위에서 말했던 것처럼 연산프로퍼티 형태로 사용이 되죠. 왜? 토글은 부모뷰인 ContentView가 가진 상태를 표현하거나 변경하는 역할만 하면 되기 때문입니다. 그 자신이 어떤 값을 보유하고 수정한다면 부모뷰의 값과 불일치 문제가 발생할 수 있기 때문이지요. 아 그리고 상태값은 해당뷰가 소유하고 관리한다는 개념을 명시적으로 나타내기 위해 항상 private 접근 레벨을 사용하는 것이 좋아요. 
  
어떻게, 좀 이해가 되셨으려나 모르겠습니다. 사실 너무 편하게? 되어있어서 그냥 갖다 쓰기만 해도 되긴 하는데요. 그래도 알고 쓰는게 좋기 때문에,,ㅎㅎ 도움이 되셨으면 좋겠습니다.   


    
## Conclusion
- `@State` - View 내부에서의 상태(State)값을 저장, 관찰하고 싶을 때 사용한다.
- `@Binding`- 상위 View의 상태(State)를 하위 View에서 받아서 표현하고 변경되면 그 값을 상위 View에 전달하는 역할을 한다. 즉, `@Binding` 속성은 자신의 값을 보유 또는 수정하지 않는다. 하위 View에서 값을 보유 또는 수정한다면 상위 View의 값과 불일치하는 문제가 발생할 수 있기 때문이다. 그래서 연산프로퍼티 형태로 사용된다.
- `@State`값은 항상 해당 뷰가 소유하고 관리한다는 개념을 명시적으로 나타내기 위해 `private` 접근 레벨을 사용하는 것이 좋다.
- `@State`특성은 상태(State)가 변할 때마다 UI가 리프레시 될 수 있도록 구독 메커니즘(Binding)을 제공하므로 MVVM 패턴의 ViewModel의 역할을 한다. 즉, 이런 것을 예로들어 SwiftUI는 MVVM 아키텍처가 내재되어 있다 또는 매커니즘이 매우 유사하다고 볼 수 있다.
    

## Reference  
https://nsios.tistory.com/120         
https://developer.apple.com/documentation/swiftui/state       
https://developer.apple.com/documentation/swiftui/binding       
http://www.yes24.com/Product/Goods/89912849       
https://seons-dev.tistory.com/entry/SwiftUI%EB%A5%BC-%EC%9C%84%ED%95%9C-Clean-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98
