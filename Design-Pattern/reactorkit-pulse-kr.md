---
layout: post
title: "[ReactorKit/KR] ReactorKit - Pulse"
tags: [ReactorKit, Pulse, RxSwift]
comments: true
---

오늘은 ReactorKit의 Pulse라는 기능에 대해서 알아볼겁니다. 

3.1.0 버전에서 추가되었고, 현재(2022.04.08)기준 제일 최신 버전인 3.2.0 버전에서 일부 수정되었습니다. 

> 3.1.0             
...
Introduce Pulse 📡 (@tokijh)            

> 3.2.0 Latest      
...     
Make public valueUpdatedCount on Pulse by @tokijh in #196           

사실, 현재 사내 프로젝트에 Pulse가 사용되고 있는데, 이게 무슨 의미인지 명확히 모르겠어서 이 글을 쓰게 되기도 한 건데요. 하나씩 차근차근 알아보면 될 것 같습니다. 😁


일단, 문서를 봐볼까요 ?  

[공식문서](https://github.com/ReactorKit/ReactorKit#pulse)에는 Pulse를 이렇게 소개하고 있어요. 

> Pulse has diff only when mutated To explain in code, the results are as follows.

음... 그렇구나. 이해가 잘 안되었습니다. 

"개발자는 역시 코드지, 코드를 봐볼까?"

```swift
  var messagePulse: Pulse<String?> = Pulse(wrappedValue: "Hello tokijh")

  let oldMessagePulse: Pulse<String?> = message
  message = "Hello tokijh"

  oldMessagePulse != messagePulse // true
  oldMessagePulse.value == messagePulse.value // true
```

음... 뭐지? "[distinctUntilChanged](https://reactivex.io/documentation/operators/distinct.html) 같은 녀석인가?" 싶었죠. 

"음, 그러면 일단 코드를 복붙에서 실행해보자!" 라는 생각이 들어서 그대로 코드를 가져와서 실행해보았습니다. 

<img width="1088" alt="image" src="https://user-images.githubusercontent.com/85085822/162379923-b3ae5b31-be7c-4bda-8ed8-91b643448ea6.png">


음 에러가 나는군요...( 에러가 나지않는 수정된 코드는 마지막에 있습니다. )

그렇다면 별 수 없이 다음 문서 내용을 보겠습니다.  

```swift
  // Reactor
  private final class MyReactor: Reactor {
    struct State {
      @Pulse var alertMessage: String?
    }

    func mutate(action: Action) -> Observable<Mutation> {
      switch action {
      case let .alert(message):
        return Observable.just(Mutation.setAlertMessage(message))
      }
    }

    func reduce(state: State, mutation: Mutation) -> State {
      var newState = state

      switch mutation {
      case let .setAlertMessage(alertMessage):
        newState.alertMessage = alertMessage
      }

      return newState
    }
  }

  // View
  reactor.pulse(\.$alertMessage)
    .compactMap { $0 } // filter nil
    .subscribe(onNext: { [weak self] (message: String) in
      self?.showAlert(message)
    })
    .disposed(by: disposeBag)

  // Cases
  reactor.action.onNext(.alert("Hello"))  // showAlert() is called with `Hello`
  reactor.action.onNext(.alert("Hello"))  // showAlert() is called with `Hello`
  reactor.action.onNext(.doSomeAction)    // showAlert() is not called
  reactor.action.onNext(.alert("Hello"))  // showAlert() is called with `Hello`
  reactor.action.onNext(.alert("tokijh")) // showAlert() is called with `tokijh`
  reactor.action.onNext(.doSomeAction)    // showAlert() is not called
```


음, 밑에 Cases를 보니 정확히는 뭔지 모르겠어도 일단 [distinctUntilChanged](https://reactivex.io/documentation/operators/distinct.html) 같은? 비슷한? 녀석은 맞는 것 같더라구요. 그런데, 아직도 확실히 이해는 잘 안갔습니다. 그래서 코드를 뜯어보기로 했죠.(마침 짧기도 해서..^^;)

```swift
//
//  Pulse.swift
//  ReactorKit
//
//  Created by tokijh on 2021/01/11.
//

@propertyWrapper
public struct Pulse<Value> {

  public var value: Value {
    didSet {
      self.riseValueUpdatedCount()
    }
  }
  public internal(set) var valueUpdatedCount = UInt.min

  public init(wrappedValue: Value) {
    self.value = wrappedValue
  }

  public var wrappedValue: Value {
    get { return self.value }
    set { self.value = newValue }
  }

  public var projectedValue: Pulse<Value> {
    return self
  }

  private mutating func riseValueUpdatedCount() {
    self.valueUpdatedCount &+= 1
  }
}
```

Pulse의 코드는 위와 같습니다. 제네릭 구조체에 `PropertyWrapper` 특성을 가지고 있습니다. `PropertyWrapper` 에 대해서 더 알고싶으시면 [공식문서](https://github.com/apple/swift-evolution/blob/master/proposals/0258-property-wrappers.md)에서 보시면 될 것 같아요. 

사실, 처음에는 저도 이해가 잘안갔는데 중요한 부분은 `var value` 의 `didSet` 부분입니다. 
value 값이 바뀔때마다 어떤 특정 작업을 해주고 있죠. 그 작업은 아래와 같구요. 

```swift
  private mutating func riseValueUpdatedCount() {
    self.valueUpdatedCount &+= 1
  }
```

value값이 바뀔 때마다 valueUpdatedCount라는 count값을 +1 해주고 있습니다. 그리고 만약에 valueUpdatedCount의 값이 UInt.max라면 valueUpdatedCount값에 다시 UInt.min 을 할당해주고 있구요. 여기서는 일단 이 정도만 파악하면 충분합니다. 다음으로 넘어가볼까요? 


```swift
//
//  Reactor+Pulse.swift
//  ReactorKit
//
//  Created by 윤중현 on 2021/03/31.
//

extension Reactor {
  public func pulse<Result>(_ transformToPulse: @escaping (State) throws -> Pulse<Result>) -> Observable<Result> {
    return self.state.map(transformToPulse).distinctUntilChanged(\.valueUpdatedCount).map(\.value)
  }
}
```

위 코드를 보면, Reactor에 extension으로 func pulse 라는 메소드를 추가했습니다. 내부 구현을 볼까요 ? 아까도 말했다시피 `distinctUntilChanged`가 나오긴 하네요. 제 짐작이 약간?은 맞은 듯 합니다. 😅  그리고 해당 `distinctUntilChanged` 연산자는 RxSwift에서 지원하는 4가지 중에 keySelector를 매개변수로 받는 녀석입니다. 

```swift
  public func distinctUntilChanged<Key: Equatable>(_ keySelector: @escaping (Element) throws -> Key)
      -> Observable<Element> {
      self.distinctUntilChanged(keySelector, comparer: { $0 == $1 })
  }
```

보통 사용은 아래와 같이 하죠. 

```swift
  struct Human {
    let name: String
    let age: Int
  }

  let myPublishSubject = PublishSubject<Human>.init()

  myPublishSubject
    .distinctUntilChanged(\.name)
    .debug()
    .subscribe()
    .disposed(by: disposeBag)

  myPublishSubject.onNext(Human(name: "a", age: 1))
  myPublishSubject.onNext(Human(name: "a", age: 2))
  myPublishSubject.onNext(Human(name: "c", age: 3))

  //-> subscribed
  //-> Event next(Human(name: "a", age: 1))
  //-> Event next(Human(name: "c", age: 3))
```

즉, 여기서 정리해보면 ***`Pulse`는 이벤트을 방출하기는 하되, `Pulse` 내부에 선언되어있는 변수 `valueUpdatedCount` 값이 바뀌어야만 이벤트를 방출한다*** 는 겁니다.

그럼 `valueUpdatedCount` 값이 언제 바뀔까요 ? 바로 위에 말했던 것처럼 value 값이 바뀔 때 입니다.

ReactorKit 공식문서에서는 아래와 같이 추가적인 설명과 예시를 들어주고 있어요. 

> Use when you want to receive an event only if the new value is assigned, even if it is the same value. like alertMessage (See follows or PulseTests.swift)

여기서 가장 중요한 부분은 `if the new value is assigned` 겠죠. 즉, ***일반적으로 알고있는 같은 이벤트가 방출되면 걸러주는 `distinctUntilChanged` 와 달리 여기서는 그 조건이 `value` 값이 바뀌어서 `valueUpdatedCount` 값이 다르지 않으면 이벤트를 방출하지 않는다***는 겁니다. 

추가적으로 제공해주는 [예시](https://github.com/ReactorKit/ReactorKit/blob/master/Tests/ReactorKitTests/PulseTests.swift)를 볼까요 ? 

```swift
import XCTest
import RxSwift
@testable import ReactorKit

final class PulseTests: XCTestCase {
  func testRiseValueUpdatedCountWhenSetNewValue() {
    // given
    struct State {
      @Pulse var value: Int = 0
    }

    var state = State()

    // when & then
    XCTAssertEqual(state.$value.valueUpdatedCount, 0)
    state.value = 10
    XCTAssertEqual(state.$value.valueUpdatedCount, 1)
    XCTAssertEqual(state.$value.valueUpdatedCount, 1) // same count because no new values are assigned.
    state.value = 20
    XCTAssertEqual(state.$value.valueUpdatedCount, 2)
    state.value = 20
    XCTAssertEqual(state.$value.valueUpdatedCount, 3)
    state.value = 20
    XCTAssertEqual(state.$value.valueUpdatedCount, 4)
    XCTAssertEqual(state.$value.valueUpdatedCount, 4) // same count because no new values are assigned.
    state.value = 30
    XCTAssertEqual(state.$value.valueUpdatedCount, 5)
    state.value = 30
    XCTAssertEqual(state.$value.valueUpdatedCount, 6)
  }
```

테스트를 보면 친절히 주석이 적혀있어요. 주석을 보면 `// same count because no new values are assigned.` 라고 되어있죠. 

***즉, `state.value = 2` 와 같은 식으로 value 값에 새로운 값을 할당하지 않았기 때문에 `valueUpdatedCount` 값은 증가되지 않았고, 결과적으로 Pulse는 이벤트를 방출하지 않습니다.*** 

그러면 Pulse, 사용은 어떻게 해야 할까요 ? 역시나 또 친절하게 문서에 나와있는 것처럼, State에 `@Pulse` 특성을 붙여주고 func bind(reactor:) 내부에서 `reactor.pulse(\.$alertMessage)` 같은 식으로 가져오면 됩니다. 


```swift
  struct State {
    @Pulse var alertMessage: String?
  }

  // View
  reactor.pulse(\.$alertMessage)
    .compactMap { $0 } // filter nil
    .subscribe(onNext: { [weak self] (message: String) in
      self?.showAlert(message)
    })
    .disposed(by: disposeBag)
```

결론적으로는 아까 위의 공식문서가 아래와 같이 일부 수정되어야겠죠?

```swift
  var messagePulse: Pulse<String?> = Pulse(wrappedValue: "Hello tokijh")

  let oldMessagePulse: Pulse<String?> = messagePulse
  messagePulse.value = "Hello tokijh" // add valueUpdatedCount +1

  oldMessagePulse.valueUpdatedCount != messagePulse.valueUpdatedCount // true
  oldMessagePulse.value == messagePulse.value // true
```

oldMessagePulse에 messagePulse를 넣고, messagePulse의 value에 새로운 값을 할당합니다. 

그러면, oldMessagePulse와 messagePulse의 `value`는 같지만, value가 할당되면서 `valueUpdatedCount가 +1` 되었으므로 oldMessagePulse와 messagePulse의 `valueUpdatedCount`는 같지 않죠. 

이상으로 Pulse에 대해서 알아봤습니다. 사용하는데 무슨 의미인지를 몰라서 좀 헷갈렸는데, 이 글을 보시는 분들이 도움이 되셨으면 좋겠네요. 😊

감사합니다. 



