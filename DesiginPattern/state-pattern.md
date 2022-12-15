
# State Pattern(상태 패턴)
> - 객체의 내부 상태가 바뀜에 따라서 객체의 행동을 바꿀 수 있습니다. 마치 객체의 클래스가 바뀌는 것과 같은 결과를 얻을 수 있습니다.


## 1. 최첨단 뽑기 기계 
- 뽑기 기계 회사에서 의뢰가 들어왔습니다. 
- 원하는 상태 다이어그램을 보내주었고, 우리는 그에 맞춰 코드를 제작하면 될 겁니다. 
			
![](https://velog.velcdn.com/images/dev_kickbell/post/4a7c29aa-5d5c-4db6-8c70-0590f4bb8866/image.png)

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
/*
 우리나라가 1,10,50,100,500원 동전이 있듯,
 미국도 그렇게 동전이 있고 이름도 따로 있다고 합니다.
 여기서는 뽑기 한 번 돌리는데 25 cent가 드나보네요.
 1달러가 1300원이니까,, 300원쯤 되는듯?
 
 1 cent = penny(푼돈)
 5 cent = nickel(니켈)
 10 cent = dime(다임)
 25 cent = quarter(쿼터)
 50 cent = half dollar(하프 달러)
 100 cent = dollar(달러)
 */
enum State: String {
    case soldOut = "매진"
    case noQuarter = "동전 투입 대기중"
    case hasQuarter = "동전 투입 완료"
    case sold = "알맹이 내보내는 중"
}

class GumballMachine {
    var state: State = .soldOut
    var count = 0   
    var description: String {
        return "\n주식회사 왕뽑기\n[ 최신형 뽑기 기계 ]\n남은 개수: \(count)개\n현재 상태: \(state.rawValue)\n"
    }
    
    init(count: Int) {
        self.count = count
        //제품이 1개 이상이면 돈을 넣어주길 기다리는 상태로 할당
        if count > 0 { state = .noQuarter }
    }
    
    //동전 넣기
    func insertQuarter() {
        switch state {
        case .hasQuarter:
            print("동전은 한 개만 넣어주세요.")
        case .noQuarter:
            state = .hasQuarter
            print("동전이 투입되었습니다.")
        case .soldOut:
            print("매진되었습니다. 다음 기회에 이용해주세요.")
        case .sold:
            print("알맹이를 내보내고 있습니다.")
        }
    }
    
    //동전 꺼내기
    func ejectQuarter() {
        switch state {
        case .hasQuarter:
            print("동전이 반환됩니다.")
            state = .noQuarter
        case .noQuarter:
            state = .hasQuarter
            print("동전이 넣어주세요.")
        case .sold:
            print("이미 알맹이를 뽑으셨습니다.")
        case .soldOut:
            print("동전을 넣지 않으셨습니다. 동전이 반환되지 않습니다.")
        }
    }
    
    //크랭크(손잡이)를 돌리기
    func turnCrank() {
        switch state {
        case .sold:
            print("손잡이는 한 번만 돌려주세요.")
        case .noQuarter:
            state = .hasQuarter
            print("동전을 넣어주세요.")
        case .soldOut:
            print("매진되었습니다.")
        case .hasQuarter:
            print("손잡이를 돌리셨습니다.")
            state = .sold
            dispense()
        }
    }
    
    //제품 내보내기
    func dispense() {
        switch state {
        case .sold:
            print("알맹이를 내보내고 있습니다.")
            count -= 1
            if count == 0 {
                print("더 이상 알맹이가 없습니다.")
                state = .soldOut
            } else {
                state = .noQuarter
            }
        case .noQuarter:
            print("동전을 넣어주세요.")
        case .soldOut:
            print("매진되었습니다.")
        case .hasQuarter:
            print("알맹이를 내보낼 수 없습니다.")
        }
    }
}
```
```swift
let gumballMachine = GumballMachine(count: 5)

print(gumballMachine)
print(gumballMachine.description)

gumballMachine.insertQuarter()
gumballMachine.turnCrank()

print(gumballMachine.description)

gumballMachine.insertQuarter()
gumballMachine.ejectQuarter()
gumballMachine.turnCrank()

print(gumballMachine.description)

gumballMachine.insertQuarter()
gumballMachine.turnCrank()
gumballMachine.insertQuarter()
gumballMachine.turnCrank()
gumballMachine.ejectQuarter()

print(gumballMachine.description)

gumballMachine.insertQuarter()
gumballMachine.insertQuarter()
gumballMachine.turnCrank()
gumballMachine.insertQuarter()
gumballMachine.turnCrank()
gumballMachine.insertQuarter()
gumballMachine.turnCrank()

print(gumballMachine.description)

/*
 StatePattern.GumballMachine

 주식회사 왕뽑기
 [ 최신형 뽑기 기계 ]
 남은 개수: 5개
 현재 상태: 동전 투입 대기중

 동전이 투입되었습니다.
 손잡이를 돌리셨습니다.
 알맹이를 내보내고 있습니다.

 주식회사 왕뽑기
 [ 최신형 뽑기 기계 ]
 남은 개수: 4개
 현재 상태: 동전 투입 대기중

 동전이 투입되었습니다.
 동전이 반환됩니다.
 동전을 넣어주세요.

 주식회사 왕뽑기
 [ 최신형 뽑기 기계 ]
 남은 개수: 4개
 현재 상태: 동전 투입 완료

 동전은 한 개만 넣어주세요.
 손잡이를 돌리셨습니다.
 알맹이를 내보내고 있습니다.
 동전이 투입되었습니다.
 손잡이를 돌리셨습니다.
 알맹이를 내보내고 있습니다.
 동전이 넣어주세요.

 주식회사 왕뽑기
 [ 최신형 뽑기 기계 ]
 남은 개수: 2개
 현재 상태: 동전 투입 완료

 동전은 한 개만 넣어주세요.
 동전은 한 개만 넣어주세요.
 손잡이를 돌리셨습니다.
 알맹이를 내보내고 있습니다.
 동전이 투입되었습니다.
 손잡이를 돌리셨습니다.
 알맹이를 내보내고 있습니다.
 더 이상 알맹이가 없습니다.
 매진되었습니다. 다음 기회에 이용해주세요.
 매진되었습니다.

 주식회사 왕뽑기
 [ 최신형 뽑기 기계 ]
 남은 개수: 0개
 현재 상태: 매진

 Program ended with exit code: 0
 */
```
  </p>
</details>

## 2. 10분의 1확률 게임기능 더하기 
- 제품을 성공적으로 납품했습니다. 다행히 뽑기 회사는 제품에 만족헀다고 합니다. 하지만, 추가 요청이 들어왔어요. 10분의 1의 확률로 알맹이가 2개가 나와야 된다고 합니다. 
- 그런데 winner라는 상태값을 하나 추가해서 수정하려고 하니, 기존의 코드를 너무 많이 고쳐야 합니다. 다른 기능 추가 요청이 더 들어온다면 그때부턴 더 힘들어지겠죠.
- 아, 또 추가로 리필 기능이 필요하다고 합니다. 그 기능도 추가를 해야 해요! 
- 현재 코드가 가지고 있는 문제점
    - OCP를 위반합니다.(변경에는 닫혀있고, 확장에는 열려있어야 한다.)
    - 이런 디자인은 객체지향 디자인이라고 하기 힘듭니다. 
    - 상태 전환이 복잡한 조건문 속에 숨어있어서 디버깅하기 힘듭니다. 
    - 바뀌는 부분을 전혀 캡슐화하지 않습니다. 
    - 새로운 기능을 추가하는 과정에서 기존 코드에 없던 새로운 버그가 생길 가능성이 높습니다.
				
![](https://velog.velcdn.com/images/dev_kickbell/post/e108da8e-a70c-40a6-879f-807de773b9f9/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/da8686a2-ad29-4d81-bd56-a1079c7bfb56/image.png)

## 3. 상태 패턴으로 리팩토링하기 
- 상태별 행동을 별도의 클래스에 넣어두고, 각자 자기 할 일을 구현하도록 하면 어떨까? 새로운 상태(winner)가 늘어난다면 그냥 클래스를 새로 추가하면 될 수 있도록 말이죠.
- 각 상태클래스에서 사용할 인터페이스를 만듭니다. 
- 10% 당첨률을 추가한 WinnerState와 refill() 기능을 추가했습니다. 
- SoldState에서 해결하지 않고 WinnerState를 추가한 것은 SRP에 위배되기 때문입니다. 절대적으로 꼭 지켜야할 법칙은 아니지만, 특별 행사 기간이 끝나거나 당첨 확률이 달라지거나 하는 변수에 대응하기 위해서는 나누는 게 더 좋습니다. 
- 상태 패턴을 적용해서 수정된 코드 
    - 각 상태의 행동을 별개의 클래스로 국지화했습니다.
    - 관리하기 힘든 if문들을 없앴습니다.
	- 각 상태는 변경에 닫혀 있게 했고, GumballMachine 클래스는 새로운 상태를 추가하는 확장에는 열려 있도록 고쳤습니다.(OCP)
    - 오너가 처음 제시했던 다이어그램에 훨씬 가까우면서 더 이해하기 좋은 코드 베이스와 클래스 구조를 만들었습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/187a1d5e-f4de-47f4-9ca4-0618b97ae2e3/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/7a4e12b1-a89b-40ec-ac94-cfa0be1377bb/image.png)

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
protocol State {
    var description: String { get }
    
    func insertQuarter() //동전 넣기
    func ejectQuarter() //동전 반환하기
    func turnCrank() //크랭크(손잡이) 돌리기
    func dispense() //알맹이 내보내기
    func refill() //알맹이 리필하기
}

extension State {
    func refill() { }
}
    
class NoQuarterState: State {
    var description: String = "동전 투입 대기중"
    
    let gumballMachine: GumballMachine
    
    init(_ gumballMachine: GumballMachine) {
        self.gumballMachine = gumballMachine
    }
    
    func insertQuarter() {
        print("동전을 넣으셨습니다.")
        gumballMachine.setState(gumballMachine.hasQuarterState)
    }
    
    func ejectQuarter() {
        print("동전을 넣어주세요.")
    }
    
    func turnCrank() {
        print("동전을 넣어주세요.")
    }
    
    func dispense() {
        print("동전을 넣어주세요.")
    }
}
    
class HasQuarterState: State {
    var description: String = "동전 투입 완료"
    
    let randomWinner = Int.random(in: 1...10) //10% 확률 난수 생성
    let gumballMachine: GumballMachine
    
    init(_ gumballMachine: GumballMachine) {
        self.gumballMachine = gumballMachine
    }
    
    func insertQuarter() {
        print("동전은 한 개만 넣어주세요.")
    }
    
    func ejectQuarter() {
        print("동전이 반환됩니다.")
        gumballMachine.setState(gumballMachine.noQuarterState)
    }
    
    func turnCrank() {
        print("손잡이를 돌리셨습니다.")
        //보너스 발동 조건: 럭키 세븐 == 10% 난수
        if randomWinner == 7 && gumballMachine.count > 1 {
            gumballMachine.setState(gumballMachine.winnerState)
        } else {
            gumballMachine.setState(gumballMachine.soldState)
        }
    }
    
    func dispense() {
        print("알맹이를 내보낼 수 없습니다.")
    }
}
    
class SoldState: State {
    var description: String = "알맹이 내보내는 중"
    
    let gumballMachine: GumballMachine
    
    init(_ gumballMachine: GumballMachine) {
        self.gumballMachine = gumballMachine
    }
    
    func insertQuarter() {
        print("알맹이를 내보내고 있습니다.")
    }
    
    func ejectQuarter() {
        print("이미 알맹이를 뽑으셨습니다.")
    }
    
    func turnCrank() {
        print("손잡이는 한 번만 돌려주세요.")
    }
    
    func dispense() {
        gumballMachine.releaseBall()
        
        if gumballMachine.count > 0 {
            gumballMachine.setState(gumballMachine.noQuarterState)
        } else {
            print("Oops, out of gumballs!")
            gumballMachine.setState(gumballMachine.soldOutState)
        }
    }
}
    
class SoldOutState: State {
    var description: String = "매진"
    
    let gumballMachine: GumballMachine
    
    init(_ gumballMachine: GumballMachine) {
        self.gumballMachine = gumballMachine
    }
    
    func insertQuarter() {
        print("죄송합니다. 매진되었습니다.")
    }
    
    func ejectQuarter() {
        print("동전을 반환할 수 없습니다. 동전을 넣지 않았습니다.")
    }
    
    func turnCrank() {
        print("죄송합니다. 매진되었습니다.")
    }
    
    func dispense() {
        print("알맹이를 내보낼 수 없습니다.")
    }
    
    func refill() {
        gumballMachine.setState(gumballMachine.noQuarterState)
    }
}
    
class WinnerState: State {
    var description: String = "보너스 당첨"
    
    let gumballMachine: GumballMachine
    
    init(_ gumballMachine: GumballMachine) {
        self.gumballMachine = gumballMachine
    }
    
    func insertQuarter() {
        print("알맹이를 내보내고 있습니다.")
    }
    
    func ejectQuarter() {
        print("이미 알맹이를 뽑으셨습니다.")
    }
    
    func turnCrank() {
        print("손잡이는 한 번만 돌려주세요.")
    }
    
    func dispense() {
        gumballMachine.releaseBall()
        if gumballMachine.count == 0 {
            gumballMachine.setState(gumballMachine.soldOutState)
        } else {
            gumballMachine.releaseBall()
            print("축하드립니다! 알맹이를 하나 더 받으실 수 있습니다.")
            if gumballMachine.count > 0 {
                gumballMachine.setState(gumballMachine.noQuarterState)
            } else {
                print("더 이상 알맹이가 없습니다.")
                gumballMachine.setState(gumballMachine.soldOutState)
            }
        }
    }
}
```
```swift
/*
 우리나라가 1,10,50,100,500원 동전이 있듯,
 미국도 그렇게 동전이 있고 이름도 따로 있다고 합니다.
 여기서는 뽑기 한 번 돌리는데 25 cent가 드나보네요.
 1달러가 1300원이니까,, 300원쯤 되는듯?
 
 1 cent = penny(푼돈)
 5 cent = nickel(니켈)
 10 cent = dime(다임)
 25 cent = quarter(쿼터)
 50 cent = half dollar(하프 달러)
 100 cent = dollar(달러)
 */

class GumballMachine {
    var count = 0
    var description: String {
        return "\n주식회사 왕뽑기\n[ 최신형 뽑기 기계 ]\n남은 개수: \(count)개\n현재 상태: \(state.description)\n"
    }
    lazy var state: State = count > 0 ? noQuarterState : soldOutState
    
    lazy var soldOutState = SoldOutState(self)
    lazy var noQuarterState = NoQuarterState(self)
    lazy var hasQuarterState = HasQuarterState(self)
    lazy var soldState = SoldState(self)
    lazy var winnerState = WinnerState(self)
    
    init(_ numberGumballs: Int) {
        self.count = numberGumballs
    }
    
    //동전 넣기
    func insertQuarter() {
        state.insertQuarter()
    }
    
    //동전 꺼내기
    func ejectQuarter() {
        state.ejectQuarter()
    }
    
    //크랭크(손잡이)를 돌리기
    func turnCrank() {
        state.turnCrank()
        state.dispense()
    }
    
    func setState(_ state: State) {
        self.state = state
    }
    
    func releaseBall() {
        print("알맹이를 내보내고 있습니다.")
        if count > 0 { count -= 1 }
    }
    
    func refill(_ count: Int) {
        self.count += count
        print("\(self.count)개의 알맹이가 리필되었습니다.")
        state.refill()
    }
    
}
```
```swift
let gumballMachine = GumballMachine(5)

print(gumballMachine.description)

gumballMachine.insertQuarter()
gumballMachine.turnCrank()

print(gumballMachine.description)

gumballMachine.insertQuarter()
gumballMachine.turnCrank()
gumballMachine.insertQuarter()
gumballMachine.turnCrank()

print(gumballMachine.description)

gumballMachine.refill(5)

print(gumballMachine.description)

/*
 주식회사 왕뽑기
 [ 최신형 뽑기 기계 ]
 남은 개수: 5개
 현재 상태: 동전 투입 대기중

 동전을 넣으셨습니다.
 손잡이를 돌리셨습니다.
 알맹이를 내보내고 있습니다.
 알맹이를 내보내고 있습니다.
 축하드립니다! 알맹이를 하나 더 받으실 수 있습니다.

 주식회사 왕뽑기
 [ 최신형 뽑기 기계 ]
 남은 개수: 3개
 현재 상태: 동전 투입 대기중

 동전을 넣으셨습니다.
 손잡이를 돌리셨습니다.
 알맹이를 내보내고 있습니다.
 알맹이를 내보내고 있습니다.
 축하드립니다! 알맹이를 하나 더 받으실 수 있습니다.
 동전을 넣으셨습니다.
 손잡이를 돌리셨습니다.
 알맹이를 내보내고 있습니다.
 Oops, out of gumballs!

 주식회사 왕뽑기
 [ 최신형 뽑기 기계 ]
 남은 개수: 0개
 현재 상태: 매진

 5개의 알맹이가 리필되었습니다.

 주식회사 왕뽑기
 [ 최신형 뽑기 기계 ]
 남은 개수: 5개
 현재 상태: 동전 투입 대기중
 */
```
  </p>
</details>


## 4. 상태 패턴 
- 상태를 별도의 클래스로 캡슐화하고, 현재 상태를 나타내는 객체에게 행동을 위임합니다. 그래서 상태가 바뀔 때 행동이 달라지게 됩니다.
- 마치 객체가 바뀌는 것 같은 결과를 얻을 수 있는 것인데, 실제로 그런 것이 아니라 구성(Composition)으로 여러 상태 객체를 바꿔가면서 사용하는 거겠죠. 
- 상태가 늘어남에 따라 클래스의 개수는 늘어납니다. 유연성을 향상시키려고 지불하는 비용이라고 생각하면 됩니다. 
- 상태 패턴과 전략 패턴의 다이어그램은 똑같습니다. 하지만 사용 용도가 다르죠.
- State Pattern(상태 패턴)
    - 미리 몇 가지 상태를 가지고 작업하고, 미리 정해진 상태 전환 규칙에 따라(State 인터페이스) 알아서 자기 상태를 변경한다. 
    - 상태 객체에 일련의 행동이 캡슐화 됩니다. Context(GumballMachine) 객체의 상태에 따라 현재 상태를 나타내는 객체가 바뀌게 되고, 행동도 바뀝니다.
    - 클라이언트는 상태 객체를 몰라도 됩니다.
    - Context 내에 수많은 if 문을 넣는 대신에 상태 별로 캡슐화해서 상태를 바꿔준다고 생각하면 됩니다. 
- Strategy Pattern(전략 패턴)		
    - 어떤 클래스를 인스턴스를 만들고, 그 인스턴스에게 어떤 행동을 구현하는 전략 객체를 건네준다. 
    - 클라이언트가 Context 객체에게 어떤 전략 객체를 사용할지를 지정합니다.
    - 주로 실행 시에 전략 객체를 변경할 수 있는 유연성을 제공하는 용도로 쓰입니다. 
    - 상속을 사용해서 클래스의 행동을 정의한다면, 유연성이 떨어지기 때문에 구성을 사용해서 실행 중에 유연하게 행동을 바꾸는 용도로 쓰입니다. 
    				
![](https://velog.velcdn.com/images/dev_kickbell/post/6dfe4abf-5569-4dd1-8558-eca3ee0b3561/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/6d00f2ab-bf39-4f47-a04d-7d636aca7043/image.png)

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
//전략 패턴 
//FlyRocketPowered()라는 행동을 직접 지정해준다.
var mallardDuck = MallardDuck()
mallardDuck.performFly() //저는 날 수 있어요.
mallardDuck.performQuack() //꽥꽥

var modelDuck = ModelDuck()
modelDuck.performFly() //저는 날 수 없어요.
modelDuck.setFlyBehavior(FlyRocketPowered())
modelDuck.performFly() //저는 모형오리라 로켓파워로 날아갑니다.
    
    
//상태 패턴
//미리 정해진 상태에 따라 insertQuarter, turnCrank 같은 작업을 해주면 
//상태가 자동으로 변경된다. 
let gumballMachine = GumballMachine(5)

print(gumballMachine.description)

gumballMachine.insertQuarter()
gumballMachine.turnCrank()

print(gumballMachine.description)

gumballMachine.insertQuarter()
gumballMachine.turnCrank()
gumballMachine.insertQuarter()
gumballMachine.turnCrank()

print(gumballMachine.description)

gumballMachine.refill(5)

print(gumballMachine.description)
```
  </p>
</details>


## 5. 생각해보기 

![](https://velog.velcdn.com/images/dev_kickbell/post/bd4cabdb-204a-4862-bc6d-ed2dfd0495a5/image.png)


## Reference 
[https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223](https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223)







