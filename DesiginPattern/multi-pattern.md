
# Multi Pattern(복합 패턴)
> - 여러 패턴을 함께 사용해서 다양한 디자인 문제를 해결하는 방법을 복합 패턴이라고 부릅니다.


## 1. 오리 게임 생성하기 
- Quackable 인터페이스 추가하고, 4종류의 오리 생성하기 
- 오리 게임을 실행할 수 있는 DuckSimulator 만들기 


<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
protocol Quackable {
    func quack()
}

class MallardDuck: Quackable {
    func quack() {
        print("꽥꽥")
    }
}

class RedheadDuck: Quackable {
    func quack() {
        print("꽥꽥")
    }
}

class DuckCall: Quackable {
    func quack() {
        print("꽉꽉")
    }
}

class RubberDuck: Quackable {
    func quack() {
        print("삑삑")
    }
}
    
class DuckSimulator {
    func simulate() {
        let mallardDuck: Quackable = MallardDuck()
        let redheadDuck: Quackable = RedheadDuck()
        let duckCall: Quackable = DuckCall()
        let rubberDuck: Quackable = RubberDuck()
        
        print("오리 시뮬레이션 게임")
        
        simulate(mallardDuck)
        simulate(redheadDuck)
        simulate(duckCall)
        simulate(rubberDuck)
    }
    
    func simulate(_ duck: Quackable) {
        duck.quack()
    }
}
    
let duckSimulator = DuckSimulator()
duckSimulator.simulate()
/*
오리 시뮬레이션 게임
꽥꽥
꽥꽥
꽉꽉
삑삑
*/
```
  </p>
</details>


## 2. 거위 추가하기(어댑터 패턴)
- 거위도 소리내고, 날고, 헤엄치기 때문에 오리 게임에 거위도 추가해봅니다.
- 기존 코드는 최대한 유지한 상태로 거위를 추가하려면 어떻게 해야 할까요? 
- 어댑터 패턴을 사용합니다. 

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class Goose {
    func honk() {
        print("끽끽")
    }
}

class GooseAdapter: Quackable {
    let goose: Goose
    
    init(_ goose: Goose) {
        self.goose = goose
    }
    
    func quack() {
        goose.honk()
    }
}

class DuckSimulator {
    func simulate() {
        let mallardDuck: Quackable = MallardDuck()
        let redheadDuck: Quackable = RedheadDuck()
        let duckCall: Quackable = DuckCall()
        let rubberDuck: Quackable = RubberDuck()
        let gooseDuck: Quackable = GooseAdapter(Goose())
        
        print("오리 시뮬레이션 게임")
        
        simulate(mallardDuck)
        simulate(redheadDuck)
        simulate(duckCall)
        simulate(rubberDuck)
        simulate(gooseDuck)
    }
    
    func simulate(_ duck: Quackable) {
        duck.quack()
    }
}
    
let duckSimulator = DuckSimulator()
duckSimulator.simulate()
/*
오리 시뮬레이션 게임
꽥꽥
꽥꽥
꽉꽉
삑삑
끽끽
*/
```
  </p>
</details>

## 3. 오리가 꽥꽥 소리는 낸 횟수 추가하기(데코레이터 패턴)
- 꽥꽥 소리를 낸 횟수를 세 주는 기능을 추가하려고 합니다. 
- 기존 Duck 코드는 건드리고 싶지 않습니다.
- 새로운 행동(꽥꽥 소리를 낸 횟수를 세 주는 기능)을 추가하려면 데코레이터를 만들고 객체를 그 데코레이터로 감싸면 될 겁니다.
- 클라이언트 요청에 따라 거위소리는 필요없으니 데코레이터로 감싸지 않습니다. 


<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class QuackCounter: Quackable {
    
    static var numberOfQuacks: Int = 0
    let duck: Quackable
    
    init(_ duck: Quackable) {
        self.duck = duck
    }
    
    func quack() {
        duck.quack()
        Self.numberOfQuacks += 1
    }
    
    static func getQuacks() -> Int {
        return Self.numberOfQuacks
    }
    
}
    
class DuckSimulator {
    func simulate() {
        let mallardDuck: Quackable = QuackCounter(MallardDuck())
        let redheadDuck: Quackable = QuackCounter(RedheadDuck())
        let duckCall: Quackable = QuackCounter(DuckCall())
        let rubberDuck: Quackable = QuackCounter(RubberDuck())
        let gooseDuck: Quackable = GooseAdapter(Goose())
        
        print("오리 시뮬레이션 게임")
        
        simulate(mallardDuck)
        simulate(redheadDuck)
        simulate(duckCall)
        simulate(rubberDuck)
        simulate(gooseDuck)
        
        print("오리가 소리를 낸 횟수: \(QuackCounter.getQuacks()) 회")
    }
    
    func simulate(_ duck: Quackable) {
        duck.quack()
    }
}
    
let duckSimulator = DuckSimulator()
duckSimulator.simulate()

/*
 오리 시뮬레이션 게임
 꽥꽥
 꽥꽥
 꽉꽉
 삑삑
 끽끽
 오리가 소리를 낸 횟수: 4 회
 */
```
  </p>
</details>

## 4. 오리 객체 생성 작업을 하나로 합치기(팩토리 패턴)
- 데코레이터 패턴을 쓰는 것 까지는 좋았습니다. 다만, 객체를 데코레이터로 하나하나 감싸주지 않으면 원하는 행동이 추가되지 않는 이슈를 발견했습니다.
- 오리 객체를 생성하는 작업을 하나로 합치려고 합니다. 오리를 생성하고 데코레이터로 감싸는 부분을 캡슐화 하는 것이죠. 
- 추상 팩토리 패턴을 사용합니다. 
- 데코레이터가 없는 DuckFactory를 구현합니다. 시뮬레이터는 실체 어떤 오리 제품이 생성된다는 것을 알 수 없고, Quackable이 리턴된다는 것만 압니다. 실제로 DuckFactory를 사용하지는 않습니다.
- 실제로 사용할 CountingDuckFactory를 생성합니다. DuckFactory를 데코레이터로 감싼 녀석입니다. 이 녀석을 아래의 실제 코드에 사용합니다. 
- createMallardDuck() 같은 식으로 만드는데 리턴 값은 Quackable가 리턴된다는 것만 알고 있으므로 객체 생성을 캡슐화 할 수 있습니다.
- CountingDuckFactory에서 객체 생성 시에 데코레이터로 감싸므로 시뮬레이터와는 관계없이 모든 오리를 데코레이터로 확실하게 감쌀 수 있게 되었습니다. 


<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
protocol AbstractDuckFactory {
    func createMallardDuck() -> Quackable
    func createRedheadDuck() -> Quackable
    func createDuckCall() -> Quackable
    func createRubberDuck() -> Quackable
    func createGooseDuck() -> Quackable
}

//class DuckFactory: AbstractDuckFactory {
//    func createMallardDuck() -> Quackable {
//        return MallardDuck()
//    }
//
//    func createRedheadDuck() -> Quackable {
//        return RedheadDuck()
//    }
//
//    func createDuckCall() -> Quackable {
//        return DuckCall()
//    }
//
//    func createRubberDuck() -> Quackable {
//        return RubberDuck()
//    }
//}

class CountingDuckFactory: AbstractDuckFactory {
    func createMallardDuck() -> Quackable {
        return QuackCounter(MallardDuck())
    }
    
    func createRedheadDuck() -> Quackable {
        return QuackCounter(RedheadDuck())
    }
    
    func createDuckCall() -> Quackable {
        return QuackCounter(DuckCall())
    }
    
    func createRubberDuck() -> Quackable {
        return QuackCounter(RubberDuck())
    }
        
    func createGooseDuck() -> Quackable {
        return GooseAdapter(Goose())
    }
}
    
class DuckSimulator {
    func simulate(_ duckFactory: AbstractDuckFactory) {
        let mallardDuck: Quackable = duckFactory.createMallardDuck()
        let redheadDuck: Quackable = duckFactory.createRedheadDuck()
        let duckCall: Quackable = duckFactory.createDuckCall()
        let rubberDuck: Quackable = duckFactory.createRubberDuck()
        let gooseDuck: Quackable = duckFactory.createGooseDuck()

        print("오리 시뮬레이션 게임")
        
        simulate(mallardDuck)
        simulate(redheadDuck)
        simulate(duckCall)
        simulate(rubberDuck)
        simulate(gooseDuck)
        
        print("오리가 소리를 낸 횟수: \(QuackCounter.getQuacks()) 회")
    }
    
    func simulate(_ duck: Quackable) {
        duck.quack()
    }
}
    
let duckSimulator = DuckSimulator()
let duckFactory = CountingDuckFactory()
duckSimulator.simulate(duckFactory)

/*
 오리 시뮬레이션 게임
 꽥꽥
 꽥꽥
 꽉꽉
 삑삑
 끽끽
 오리가 소리를 낸 횟수: 4 회
 */
```
  </p>
</details>


## 5. 오리 무리를 관리하는 기능 추가하기(컴포지트 패턴)
- 갈수록 늘어나는 오리를 한마리씩 관리하기가 버겁습니다. 객체들로 구성된 컬렉션을 개별 객체와 같은 방식으로 다룰 수 있게 해주는 컴포지트 패턴을 적용해봅니다. 
- 음, 그냥 Swift의 컬렉션 타입(Array, Dictionary) 같은 녀석들 처럼 내가 만든 오리Duck라는 객체도 그렇게 쓸 수 있게 한다 정도로 이해하면 되지 않을까 싶습니다.
- 하려고 하는 작업은 그냥 오리 무리를 물오리(Mallard) 무리와 그냥 오리(Duck)무리로 나누어서 관리할 수 있게 바꾸기 입니다. 

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
//무리, 짐승의 떼
class Flock: Quackable {
    var quackers: [Quackable] = []
    
    func add(_ quacker: Quackable) {
        self.quackers.append(quacker)
    }
    
    func quack() {
        quackers.forEach {
            $0.quack()
        }
    }
}
    
class DuckSimulator {
    func simulate(_ duckFactory: AbstractDuckFactory) {
//        let mallardDuck: Quackable = duckFactory.createMallardDuck()
        let redheadDuck: Quackable = duckFactory.createRedheadDuck()
        let duckCall: Quackable = duckFactory.createDuckCall()
        let rubberDuck: Quackable = duckFactory.createRubberDuck()
        let gooseDuck: Quackable = duckFactory.createGooseDuck()

        print("오리 시뮬레이션 게임")
        
        let flockOfDucks = Flock()
        flockOfDucks.add(redheadDuck)
        flockOfDucks.add(duckCall)
        flockOfDucks.add(rubberDuck)
        flockOfDucks.add(gooseDuck)
        
        let flockOfMallards = createMallards(duckFactory)
        flockOfDucks.add(flockOfMallards)
        
        print("\n오리 게임: 전체 오리 무리")
        simulate(flockOfDucks)
        
        print("\n오리 게임: Mallard 오리 무리")
        simulate(flockOfMallards)
        
        print("\n오리가 소리를 낸 횟수: \(QuackCounter.getQuacks()) 회")
    }
    
    func createMallards(_ duckFactory: AbstractDuckFactory) -> Flock {
        let flockOfMallards = Flock()
        
        let mallardOne: Quackable = duckFactory.createMallardDuck()
        let mallardTwo: Quackable = duckFactory.createMallardDuck()
        let mallardThree: Quackable = duckFactory.createMallardDuck()
        let mallardFour: Quackable = duckFactory.createMallardDuck()
        flockOfMallards.add(mallardOne)
        flockOfMallards.add(mallardTwo)
        flockOfMallards.add(mallardThree)
        flockOfMallards.add(mallardFour)
        
        return flockOfMallards
    }
    
    func simulate(_ duck: Quackable) {
        duck.quack()
    }
}
    
let duckSimulator = DuckSimulator()
let duckFactory = CountingDuckFactory()
duckSimulator.simulate(duckFactory)

/*
 오리 시뮬레이션 게임

 오리 게임: 전체 오리 무리
 꽥꽥
 꽉꽉
 삑삑
 끽끽
 꽥꽥
 꽥꽥
 꽥꽥
 꽥꽥

 오리 게임: Mallard 오리 무리
 꽥꽥
 꽥꽥
 꽥꽥
 꽥꽥

 오리가 소리를 낸 횟수: 11 회
 */
```
  </p>
</details>


## 6. 반대로 개별 오리의 행동을 관찰하기(옵저버 패턴)
- 개별 오리의 행동을 관찰하려고 합니다. 먼저 인터페이스를 2개 만들어 주어야 합니다. Rx로 따지면 Observable, Subject 역할을 하는 QuackProtocol을 만들고 이어서 Observer 역할을 하는 QuackObserver도 만들어 줍니다. 
- 지금 관찰하고 싶은 것은 Quackable 프로토콜을 준수하는 애들이니까, Quackable이 QuackProtocol를 준수하도록 해주어야겠죠. 
- 그리고 각각 해줘도 되지만, 옵저버를 등록하고 실행하게 하는 QuackObservable라는 클래스를 따로 만들어줍니다. 그러면 실제로 QuackProtocol는 QuackObservable에게 위임하게만 작업하면 됩니다. 
- 이제 MallardDuck, RedheadDuck 등에서 작업을 해줘야 합니다. 근데 QuackObservable이 있으니 구성(Composition)으로 QuackProtocol를 준수하는 QuackObservable 인스턴스를 가지고 있습니다. 
- 그리고 각 registerObserver, notifyObservers 메소드로 작업을 위임합니다. QuackCounter도 똑같이 작성해줍니다.
- 컴포지트 패턴이 적용되어있기 때문에 Flock에도 구현해주어야 합니다. 가지고있는 quackers로 등록해주면 됩니다. 
- 마지막으로 인터페이스만 있는 Observer를 구현해야겠죠. Quacklogist를 작성해주고 시뮬레이터에서 오리무리에게 Quacklogist의 인스턴스를 넘겨줍니다. 



<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
protocol QuackProtocol {
    func registerObserver(ob: QuackObserver)
    func notifyObservers()
}

protocol QuackObserver {
    func update(_ duck: QuackProtocol)
}

class QuackObservable: QuackProtocol {
    var observers: [QuackObserver] = []
    let duck: QuackProtocol
    
    init(_ duck: QuackProtocol) {
        self.duck = duck
    }
    
    func registerObserver(ob: QuackObserver) {
        observers.append(ob)
    }
    
    func notifyObservers() {
        observers.forEach {
            $0.update(duck)
        }
    }
}

class Quacklogist: QuackObserver {
    func update(_ duck: QuackProtocol) {
        print("꽥꽥학자: \(duck)가 방금 소리냈다.")
    }
}
```
```swift
protocol Quackable: QuackProtocol {
    func quack()
}

class MallardDuck: Quackable {
    lazy var observable: QuackProtocol = QuackObservable(self)
    
    func quack() {
        print("꽥꽥")
        notifyObservers()
    }
    
    func registerObserver(ob: QuackObserver) {
        observable.registerObserver(ob: ob)
    }
    
    func notifyObservers() {
        observable.notifyObservers()
    }
}

class RedheadDuck: Quackable {
    lazy var observable: QuackProtocol = QuackObservable(self)

    func quack() {
        print("꽥꽥")
        notifyObservers()
    }
    
    func registerObserver(ob: QuackObserver) {
        observable.registerObserver(ob: ob)
    }
    
    func notifyObservers() {
        observable.notifyObservers()
    }
}

class DuckCall: Quackable {
    lazy var observable: QuackProtocol = QuackObservable(self)
    
    func quack() {
        print("꽉꽉")
        notifyObservers()
    }
    
    func registerObserver(ob: QuackObserver) {
        observable.registerObserver(ob: ob)
    }
    
    func notifyObservers() {
        observable.notifyObservers()
    }
}

class RubberDuck: Quackable {
    lazy var observable: QuackProtocol = QuackObservable(self)
    
    func quack() {
        print("삑삑")
        notifyObservers()
    }
    
    func registerObserver(ob: QuackObserver) {
        observable.registerObserver(ob: ob)
    }
    
    func notifyObservers() {
        observable.notifyObservers()
    }
}
    
class QuackCounter: Quackable {
    static var numberOfQuacks: Int = 0
    let duck: Quackable
    
    init(_ duck: Quackable) {
        self.duck = duck
    }
    
    func quack() {
        duck.quack()
        Self.numberOfQuacks += 1
    }
    
    static func getQuacks() -> Int {
        return Self.numberOfQuacks
    }
    
    func registerObserver(ob: QuackObserver) {
        duck.registerObserver(ob: ob)
    }
    
    func notifyObservers() {
        duck.notifyObservers()
    }
}
```
```swift
class DuckSimulator {
    func simulate(_ duckFactory: AbstractDuckFactory) {
//        let mallardDuck: Quackable = duckFactory.createMallardDuck()
        let redheadDuck: Quackable = duckFactory.createRedheadDuck()
        let duckCall: Quackable = duckFactory.createDuckCall()
        let rubberDuck: Quackable = duckFactory.createRubberDuck()
        let gooseDuck: Quackable = duckFactory.createGooseDuck()

        print("오리 시뮬레이션 게임")
        
        let flockOfDucks = Flock()
        flockOfDucks.add(redheadDuck)
        flockOfDucks.add(duckCall)
        flockOfDucks.add(rubberDuck)
        flockOfDucks.add(gooseDuck)
        
        let flockOfMallards = createMallards(duckFactory)
        flockOfDucks.add(flockOfMallards)
        
        let quacklogist = Quacklogist()
        flockOfDucks.registerObserver(ob: quacklogist)
        
        print("\n오리 게임: 전체 오리 무리")
        simulate(flockOfDucks)
        
        print("\n오리 게임: Mallard 오리 무리")
        simulate(flockOfMallards)
        
        
        
        print("\n오리가 소리를 낸 횟수: \(QuackCounter.getQuacks()) 회")
    }
    
    func createMallards(_ duckFactory: AbstractDuckFactory) -> Flock {
        let flockOfMallards = Flock()
        
        let mallardOne: Quackable = duckFactory.createMallardDuck()
        let mallardTwo: Quackable = duckFactory.createMallardDuck()
        let mallardThree: Quackable = duckFactory.createMallardDuck()
        let mallardFour: Quackable = duckFactory.createMallardDuck()
        flockOfMallards.add(mallardOne)
        flockOfMallards.add(mallardTwo)
        flockOfMallards.add(mallardThree)
        flockOfMallards.add(mallardFour)
        
        return flockOfMallards
    }
    
    func simulate(_ duck: Quackable) {
        duck.quack()
    }
}

let duckSimulator = DuckSimulator()
let duckFactory = CountingDuckFactory()
duckSimulator.simulate(duckFactory)

/*
 오리 시뮬레이션 게임

 오리 게임: 전체 오리 무리
 꽥꽥
 꽥꽥학자: MultiPattern.RedheadDuck가 방금 소리냈다.
 꽉꽉
 꽥꽥학자: MultiPattern.DuckCall가 방금 소리냈다.
 삑삑
 꽥꽥학자: MultiPattern.RubberDuck가 방금 소리냈다.
 끽끽
 꽥꽥학자: MultiPattern.GooseAdapter가 방금 소리냈다.
 꽥꽥
 꽥꽥학자: MultiPattern.MallardDuck가 방금 소리냈다.
 꽥꽥
 꽥꽥학자: MultiPattern.MallardDuck가 방금 소리냈다.
 꽥꽥
 꽥꽥학자: MultiPattern.MallardDuck가 방금 소리냈다.
 꽥꽥
 꽥꽥학자: MultiPattern.MallardDuck가 방금 소리냈다.

 오리 게임: Mallard 오리 무리
 꽥꽥
 꽥꽥학자: MultiPattern.MallardDuck가 방금 소리냈다.
 꽥꽥
 꽥꽥학자: MultiPattern.MallardDuck가 방금 소리냈다.
 꽥꽥
 꽥꽥학자: MultiPattern.MallardDuck가 방금 소리냈다.
 꽥꽥
 꽥꽥학자: MultiPattern.MallardDuck가 방금 소리냈다.

 오리가 소리를 낸 횟수: 11 회
 */
```
  </p>
</details>


## 7. 오리 게임에 사용된 패턴들 정리하기 
- 사실, 오리 게임에 쓰인 패턴은 복합패턴은 아닙니다. 그저 하나의 프로젝트에도 예시를 보여주기 위해서 여러가지 패턴을 사용한 것이죠. 
- 복합 패턴이란, 어떤 문제를 해결하기 위해서 몇 개의 패턴을 복합적으로 사용하는 것을 말하죠. 
- 패턴은 반드시 상황에 맞게 써야 합니다. 억지로 적용하면 오히려 문제가 될 수 있어요. 
- 오리만 있는데 거위가 나타나버려서 어댑터 패턴을 적용했고, 꽥꽥 소리가 난 횟수를 세고 싶다고 해서 데코레이터 패턴을 사용했습니다. 
- 데코레이터로 장식되지 않은 Quackable 객체를 우려해서 아예 통째로 객체 생성을 캡슐화하는 추상 팩토리 패턴을 적용합니다. 그러면 객체 생성에는 항상 팩토리를 사용하니까 캡슐화+데코레이터 이슈를 해결했습니다. 
- 오리와 거위가 너무 많아짐에 따라 오리를 무리 단위로 관리하기 위해 컴포지트 패턴을 적용했고, Quackable에서 소리가 났을 때 연락을 받고 싶었기에 옵저버 패턴을 적용했습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/81717019-4263-4be5-b52a-5ad3e5dd5ce0/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/7584b9e5-18eb-41b2-91de-ff7393a40063/image.png)

## 8. Model-View-Contrller 패턴 
- 어떤 앱을 하나 만드는 문제를 해결하기 위해서 여러 개의 패턴이 복합적으로 사용된 다지인 중에 하나가 Model-View-Contrller 패턴 입니다. 
- 모델은 옵저버 패턴이 사용되어 있어요. 그래서 모델이 바뀔 때 마다 뷰와 컨트롤러에게 연락합니다. 
- 컨트롤러는 전략 패턴이 사용되어 있어요. Swift로 예시를 들면, TableViewController 라던지, CollectionViewController, MyViewController 처럼 어떤 뷰 객체마다 다른 전략을 사용할 수 있는 거죠. 또 컨트롤러를 바꾸면 뷰의 행동도 바꿀 수 있습니다. 컨트롤러에서 뷰의 행동에 관한 동작을 다 처리하기 때문이죠. 
- 뷰에는 컴포지트 패턴이 적용되는데, 디스플레이는 여러 단계로 겹쳐있는 윈도우/버튼/레이블 등으로 구성됩니다. 최상위 구성요소에는 다른 구성 요소들이 들어있고, 그 안에는 각각 다른 구성요소가 들어갈 수 있습니다. 맨 끝에는 잎 개체가 있어요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/29728547-bfb5-4f1e-b7fa-8acc0269c8d0/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/2193931e-6e7e-43ae-a2b5-f5b911b55698/image.png)


## Reference 
[https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223](https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223)

