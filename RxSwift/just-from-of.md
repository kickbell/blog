
# Just, From, Of

테이블뷰를 Rx로 구현하던 중에 문득 궁금증이 생겨서 just, from, of를 비교해보려고 합니다.
코드가 이해가 더 빠르니 코드를 보죠. 


```swift
Observable.just(["Lisbon", "Copenhagen", "London"])
    .debug("just")
    .subscribe()
    .disposed(by: rx.disposeBag)

Observable.from(["Lisbon", "Copenhagen", "London"])
    .debug("from")
    .subscribe()
    .disposed(by: rx.disposeBag)

Observable.of(["Lisbon", "Copenhagen", "London"])
    .debug("of_array")
    .subscribe()
    .disposed(by: rx.disposeBag)

Observable.of("Lisbon", "Copenhagen", "London")
    .debug("of_")
    .subscribe()
    .disposed(by: rx.disposeBag)
```

## Debug
```
2022-06-30 16:12:41.526: just -> subscribed
2022-06-30 16:12:41.532: just -> Event next(["Lisbon", "Copenhagen", "London"])
2022-06-30 16:12:41.533: just -> Event completed
2022-06-30 16:12:41.533: just -> isDisposed

2022-06-30 16:12:41.534: from -> subscribed
2022-06-30 16:12:41.537: from -> Event next(Lisbon)
2022-06-30 16:12:41.538: from -> Event next(Copenhagen)
2022-06-30 16:12:41.538: from -> Event next(London)
2022-06-30 16:12:41.538: from -> Event completed
2022-06-30 16:12:41.538: from -> isDisposed

2022-06-30 16:12:41.538: of_array -> subscribed
2022-06-30 16:12:41.539: of_array -> Event next(["Lisbon", "Copenhagen", "London"])
2022-06-30 16:12:41.539: of_array -> Event completed
2022-06-30 16:12:41.540: of_array -> isDisposed

2022-06-30 16:12:41.540: of_ -> subscribed
2022-06-30 16:12:41.540: of_ -> Event next(Lisbon)
2022-06-30 16:12:41.541: of_ -> Event next(Copenhagen)
2022-06-30 16:12:41.541: of_ -> Event next(London)
2022-06-30 16:12:41.541: of_ -> Event completed
2022-06-30 16:12:41.541: of_ -> isDisposed

```

결과를 보면, 아래와 같습니다. (매개변수로 []이 들어갔는지 아닌지를 잘 보셔야 해요.) 
기억만 잘 해두면 될 것 같아요. 그럼 왜 같을까요? 

```swift
//둘 다 배열
just(["Lisbon", "Copenhagen", "London"]) == of(["Lisbon", "Copenhagen", "London"]) 

//from만 배열, of는 가변 파라미터 
from(["Lisbon", "Copenhagen", "London"]) == of("Lisbon", "Copenhagen", "London")
```

just와 from 과는 다르게 of는 `Variadic Parameters(가변 매개변수)`로 구현되어 있어요. 그래서 배열을 넣을 수도 있고, String의 연속된 값을 넣을 수도 있는 것이죠. 

```swift
 public static func of(_ elements: Element ..., scheduler: ImmediateSchedulerType = CurrentThreadScheduler.instance) -> Observable<Element> {
	 ObservableSequence(elements: elements, scheduler: scheduler)
 }
```

사실 애초에 이 글을 쓴 이유도 아래의 코드 때문이었어요. 이게 왜 둘 다 되지? 하는 의문에 말이죠. 

```swift
Observable.of(["Lisbon", "Copenhagen", "London"])
    .bind(to: tableView.rx.items(cellIdentifier: "cell", cellType: UITableViewCell.self)) { (row, element, cell) in
        cell.textLabel?.text = "\(element) @ row \(row)"
    }
    .disposed(by: rx.disposeBag)

Observable.of("Lisbon", "Copenhagen", "London")
    .bind(to: tableView.rx.items(cellIdentifier: "cell", cellType: UITableViewCell.self)) { (row, element, cell) in
        cell.textLabel?.text = "\(element) @ row \(row)"
    }
    .disposed(by: rx.disposeBag)
```

~~그래서 코드에 들어가보니 `bind(to:)` 라는 녀석이 아래와 같이 구현되어 있어서 가능했더라구요😀. 
~~
가 아니라 모르겠는데 여기는.. 시퀀스 프로토콜? 이라서 그런가.?? 

```swift
public func bind<Observer: ObserverType>(to observers: Observer...) -> Disposable where Observer.Element == Element {
    self.subscribe { event in
        observers.forEach { $0.on(event) }
    }
}
```

무슨말이냐면, `Observer...` 타입을 파라미터로 받기 때문에 위에서 내려오는 값에 따라 

              public func bind<Observer: ObserverType>(to observers: Observer...) -> Disposable where Observer.Element == Element {
                  self.subscribe { event in
                      observers.forEach { $0.on(event) }
                  }
              }



## Conclusion
- 앱의 아이콘을 동적으로 바꿀 수 있습니다. 주로 `구독형 유료회원 결제`, `30일/100일/300일 연속출석회원`, `특정 아이템 구매회원` 과 같은 마케팅에 활용할 수 있다.  
- 앱에서는 기본 아이콘 값인 `Primary App Icon` 값과 대체할 수 있는 아이콘들의 값인 `Alternate App Icon` 이 있다.
- 2x, 3x을 규칙을 따르는 첫 번째 방법도 있지만, 공식 문서에 나오는 두 번째 방법을 사용하도록 하자. 
- `UIApplication.shared.setAlternateIconName`에 사용되는`Primary App Icon`을 가져올 수 있는 기본 값은 `nil`이다. 
- 자세한 설명과 샘플코드가 제공되는 공식문서의 링크는 [여기](https://developer.apple.com/documentation/xcode/asset_management/configuring_your_app_to_use_alternate_app_icons)이다.





