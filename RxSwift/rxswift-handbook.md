
# RxSwift 핸드북

## 1. RxSwift


### Observable

옵저버블은 어떤 데이터를 내보내야할지가 이미 정해진 스트림. 외부에서 데이터를 주입시켜줄 수 없기 때문에 구독만 가능하다.

### Subject

서브젝트는 외부에서 동적으로 데이터를 주입시켜줄 수 있는 스트림. 옵저버 이면서 옵저버블(구독)도 가능한 양방향성을 가진 애다.



## 2. RxCocoa 

> swift 요소들을 uikit에 적용시킨 녀석이다. 
myLabel.rx 라고 치면 제공되는 애들인데 리턴값은 Reactive<UILabel> 
  { get set } 이다. 
지금 사용한 값이 UILabel이므로 text라는 속성을 가지고 있는데, 얘는 타입이 Binder<String?>이다. 
myLabel.rx.text //Binder<String?> 
그리고 바인더는 bind할 수 있다. 

  
  
### Subcribe vs bind 
  
  subcribe 보다 bind가 좋은 이유는 3가지이다. 
  1. 코드가 짧아진다. 
  2. 순환참조 문제가 해결된다. 
  3. 가변 매개변수(Variadic Parameters)를 제공한다. 
  
  
```swift
        Observable.from(["Apple", "Melon", "Grape", "Banana"])
            .subscribe(onNext: { [weak self] in
                self?.myLabel.rx.text = $0
            })
            .disposed(by: disposeBag)
        
        Observable.from(["Apple", "Melon", "Grape", "Banana"])
            .bind(to: myLabel.rx.text)
            .disposed(by: disposeBag)
  
//1번은 그냥 바인더라서 된다 정도로 이해하자. 다음에 보고. 
  
  
//2번 근거 
        public func bind<Object: AnyObject>(
            with object: Object,
            onNext: @escaping (Object, Element) -> Void
        ) -> Disposable {
            self.subscribe(onNext: { [weak object] in
                guard let object = object else { return }
                onNext(object, $0)
            },
            onError: { error in
                rxFatalErrorInDebug("Binding error: \(error)")
            })
        }  
  
//3번 근거  
    public func bind<Observer: ObserverType>(to observers: Observer...) -> Disposable where Observer.Element == Element {
        self.subscribe { event in
            observers.forEach { $0.on(event) }
        }
    }
```


   
## 3. RxRelay 

옵저버블 중에 UI용이 있는데, UI전용 옵저버블은 RxCocoa에서 제공하는 Driver이고, UI전용 서브젝트는 RxRelay에서 제공하는 Relay이다. 

### UI 작업의 특징 
- 항상 메인쓰레드에서 돌아가야 한다. 
- 에러가 나더라도 절대로 스트림이 끊어지면 안된다. (앱이 동작을 안하는거니까)

### Relay 특징 
- 원본 소스 코드를 보면 서브젝트를 랩핑해서 만들어진 것을 볼 수 있다. 얘는 completed, .error이 없다. 오로지 next만 있다. 
- 그리고 뜻그대로 받아들이는것밖엔 할 수 없으니 accept라고 부른다. 
- 연산프로퍼티로 구현되어있고 내부에 onNext만 있는 것을 볼 수 있다. 
  
  
```swift
import RxSwift

/// PublishRelay is a wrapper for `PublishSubject`.
///
/// Unlike `PublishSubject` it can't terminate with error or completed.
public final class PublishRelay<Element>: ObservableType {
    private let subject: PublishSubject<Element>
    
    // Accepts `event` and emits it to subscribers
    public func accept(_ event: Element) {
        self.subject.onNext(event)
    }
    
    /// Initializes with internal empty subject.
    public init() {
        self.subject = PublishSubject()
    }

    /// Subscribes observer
    public func subscribe<Observer: ObserverType>(_ observer: Observer) -> Disposable where Observer.Element == Element {
        self.subject.subscribe(observer)
    }
    
    /// - returns: Canonical interface for push style sequence
    public func asObservable() -> Observable<Element> {
        self.subject.asObservable()
    }
}
```

### 헷갈릴 수 있는 또하나의 특징 
  - 쉽게 헷갈릴 수 있는 또하나의 특징이 있다. 
  - 위에서 본 것처럼 relay는 서브젝트를 랩핑한 것 뿐이다. 
  - 그러면 accept는 알겠다. 옵저버 이자 옵저버블이라는 것도 알겠다. 그럼 구독할 때는 어떻게 될까 ? 
  - 구독할때는 일반적인 서브젝트와 마찬가지로 subscribe, bind, drive를 모두 사용할 수 있다. 
  - 다시말하면, relay라고 해서 main thread에서 돌아간다는 보장이 없다는 뜻이다. 아래코드를 보자. 
  
```swift
let relay = PublishRelay<Int>()
//let relay = BehaviorSubject.init(value: 1)
var disposeBag = DisposeBag()

Observable.from([1,2,3,4,5])
    .observe(on: ConcurrentDispatchQueueScheduler(qos: .background))
    .bind(to: relay)
    .disposed(by: disposeBag)


relay
//    .observe(on: MainScheduler.instance)
    .do(onNext: {
        print(Thread.main, $0, "thread...")
    })
    .subscribe()
  
  
  
  
//1.
//let relay = PublishRelay<Int>() 출력 
//.observe(on: MainScheduler.instance) 활성화 
  
<_NSMainThread: 0x6000003d4500>{number = 1, name = main} 1 thread...
<_NSMainThread: 0x6000003d4500>{number = 1, name = main} 2 thread...
<_NSMainThread: 0x6000003d4500>{number = 1, name = main} 3 thread...  
  
//2.
//let relay = PublishRelay<Int>() 출력 
//.observe(on: MainScheduler.instance) 비활성화 
<_NSMainThread: 0x600000bb04c0>{number = 1, name = (null)} 1 thread...
<_NSMainThread: 0x600000bb04c0>{number = 1, name = (null)} 2 thread...
<_NSMainThread: 0x600000bb04c0>{number = 1, name = (null)} 3 thread...

//3. 
//let relay = BehaviorRelay.init(value: 1) 출력
//.observe(on: MainScheduler.instance) 비활성화 
<_NSMainThread: 0x6000010084c0>{number = 1, name = main} -1 thread...
<_NSMainThread: 0x6000010084c0>{number = 1, name = (null)} 1 thread...
<_NSMainThread: 0x6000010084c0>{number = 1, name = (null)} 2 thread...
<_NSMainThread: 0x6000010084c0>{number = 1, name = (null)} 3 thread...

```
  
위에 출력문을 보자. 각 1,2,3번의 조건대로 동작시킨 것이다. 
- 1번을 보자. 퍼블리시릴레이, 출력전에 메인스케쥴러로 바꿔주었다. 그러면 셋다 main쓰레드에서 출력된다. 
- 2번. 1번과 같이 퍼블리시 릴레이, 대신에 이건 출력전에 메인스케쥴러로 바꿔주지 않았다. 그랬더니? 메인쓰레드에서 돌지않는다. 왜? 옵저버블을 생성할때 백그라운드에서 돌게 설정을 했기 때문이다. 
- 3번. 3번도 재미있다. 3번은 2번과 조건이 같으나 퍼블리시가 아니라 비헤이비어 릴레이다. 근데 재밌는게 초기값이 들어가는 -1만 메인쓰레드에서 돈다는 것이다. 이건 음..엑코가 정하는건지 뭔지 이유는 모르지만, 릴레이나 내부코드에서 따로 어디쓰레드에서 돌아라라고 지정되어있진 않지 싶다. 근데 알고는 있어야겠지. 
- 결론 -> relay라고 해서 main thread에서 돌아간다는 보장이 없다. 그래서 구독할때 UI작업이라면 driver를 사용하거나, 또는 obseron + catchjustreturn 오퍼레이터를 사용해서 작업을 해줘야 된다는 것이다. 
  
  
  
  
## 4. Traits 
  
> 한글로 번역하면 특성?이라는 뜻이지만 직역하면 뭔가 좀 어색하다. 그렇지않나 ?.. 특성이라고 말하긴 좀 그렇고.. 기능?? 메소드??같은느낌이 오히려 더 잘어울리는듯.. 대충 그렇게 이해하자
> 공식문서에 온갖 예시와 설명이 기깔나게 나와있다. 항시 공식문서를 보자. 
공식문서  https://github.com/ReactiveX/RxSwift/blob/main/Documentation/Traits.md

  
  
### RxSwift traits 
- 추가예정
  

### RxCocoa traits
    
#### 1. Signal(드라이버랑 비슷함) 
https://medium.com/@hongseongho/%EB%B2%88%EC%97%AD-signal-and-relay-in-rxcocoa-4-619d5194dcbd
  
#### 2. Driver 
  
  UI를 작업할 때. 항상 지켜줘야 되는 조건이 있다. 
  
  1. 메인쓰레드에서 동작해야 한다. 
  2. 스트림이 끊겨도(에러가 발생해도) 스트림이 종료되면 안된다. (UI가 에러가 발생해서 스트리밍 종료되면 앱이 동작하지 않는다는 뜻이기 때문이다.) 
  

  그래서 보통 옵저버블에 이런 작업을 한다. 
  
  ```swift
        Observable.from(["Apple", "Melon", "Grape", "Banana"])
            .catchAndReturn("Error...")
            .observe(on: MainScheduler.instance)
            .bind(to: myLabel.rx.text)
            .disposed(by: disposeBag)
```
  
  여기서 눈여겨봐야 핣 부분은 에러를 캐치하면 리턴값인 "Error..."를 해주는 부분과 .observe(on: MainScheduler.instance) 오퍼레이터이다. 
  
위에서 말한것처럼 UI작업이니까 끊기면 안되니 -> catchAndReturn
메인 쓰레드에서 동작해야 하니까 -> observe(on: MainScheduler.instance)
  
  를 사용한 것이다. 
  
  근데 이게 매번 귀찮잖아 ? 그래서 Driver라는 걸 RxCocoa에서 제공해준다. 
  
  asDriver라는 메소드를 통해서 드라이버로 바꿔주고, bind/subscribe대신에 drive라는 메소드를 사용한다. 
  
 기존 코드에 비해 드라이버가 가지는 장점은 위에서 말했다. 
  
>  1. 에러처리가 따로 필요없고, 메인쓰레드에서만 동작한다는 것
  
  ```swift
        Observable.from(["Apple", "Melon", "Grape", "Banana"])
            .asDriver(onErrorJustReturn: "Error...")
            .drive(myLabel.rx.text)
            .disposed(by: disposeBag)
```


  1번에 대한 근거(내부에서 에러처리 및 쓰레드를 조절해주고 있다.) 
  
```swift
        public func asDriver(onErrorJustReturn: Element) -> Driver<Element> {
            let source = self
                .asObservable()
                .observe(on:DriverSharingStrategy.scheduler)
                .catchAndReturn(onErrorJustReturn)
            return Driver(source)
        }
```
  
  

#### 3. ControlEvent/ControlProperty 

컨트롤 이벤트는 뭐냐면, myButton.rx.tap 같은 애들을 말한다. 
아까 말했지않나? .rx 는 Reactive 타입이라고. 
그리고 버튼클릭을 뜻하는 tap의 구현을 보면 ControlEvent로 구현되어 있다. 
아래의 코드를 보자. 

```swift 
extension Reactive where Base: UIButton {
    
    /// Reactive wrapper for `TouchUpInside` control event.
    public var tap: ControlEvent<Void> {
        controlEvent(.touchUpInside)
    }
}





public struct ControlEvent<PropertyType> : ControlEventType {
    public typealias Element = PropertyType

    let events: Observable<PropertyType>

    /// Initializes control event with a observable sequence that represents events.
    ///
    /// - parameter events: Observable sequence that represents events.
    /// - returns: Control event created with a observable sequence of events.
    public init<Ev: ObservableType>(events: Ev) where Ev.Element == Element {
        self.events = events.subscribe(on: ConcurrentMainScheduler.instance)
    }
```


아까 말한것처럼 .rx 같은 경우는 extension Reactive 에 base로 UIButton인 것을 볼 수 있다. 그리고 내부에 tap이라는 변수로 ControlEvent<Void> 타입이 있다. 
  
  
또, ControlEvent의 내부구현을 보면 
  
얘도 events라는 옵저버블이다. 또 중요한게 init을 보자. 
그러면 얘같은 경우는 events.subscribe(on: ConcurrentMainScheduler.instance) 로 구현되어 있다. 여기서 알수있는것 은 두개다. 
 

#### ControlProperty 공통점                
            
![](https://images.velog.io/images/dev_kickbell/post/72075400-a214-4911-b4cb-5c8d18e73c6d/image.png)
      
#### ControlEvent 특징                    
- subscribe를 해줄 필요가 없다. 이미 되어있으니 
- 메인 쓰레드에서 돌아가는게 보장된다는 것이다. 

#### ControlProperty / ControlEvent 차이점         
- UISearchBar, UISegmentedControl 같은 애들은 ControlProperty
- UIButton 같은애들은 ControlEvent이다. 
- ControlProperty는 옵저버, 옵저버블 둘 다 가능. 
- ControlEvent는 옵저버블은 가능하지만 옵저버의 역할을 하지 못한다.      
          
![](https://images.velog.io/images/dev_kickbell/post/3fad8f69-cf49-46a8-87b1-bb297bfef249/image.png)
  
