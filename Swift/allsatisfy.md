작업을 하다보면, 특정 값들이 모두 true 조건인지 확인해야 할 때가 있습니다. 

예를들면 아래와 같은 것들이 있겠네요. 

> _1. 회원가입 화면의 login, password, password repeat 값이 모두 유효한지_           
> _2. 개인정보동의 값에서 필수/선택 값들을 유효하게 선택했는지_

Swift 에서는 이런 경우에 사용할 수 있는 [allSatisfy](https://developer.apple.com/documentation/swift/array/allsatisfy(_:)) 메소드를 지원합니다. [SE-0207](https://github.com/apple/swift-evolution/blob/master/proposals/0207-containsOnly.md) 에서 제안되었고, `Swift 4.2` 버전부터 지원하고 있어요. 생각보다 간단하고 저희가 기존과 썼던 방법과 크게 다르지 않으니 바로 살펴보겠습니다. 

> allSatisfy(_:)        
Returns a Boolean value indicating whether every element of a sequence satisfies a given predicate. 			
`iOS 8.0+` `iPadOS 8.0+` `macOS 10.10+` `Mac Catalyst 13.0+` `tvOS 9.0+` `watchOS 2.0+`
> ```swift
> func allSatisfy(_ predicate: (Self.Element) throws -> Bool) rethrows -> Bool
> ```

문서에서는 위와 같이 설명하고 있는데요. 시퀀스의 모든 요소가 주어진 조건을 만족하는지의 여부에 따라 Bool 타입을 리턴해주고 있어요. 모두 만족하면 true, 그렇지 않다면 false 를 말하는 거겠죠? 코드를 볼게요. 

지금 names 라는 시퀀스가 있고, `해당 시퀀스 내부의 값들의 길이가 모두 5 이상인 경우`를 체크한다고 해보죠. 

```swift
let names = ["Sofia", "Camilla", "Martina", "Mateo", "Nicolás"]
```

## for loop 

먼저 for문을 사용하는 경우입니다. 익숙하지만, 고차함수를 사용하면 왠지 더 축약할 수 있을 것 같아요. 

```swift
//for loop
func allHaveAtLeastFive(_ names: [String]) -> Bool {
    for name in names {
        if name.count < 5 {
            return false
        }
    }
    return true
}
```

## filter

고차함수인 filter를 사용하면 어떨까요 ? 2가지 방법을 적어봤는데, 둘 다 for문 보다는 훨씬 간결하죠. 체이닝하면서 바로 작업할 수 있구요. 

```swift
let allHaveAtLeastFiveFilter1 = names.filter({ $0.count >= 5 }).count == names.count
let allHaveAtLeastFiveFilter2 = names.filter({ $0.count < 5 }).isEmpty
```

## allSatisfy

그럼 allSatisfy 를 사용하면 어떨까요 ? filter보다도 코드의 길이가 약간 더 축약되네요. 

```swift
let allHaveAtLeastFive = names.allSatisfy({ $0.count >= 5 })
```

이쯤되면 이런 생각을 하실 수 있어요. _"아니 그럼 더 익숙한 filter를 쓰지 굳이 왜 allSatisfy를 써?"_ 라고 말이죠. 그런데, filter와 allSatisfy를 잘 보시면 묘하게 다르다는 것을 볼 수 있어요. 

filter는 해당 조건을 만족하는 시퀀스를 리턴하죠. 그래서 `allHaveAtLeastFiveFilter1` 의 방법으로는 필터를 한 번 걸고 해당 시퀀스의 count 값이 주어진 name의 count 값과 같은지를 비교하는 연산이 추가됩니다. 또 다른 `allHaveAtLeastFiveFilter2` 의 방법은 `isEmpty`라는 속성으로 더 간단하지만, 주어진 제한 조건을 반대로 `$0.count < 5` 처럼 한번 꺾어주어야 된다는 단점이 있어요. 그만큼 가독성이 떨어진다는 얘기죠. 

하지만, allSatisfy는 위에서 보신 것처럼 주어진 제한 조건을 그대로 읽을 수 있고, 코드도 더 간결하죠. 그래서 문서에 나와있는 것처럼 어떤 특정 시퀀스의 조건을 모두 만족하는 지를 판단할 때는 꽤나 유용하게 사용할 수 있어요. 


## Conclusion
- Swift에서는 시퀀스의 모든 요소가 주어진 조건을 만족하는지의 여부를 판단하는 메소드로 `allSatisfy`를 지원합니다.
- 기존의 `for loop, filter, contain` 등을 사용해도 되지만, `allSatisfy`가 `연산, 가독성, 축약` 부분에서 더 뛰어납니다.

## Reference
[https://developer.apple.com/documentation/swift/array/allsatisfy(_:)](https://developer.apple.com/documentation/swift/array/allsatisfy(_:))			
				
[https://github.com/apple/swift-evolution/blob/master/proposals/0207-containsOnly.md](https://github.com/apple/swift-evolution/blob/master/proposals/0207-containsOnly.md)			








