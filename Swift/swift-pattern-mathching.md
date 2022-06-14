---
layout: post
title: "[Swift] Swift Pattern Mathching"
tags: [sample post, readability, test, intro]
comments: true
---


업무 중에 자주 헷갈려서 적어 놓습니다 :).. 

`swift의 pattern` 중에 하나인데요. [공식 문서](https://docs.swift.org/swift-book/ReferenceManual/Patterns.html#grammar_expression-pattern)는 여기에 있습니다. 

```swift
//옵셔널 값이 있을 때
let someOptional: Int? = 42

//이렇게 바인딩이 가능합니다. 
if case .some(let x) = someOptional {
  print(x)
}

//왜 ? 
//옵셔널이 아래와 같이 구현되어 있기 때문이죠. 
enum Optional<Wrapped> : ExpressibleByNilLiteral {
   case none
   case some(Wrapped)
}
```

```swift
//이런식으로도 바인딩이 가능한데요. 
if case let x? = someOptional {
  print(x) //42
}

//이것과는 뭐가 다를까요 ? 
//사실 이것과는 다를게 없습니다. 
if let x = someOptional {
  print(x) //42
}

//그런데, 아래와 같은 for 문에서의 case? 문법이 지원되지요. 
let arrayOfOptionalInts: [Int?] = [nil, 2, 3, nil, 5]
// Match only non-nil values.
for case let number? in arrayOfOptionalInts {
  print("Found a \(number)") 
  //Found a 2
  //Found a 3
  //Found a 5
}

//위의 문법을 사용하지 않는다면 이런식으로 작업해야겠죠. 
for number in arrayOfOptionalInts.compactMap({ $0 }) {
  print("Found a \(number)")
}
```

또, 자주 보는 패턴 중에 `if case`도 있는데요. 

```swift
enum Fruits {
  case melon
  case watermelon
}

let myEnum = Fruits.melon

//이런식으로 switch 문으로 melon, watermelon 2개를 다 분기하지 않고, 
//하나만 가져와서 해당 케이스가 맞는지 매칭하는 방법이 있습니다. 
if case .melon = myEnum {
  print("melon")
} else {
  print("not melon")
}

//응용은 이렇게 할 수 있는데요. 
//아래와 같이 Action이라는 enum이 있고, 그 매개변수인 action이 .updateQuery이면 true를 리턴해주는 식입니다. 
extension GitHubSearchViewReactor.Action {
  static func isUpdateQueryAction(_ action: GitHubSearchViewReactor.Action) -> Bool {
    if case .updateQuery = action {
      return true
    } else {
      return false
    }
  }
}

//그리고 그것을 아래처럼 filter 고차함수에 넣는식으로 해서 코드를 간결하게 작업할 수 있습니다. 
self.search(query: self.currentState.query, page: page)
  .take(until: self.action.filter(Action.isUpdateQueryAction))
  .map { Mutation.appendRepos($0, nextPage: $1) },
```

또, 엄~청 많이쓰는 `case let`도 있습니다. 조건은 연관값이 있을 때 사용할 수 있어요. 

```swift
enum SportsPlayer {
  case baseball(String)
  case football(String)
  case tennis(Double)
}

let car = SportsPlayer.football("messi")

switch car {
case let .football(name): //이렇게 사용해도 되고
  print(name)
case .baseball(let name): //이렇게 써도 되고
  print(name)
case .tennis: //아예 연관값을 생략할 수도 있습니다. 
  print("no name..")
}
```
