---
layout: post
title: "[SwiftUI] @ObservedObject vs @StateObject"
tags: [SwiftUI, ObservedObject, StateObject] 
comments: true
---

오늘 글에서는 [`@ObservedObject`](https://developer.apple.com/documentation/swiftui/observedobject) 와 [`@StateObject`](https://developer.apple.com/documentation/swiftui/stateobject)에 대해 알아보려고 합니다.

사실, 처음엔 좀 헷갈렸어요. 그래서 공식문서도 보고 이것저것 찾아보다가 어느 한 블로그에서 좋은 글을 보게 되서 겸사겸사 정리하려고 합니다. 

일단 여느 때와 같이 공식문서로 시작해볼까요 ? 정의는 아래와 같습니다. 
( 아 그리고 공식문서 홈페이지 리뉴얼 됐던데, 너무 보기 예뻐졌습니다. 아주 굳굳 😄. )

> [ObservedObject](https://developer.apple.com/documentation/swiftui/observedobject)            
A property wrapper type that subscribes to an observable object and invalidates a view whenever the observable object changes.          
iOS 13.0+           

> [StateObject](https://developer.apple.com/documentation/swiftui/stateobject)              
A property wrapper type that instantiates an observable object.             
iOS 14.0+               

일단 ObservedObject부터 보면, iOS 13에 나왔고, 프로퍼티 래퍼 타입이네요. 관찰 가능한 객체를 구독하고 해당 객체가 변경될때마다 View를 invalidates(무효화) 한다고 해요. ObservedObject에 대해서는 [저번 글](https://io3s.github.io/ObservableObject/)에서도 공부를 했으니 혹시 모르신다면 보고 오시는 것을 추천드립니다. 그래서, 저는 사실 저 부분이 처음엔 잘 이해되지 않았어요. View를 invalidates(무효화)한다라... 기존의 저희에게 익숙한 Rx에서는 이벤트를 `방출`한다고 하잖아요? 그럼 그냥 이벤트를 `방출`하면 되는거지 왜 `무효화` 한다는 걸까? 하고 말이죠. 저번 글에서는 그냥저냥 넘어갔었는데, 이번에 공부하면서 확실히 알게 되었습니다. 

View를 `무효화`한다는 말은 그런 거에요. SwiftUI의 View들은 struct이죠 ? 즉 값타입 입니다. 그리고 하나의 함수와도 같아요. input값이 있으면 그에 따라서 뷰를 매번 새로 그리죠. 물론 매번 새롭게 뷰를 그리는 것은 꽤나 큰 리소스가 드는 일이기 때문에, SwiftUI에서는 뷰를 매번 그리는 것을 피하기 위해서 최적화가 되어 있다고 합니다. 

예를 들어서 아래와 같은 코드가 있을 수 있겠죠? 버튼을 탭하면 number의 값이 1씩 증가하는 간단한 코드에요. 

```swift
struct MyView: View {
    
    let text = ""Hello Jace""
    
    @State var number = 0
    
    var body: some View {
        Button {
            self.number += 1
        } label: {
            Text(text)
        }
    }
}
``` 

하지만, 뷰를 그리는데 있어서 실질적으로 사용되는 값은 고정값인 text 입니다. 즉 number의 증가여부는 뷰를 다시 그리는데는 관계가 없는거죠. 이럴 때 number값이 변할 때마다 뷰를 새롭게 그려주는 것은 리소스 낭비겠죠? 이럴 때는 뷰를 다시 그리지 않아도 되는 겁니다. 이것에 관한 글은 나중에 다시 써보도록 하겠습니다. 

다시 돌아와서, View를 `무효화`한다는 말은 View를 `삭제한다.` 또는 `지운다.`같은 의미로 이해하면 되지 않을까 싶습니다. ObservedObject가 관찰하고 있는 객체가 변경될 때마다 View를 `무효화`하면, SwiftUI 프레임워크에서 "OK, 값이 변경되었다고? 알았어. 니가 View를 무효화하면 내가 새롭게 변경된 값으로 뷰를 다시 그릴게. 😀" 같은 느낌인거죠. 

그럼, StateObject는 뭘까요? 문서에서는 "관찰가능한 객체를 인스턴스화 하는 프로퍼티 래퍼 타입이다." 라고 정의하고 있죠. 음?.. 무슨소리? 넵 이게 정상입니다. 이건 말로 설명하기 힘들어요. 코드로 볼게요. 


```swift
import SwiftUI

final class CounterViewModel: ObservableObject {
    private(set) var count = 0

    func incrementCounter() {
        count += 1
        objectWillChange.send()
    }
}

struct CounterView: View {

    @ObservedObject var viewModel = CounterViewModel()

    var body: some View {
        VStack {
            Text("Count is: \(viewModel.count)")
            Button("Increment Count") {
                viewModel.incrementCounter()
            }
        }
    }
}

struct ContentView_Previews: PreviewProvider {
  static var previews: some View {
      CounterView()
  }
}
``` 
![](https://velog.velcdn.com/images/dev_kickbell/post/ff53ee54-bf7d-4a86-b583-55bef75305f9/image.gif)
일단, ObservedObject와 StateObject 둘 다 [저번 글](https://io3s.github.io/ObservableObject/)에서 배웠던 ObservableObject과 같이 사용해야 하는 것 똑같습니다. CounterViewModel이 있고, ObservableObject 프로토콜을 준수해서 CounterViewModel을 관찰가능한 객체로 지정합니다. 그리고 count라는 변수가 있으며, incrementCounter() 할 때마다 count가 1씩 증가하고 objectWillChange.send()를 해서 CounterViewModel을 관찰하는 객체에게 값이 변했다고 알려주지요. 

관찰하는 쪽인 CounterView에서는 CounterViewModel을 관찰하기 위해 @ObservedObject 속성을 지정하고, CounterViewModel의 count를 참조해서 뷰로 그려주고 있으며, 버튼을 누를 때마다 count값을 증가시켜줍니다. 

간단한 예제입니다. 이게 그러면 여기서 viewModel의 속성을 @StateObject로 바꾸면 어떻게 될까요 ? 넵, 아무 일도 일어나지 않습니다. 여기서는 차이를 알 수 없어요. 예제 코드를 조금 수정해보죠. 


```swift
import SwiftUI

final class CounterViewModel: ObservableObject {
    private(set) var count = 0

    func incrementCounter() {
        count += 1
        objectWillChange.send()
    }
}

struct CounterView: View {

    @ObservedObject var viewModel = CounterViewModel()
//    @StateObject var viewModel = CounterViewModel()

    var body: some View {
        VStack {
            Text("Count is: \(viewModel.count)")
            Button("Increment Count") {
                viewModel.incrementCounter()
            }
        }
    }
}

struct RandomNumberView: View {

    @State var randomNumber = 0

    var body: some View {
        VStack {
            Text("Current Random is \(randomNumber)")
            Button {
                randomNumber = (0..<100).randomElement()!
            } label: {
                Text("Generator Random")
            }
            .padding(.bottom)

            CounterView()
        }
    }
}


struct ContentView_Previews: PreviewProvider {
  static var previews: some View {
      RandomNumberView()
  }
}
``` 

수정된 부분을 말씀드릴게요. 

1. 일단 RandomNumberView 가 생겼습니다. RandomNumberView는 그저 버튼을 탭하면 0..<100 사이의 숫자를 생성해서 뷰를 그려주는 간단한 뷰입니다. 
2. 또하나 중요한 점은 RandomNumberView 내에 CountView()가 하위뷰로 들어가있다는 점입니다. 

나머지는 변경된 것이 없어요. 자, 그럼 이렇게 한 상태에서 viewModel을 @ObservedObject로 하는 것과 @StateObject로 하는 것의 차이를 볼까요 ? 각각 Random 버튼을 3번 누르고 Count 버튼을 3번 눌러볼게요. 


> @ObservedObject var viewModel = CounterViewModel()		
![](https://velog.velcdn.com/images/dev_kickbell/post/56c518e8-f1fa-4723-b95f-1a9df98b6ce0/image.gif)

> @StateObject var viewModel = CounterViewModel()		
![](https://velog.velcdn.com/images/dev_kickbell/post/9d8af14c-4e69-4fd0-a312-48a639144841/image.gif)


어때요 ? 차이점을 찾으셨나요 ? 😀 

네, 차이점은 `Count is: 의 값이 0으로 초기화가 되느냐 안되느냐`의 차이 입니다. 이거 왜 이럴까요 ? 

자 다시 코드를 봐보고, 요점만 다시 정리해볼게요. 핵심은 아래와 같습니다. 

- SwiftUI는 input값에 따라서 뷰를 새로 그립니다. 
- ObservedObject는 해당 객체가 변경될때마다 View를 invalidates(무효화) 한다.
- StateObject는 관찰가능한 객체를 인스턴스화 한다. 
- RandomNumberView 내에 CountView()가 하위뷰이다. 

그리고, 지금 위에 @ObservedObject 상태의 코드대로라면 flow가 이렇게 될 거에요. 

1. 랜덤버튼을 3번 누른다. 
2. 0..<100의 난수 생성 Current Random is 변경. 
3. 카운트버튼을 3번 누른다. 
4. viewModel.incrementCounter() 3회 실행 1,2,3으로 값 방출. Count is 변경.
5. 다시 랜덤버튼을 누른다.        

그리고 여기서 부터 중요합니다.      

- var body안에서 뷰를 새로 그리는데, 하위 뷰로 CounterView() 가 있다. 그러면 여기서 `뷰가 새로 그려지면서 CounterView()의 CounterViewModel의 count = 0이 할당되면서 뷰가 새로 그려진다.` 라는 것이죠. 

그러면 CountView의 viewModel이 @StateObject 라면 어떻게 되죠 ? 

- 똑같이 뷰가 새로 그려집니다. 하지만, @StateObject가 뭐죠? @StateObject는 `관찰가능한 객체를 인스턴스화`합니다. 즉, 뷰가 새로 그려질 때 뷰가 무효화되고 다시 인스턴스화 되지 않습니다. 그래서 기존의 count 값이 유지 되는 것이죠. 이해가 가실까요 ? 여기서의 주요한 점은 CounterView가 RandomNumberView의 하위뷰로 있기 때문에 RandomNumberView가 새로 그려질 때, CounterView도 새로 그려진다는 겁니다.


그러면, 각각 언제 @ObservedObject, @StateObject를 써야 할까요 ? 이것에 관해서는 애플의 포럼에서 답변한 [오피셜 문서](https://developer.apple.com/forums/thread/650776)가 있습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/e3a7a7a3-2584-4a1f-a978-43668f067517/image.png)

즉, @ObservedObject는 관찰 중인 객체에서 변경 사항이 감지되면 뷰를 업데이트 한다는 목적으로 사용한다. 물론 @StateObject도 같은 용도이지만, 둘의 차이는 위에서 말한 것처럼 @StateObject는 포함되는 View의 구조체가 scope에 포함되더라도 파괴되고 다시 인스턴스화 되지 않는다는 것이죠. 이 말을 다르게 표현하면 @ObservedObject는 포함되는 View의 cycle에 의존적이지만, @StateObject는 그렇지 않다는 겁니다. 

그래서, 이런저런 자료들을 보면 일관적으로 하는 말이 `SwiftUI는 언제든지 뷰를 생성하거나 다시 생성할 수 있기 때문에 뷰 내부에 @ObservedObject를 생성하는 것은 안전하지 않습니다. @ObservedObject를 종속성으로 삽입하지 않는 한 @StateObject 래퍼를 사용하여 뷰를 다시 그린 후 일관된 결과를 보장하는 것이 좋다.` 라고 합니다. 

즉, 이걸 쉽게 표현하면 `왠간하면 일관된 결과가 보장된 @StateObject을 쓰되 해당 View에서 종속성으로 값을 삽입하는 경우에만 @ObservedObject를 사용해라`가 되겠습니다. 추가적으로 이와 관련된 애플의 [아티클](https://developer.apple.com/documentation/swiftui/managing-model-data-in-your-app)도 있으니 첨부해놓도록 할게요. 😄


## Conclusion
- @ObservedObject, @StateObject는 모두 관찰 중인 객체에서 변경 사항이 감지되면 뷰를 업데이트 한다는 목적으로 사용한다.
- @ObservedObject는 변경사항이 감지되면, View를 무효화(삭제)하고 변경을 알린다. 그러면 SwiftUI 프레임워크는 변경된 값으로 View를 새로 그린다. 
- 반면에, @StateObject는 관찰객체를 처음에 이미 인스턴스화 했기 때문에 포함되는 View의 구조체가 변경되더라도 무효화되고 다시 인스턴스화 되지 않는다. 
- 결론적으로, @ObservedObject는 포함되는 View의 cycle에 의존적이지만, @StateObject는 그렇지 않다. 따라서 SwiftUI는 언제든지 뷰를 생성하거나 다시 생성할 수 있기 때문에 뷰 내부에 @ObservedObject를 생성하는 것은 안전하지 않습니다. @ObservedObject를 종속성으로 삽입하지 않는 한 @StateObject 래퍼를 사용하여 뷰를 다시 그린 후 일관된 결과를 보장하는 것이 좋다. 그래서 왠간하면 일관된 결과가 보장된 @StateObject을 쓰되 해당 View에서 종속성으로 값을 삽입하는 경우에만 @ObservedObject를 사용해라. ( @StateObject는 iOS 14 부터 사용이 가능합니다. )

    
## Reference 
https://developer.apple.com/documentation/swiftui/observedobject    
https://developer.apple.com/documentation/swiftui/stateobject     
https://developer.apple.com/forums/thread/650776        
https://developer.apple.com/documentation/swiftui/managing-model-data-in-your-app       
https://www.avanderlee.com/swiftui/stateobject-observedobject-differences/      
https://nsios.tistory.com/120       


