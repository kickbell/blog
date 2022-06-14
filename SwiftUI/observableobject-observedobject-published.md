---
layout: post
title: "[SwiftUI] ObservableObject, @ObservedObject, @Published "
tags: [SwiftUI, ObservableObject, ObservedObject, Published] 
comments: true
---


[저번 글](https://io3s.github.io/State-and-Binding/)에서는 @State와 @Binding에 대해서 알아봤는데요.  
오늘은 ObservableObject, @ObservedObject, @Published에 대해서 알아볼까 합니다. 

@State가 어떤 뷰의 내부의 상태값을 저장하고 다루기 위한 용도였다면, 조금 더 복잡한 상황에서 뷰 외부의 모델이 가진 데이터를 다루기 위한 도구도 존재합니다. 그리고 그 중에서도 값 타입(Value type)이 아닌 참조 타입(Reference type)을 사용하는 경우에 ObservableObject 프로토콜을 사용할 수 있습니다.

ObservableObject은 프로토콜이고, iOS에서는 13.0부터 사용가능하며, Combine 프레임워크에 포함되어 있습니다. 

아래 코드에 보이는 것처럼 AnyObject를 채택하고 있기 때문에 **구조체, 열거형 타입**에는 사용이 불가하죠. 

```swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
public protocol ObservableObject : AnyObject {

    /// The type of publisher that emits before the object has changed.
    associatedtype ObjectWillChangePublisher : Publisher = ObservableObjectPublisher where Self.ObjectWillChangePublisher.Failure == Never

    /// A publisher that emits before the object has changed.
    var objectWillChange: Self.ObjectWillChangePublisher { get }
}
```

내부에는 objectWillChange 라는 퍼블리셔를 하나 가지고 있는데, Rx로 치면 옵저버블 같은 녀석이지요. 그런데, 이름을 보아하니 아마도 이 프로토콜을 채택한 객체가 willChange될 때, 이벤트를 방출한다 그런느낌으로 보면 될 것처럼 생겼어요. 마치 ViewLifeCycle 처럼 말이죠. 😄 

정리하자면 ObservableObject는 _**1. 클래스에만 사용할 수 있고, 2. 이 프로토콜을 채택하게 되면 뷰 내의 상태값만 관찰했던 @State와는 다르게 뷰 외부의 클래스를 관찰할 수 있게 됩니다.**_ 그리고 사용하는 곳에서 @ObservedObject 특성을 붙여주는 거죠. 

설명은 아래 코드를 보고 이어서 해보겠습니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/74a64cac-b6d2-4e07-a69c-ed59d3eb89cc/image.png)
```swift
import SwiftUI

class ViewModel: ObservableObject {
  let name = "User name"
  var score = 0
}

struct ContentView: View {
  
  @ObservedObject var viewModel: ViewModel
  
  var body: some View {
    VStack(spacing: 30) {
      Text(viewModel.name)
      Button {
        self.viewModel.score += 1
      } label: {
        Text(viewModel.score.description)
      }
    }
  }
}

struct ContentView_Previews: PreviewProvider {
  static var previews: some View {
    ContentView(viewModel: ViewModel())
  }
}
```
코드를 보면 label인 user name은 고정값이고, 버튼을 클릭하면, 버튼의 title값인 score값이 1씩 증가하는 로직입니다. 

음 정말 이해하기 쉽게 저희 Rx 작업할 때 많이 쓰는 ViewModel이라는 개념을 가져와봤어요. 이걸 약간 Rx느낌으로 이해하면 이런겁니다. 

ViewModel은 ObservableObject를 채택하게 되면서 `관찰할 수 있는 객체`가 될 수 있는거에요. 그리고 우리는 그걸 인스턴스로 선언하는데, @ObservedObject 라는 속성을 부여하면서 "아 얘는 관찰되고 있는 애다"라고 연결해주는 것이죠. 

그러면 이 상태로 앱을 실행해보면 우리가 예상하는 기대값처럼 버튼을 탭하면 화면이 제대로 갱신될까요? 네. 놀랍게도 안됩니다.😂 될 것 같은데 안되요. 왜냐면, 지금은 말그대로 연결?만 해놓은 상태이기 때문이에요. 이 상태에서 어떤 값이 바뀌었을 때, "값이 바뀌었으니 뷰를 다시 그려!"라고 하는 코드가 없는 상황인거죠. 이걸 Rx로 구현해서 설명하면 아래와 같은 느낌 입니다. 

```swift
import UIKit
import RxSwift
import RxCocoa

class ViewModel {
  let name = "User name"
  var score = 0
  
  func addScore() {
    score += 1
    scoreSubject.onNext(score)
  }
  
  var scoreSubject = PublishSubject<Int>.init()
}

class ViewController: UIViewController {
  
  @IBOutlet weak var myLabel: UILabel!
  @IBOutlet weak var myButton: UIButton!
  
  private let viewModel = ViewModel()
  var disposeBag = DisposeBag()

  override func viewDidLoad() {
    super.viewDidLoad()
    setupUI()
    bind()
  }
  
  private func setupUI() {
    self.myLabel.text = viewModel.name
    self.myButton.setTitle("\(viewModel.score)", for: .normal)
  }
  
  private func bind() {
    //Input
    myButton.rx.tap
      .withUnretained(self)
      .map(\.0)
      .subscribe(onNext: { owner in
        owner.viewModel.addScore()
      })
      .disposed(by: disposeBag)
    
    //Output
  }

}
```

그러니까, Input은 있는데 Output은 없는 그런느낌적인 느낌?인 것이죠. 그래서 이걸 바꿔줘야 해요. 여기서는 score 값에 따라서 뷰를 갱신해주어야 하므로 아까 말했던 ObservableObject의 objectWillChange를 사용해서 아래처럼 바꿔줍니다. 

```swift
import SwiftUI

class ViewModel: ObservableObject {
  let name = "User name"
  var score = 0{
    didSet {
      objectWillChange.send()
    }
  }
}

struct ContentView: View {
  
  @ObservedObject var viewModel: ViewModel
  
  var body: some View {
    VStack(spacing: 30) {
      Text(viewModel.name)
      Button {
        self.viewModel.score += 1
      } label: {
        Text(viewModel.score.description)
      }
    }
  }
}

struct ContentView_Previews: PreviewProvider {
  static var previews: some View {
    ContentView(viewModel: ViewModel())
  }
}
```

수정된 것은 score에 didSet으로 objectWillChange.send()가 실행되도록 바꿔준 것 밖에는 없어요. 이러면 잘 실행이 됩니다. 겸사겸사 Rx예시 또한 Output을 아래처럼 바꿔주면 되겠죠. 원래 아래의 코드처럼 Rx에서 하는 subscribe/bind 그리고 Combine에서는 sink 작업을 SwiftUI에서는 "뷰 바꼈으니 다시 그리렴"하고 알아서 해주는 것이죠. 

```swift
//Output
viewModel.scoreSubject
  .map { "\($0)" }
  .bind(to: myLabel.rx.text)  
  .disposed(by: disposeBag)
```

음, 그런데 이제 여기서 아직 안했던 `@Published`가 나오는데요. 이건 그냥 그거에요. 지금은 저희가 didSet 으로 트리거를 만들어줬잖아요? 이렇게 하는 경우는 내가 트리거를 넣을 상황을 프로퍼티의 변경 시점이 아니라 특정 시점에 넣는다던지, 아니면 또는 특정 조건에만 변경을 알리고 싶다던지에 주로 사용합니다. 예를들어 score가 짝수일때만 뷰를 업데이트 하고 싶다던지 처럼요. 

```swift
class ViewModel: ObservableObject {
  let name = "User name"
  var score = 0{
    didSet {
      guard self.score % 2 == 0 else { return }
      objectWillChange.send()
    }
  }
}
```

만약에 위와 같은 경우가 아니라면, 애플쪽에서 `@Published`라는 속성을 제공해줘서 아주 간단하게 이렇게만 해주면 됩니다. 

```swift
class ViewModel: ObservableObject {
  let name = "User name"
  @Published var score = 0
}
```

별거없죠? 사실 `@Published` 내부는 정확히 모르지만, 아마 내부도 저희가 didSet했던 비스무리한? 식으로 구현이 되어있을 거에요. 그저 매번 그렇게 사용하기 번거로우니 `@Published`라는 프로퍼티래퍼 속성을  통해 보다 쉽게 사용할 수 있게 해준 거겠죠.😄

이번 글은 여기까지 하고, 다음 글에서 `@ObservedObject`와 iOS 14부터 생긴`@StateObject`에 대해서 알아보도록 하겠습니다. 


    
## Conclusion
- 작은 단위에서 뷰 내부의 상태(State)값을 관찰 할 때, `@State`속성을 사용했다면, 뷰가 더 복잡해짐에 따라 뷰 외부의 값을 관찰할 때는 `ObservableObject`를 사용한다.
- `ObservableObject`은 프로토콜이며 `AnyObject`를 채택하고 있기 때문에 구조체와 열거형에는 사용이 불가하고 참조타입인 클래스에만 사용가능하다.
- 클래스에서 `ObservableObject`라는 프로토콜을 채택해서 관찰할 수 있는 객체로 만들고, SwiftUI의 View에서 인스턴스 앞에 `@ObservedObject` 속성을 붙여줘서 사용한다.
- `ObservableObject`에는 `objectWillChange`라는 퍼블리셔를 하나 가지고 있는데, 내가 관찰하고 싶은 클래스 내의 인스턴스가 변경될 때 `objectWillChange.send()` 메소드를 호출해줘도 되지만 Swift에서 `@Published`라는 속성을 지원해주고 있기 때문에 이것을 사용하면 된다.
    
## Reference 
https://developer.apple.com/documentation/combine/observableobject          
https://developer.apple.com/documentation/swiftui/observedobject            
https://eunjin3786.tistory.com/410              
https://developer.apple.com/documentation/swiftui/managing-model-data-in-your-app               
https://nsios.tistory.com/145               



