# Swift로 함수형 프로그래밍 시작하기


> _이 글은 [Swift로 함수형 프로그래밍 시작하기](https://www.inflearn.com/course/swift-fp)의 내용을 공부하고, 개인적으로 정리해놓은 글입니다._


## 1. 프로그래밍 패러다임의 변화

### 패러다임 
- 한 시대의 사람들의 견해나 사고를 인식하는 체계 또는 흐름이랄까 그런 것을 패러다임이라고 하죠. 
- Low Memory 
    - 지금과는 달리 메모리가 제한적이던 옛날에 프로그래머들이 선택할 수 있는 패러다임은 최적화였습니다. 데이터를 분리하고 메모리에 직접 접근하고 중복되는 데이터를 없앴죠. 그래서 취급하는 데이터가 변경되면 프로그래밍도 변경되어야 했죠. 
- Mass Production 
    - 컴퓨터가 대중화되면서 대량 생산의 필요성이 증가했어요. 그래서 기존의 매번 코드를 재작성해야하는 패러다임은 맞지 않았습니다. 코드의 재사용의 필요성이 대두된거죠. 여기서 객체지향 프로그래밍(OOP)가 등장했습니다. 
- Concurrency
    - 요즘 시대를 생각해보죠. CPU에는 여러 개의 코어가 들어가고, 하나의 PC에 여러 개의 CPU가 장착됩니다. 하나의 프로세스로 돌아가는 프로그램도 여러 개의 쓰레드로 나눠져서 동시에 실행되죠. 
    - 따라서, 이런 시대에 맞는 패러다임이 필요했습니다. 하나의 반복된 작업을 병렬로 나눠서 처리하고, 여러 개의 작업을 동시에 수행하고, 각 작업이 끝날 때까지 멈춰서 기다리지도 않고, 각기 작업에 실행된 결과들이 수집되서 새로운 작업에 사용됩니다. 
    - 패러다임이 재사용의 관점에서 동시성의 관점으로 옮겨진겁니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/218582be-ad73-41c5-8fc6-4f142ee5b0f6/image.png)

### FP가 재조명 받는 이유 
- 결국엔 동시성(Concurrency)이 문제인거죠. 여기서 발생할 수 있는 제일 큰 문제는 이런 겁니다. 사과가 10개있고, A에서 사과 10개를 가져다가 3개를 팔고 7개를 반환합니다. 그런데 B에서 7개가 반환되기 전에 사과를 요청한거죠. 그러면 데이터의 무결성이 깨져버립니다. 
- 이런 동시성의 문제를 해결할 수 있는 방법이 OOP에도 있긴 합니다. 세마포어를 통해서 자원의 동시 접근을 제한하는거죠. 자바에서는 Synchronized 라는 키워드를 통해 자원의 동시 접근을 제한합니다. 
- 하지만 많이 불편합니다. 그리고 이것을 해결하는 간단한 방법도 있습니다. 바로 한 번 만들어진 데이터는 변경하지 않는겁니다. 하나의 데이터를 참조하지않고 데이터가 필요하면 그냥 복사하는거죠. 기존의 데이터가 변경되지 않으니 동시에 수행되는 다른 프로그램에 영향을 주지 않습니다. 
- 이렇게 프로그래밍하면 side-effect가 없고, 이런 관점이 현재 시대의 동시성 문제를 해결하는데 도움이 됩니다. 그리고 이것은 옛날부터 존재했지만 함수형 프로그래밍(FP)가 다시 주목받는 이유입니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/45b4cd09-e7f1-4801-a9ee-0fb540df6d5d/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/3de50148-3a51-4e6e-88e1-6e1670d5aa4f/image.png)


### FP에 대한 오해 
- 고차함수, 커링, 맵, 핉터, 리듀스는 프로그래밍 기법입니다. 내 프로그램에서 map, filter를 사용한다고 FP는 아니라는거죠. 
- FP의 가장 특징은 side-effect가 없다는 겁니다. 함수를 중심으로 side-effect가 없도록 하는게 FP입니다. 
- 또 FP <-> OOP는 아닙니다. OOP도 FP도 각자 당면한 문제에 따라 대두된 패러다임이죠. 그냥 지금 내가 맞이한 문제에 맞는 패러다임을 사용하면 됩니다. 둘 다 공존할 수 있는 것이죠. 
- 마지막으로 함수를 일급객체로 취급하는 언어들에서는 더 쉽겠지만, 다른 언어에서도 FP를 사용할 수 있습니다. FP는 기술이 아니라 패러다임이기 때문이지요. 
			
![](https://velog.velcdn.com/images/dev_kickbell/post/aaef34a2-6b4b-4f83-bc90-dce6d6f33208/image.png)

## 2. 함수를 다루는 기술들 

### 순수함수(Pure Function)
- 함수형 프로그램은 함수를 이용해서 프로그래밍을 하죠. 여기서 말하는 함수는 순수 함수입니다.
- 순수함수란, 어떤 input에 대해서 항상 동일한 output을 반환하는 함수에요. output은 오직 input에 의해서만 결정됩니다. 
- 그래서 side-effect가 없고 외부에 영향을 주지도, 받지도 않죠. 

```swift
//순수함수 X
//함수가 외부의 name, greeting에 영향을 받음
var name = "FP"
var greeting = ""
func makeGreeting() {
	greeting = "Hello, \(name)"
}

//순수함수 O
//name이라는 input에 의해서만 output이 결정. 순수함수로 변경
func greeting(_ name: String) -> String {
	return "Hello, \(name)"
}

//순수함수 X 
//외부값을 사용하지 않으나, 실행시마다 다른 결과를 도출하기 때문
func add(_ a: Int) -> Int {
	return Int(arc4random()) + a
}
```


### 고차함수(Higher-Order Function) 
- 함수형 프로그래밍에서는 함수를 1급객체로 취급합니다. 1급객체란 함수의 파라미터로 전달되거나 리턴값으로 사용될 수 있는 객체를 말하죠. 
- 우리가 매번 사용하는 swift의 고차함수인 map, filter, reduce와 같은 것들입니다. 

```swift
//파라미터를 (Element) throws -> Bool 이라는 함수로 받고 있음
func filter(_ isIncluded: (Element) throws -> Bool) rethrows -> [Element]

//마찬가지로 (Element) throws -> T 라는 함수를 받음 
func map<T>(_ transform: (Element) throws -> T) rethrows -> [T]

//얘도 2개의 파라미터 중 하나가 (Result, Element) throws -> Result라는 함수
func reduce<Result>(_ initialResult: Result,_ nextPartialResult: (Result, Element) throws -> Result) rethrows -> Result
```

### 합성(Composition)
- 함수의 반환값이 다른 함수의 입력값으로 사용되는 것을 함수의 합성이라고 합니다. 
- 약간, OOP에서 상속보단 구성(Composition)을... 같은 느낌의 유사한 의미를 가진 용어네요. 
- 아래의 코드처럼 f1의 리턴이 f2의 입력값으로 사용됐죠. 

```swift
func f1(_ i: Int) -> Int {
    return i * 2
}

func f2(_ i: Int) -> String {
    return "\(i)"
}

let result1 = f2(f1(100)) 
```

### 커링(Currying)
- 여러 개의 파라미터를 받는 함수를 하나의 파라미터를 받는 여러 개의 함수로 쪼개는 것을 커링이라고 합니다. 
- a와 b를 받는 f를 f1과 f2로 쪼개면 cf처럼 될 수 있죠. 리턴값을 쪼갠 f2함수로 대체할 수 있으니까요. 
- 커링을 하는 이유는 합성을 원활하게 하기 위해서입니다. 함수의 Output이 다른 함수의 Input으로 연결되면서 합성(Composition)됩니다. 함수들이 서로 chain을 이루면서 연속적으로 연결이 되려면, Output과 Input의 타입과 개수가 같아야 합니다.
- 함수의 Output은 하나밖에 없으니 Input도 모두 하나씩만 갖도록 한다면 합성하기가 쉬워질 것입니다.
 
```swift
func f(_ a: Int, _ b: Int) -> Int

func f1(_ a: Int) -> Int
func f2(_ b: Int) -> Int

func cf(_ a: Int) -> (Int) -> Int {
    return f2(_:)
}

//다른 예제 
//주어진 코드에서 n의 배수만을 모아 합을 구하는 함수를 커링
func filterSum(_ n: Int, _ ns: [Int]) -> Int {
    return ns.filter({ $0 % n == 0 }).reduce(0, +)
}

func curryingfilterSum(_ n: Int) -> ([Int]) -> Int {
    return { ns in
        return ns.filter({ $0 % n == 0 }).reduce(0, +)
    }
}

func solution(_ nums: [Int], _ r: Int) -> Int {
    let filteredR = curryingfilterSum(r)
    return filteredR(nums)
}

solution([1, 2, 3, 4, 5, 6], 2) //12
solution([1, 2, 3, 4, 5, 6], 3) //9
```


### 비동기 함수(Async Result) 
- 함수는 input에 따라 연산을 하고 output을 리턴하지만, 시간이 오래 걸리는 경우라면 프로그램이 연산하는동안 멈추겠죠. 이런 것을 동기식(sync)이라고 합니다. 
- 반대로 비동기식(async)도 있습니다. 얘는 결과는 나중에 전달 받기로 하고 프로그램의 동작은 멈추지 않는 방식이죠. 네트워킹, 오랜 연산이 걸리는 작업, 딜레이가 포함된 작업은 비동기 방식으로 작업하는 것이 좋습니다. 
- 아래의 코드는 같은 작업의 sync, async 인데요. 중요한 부분은 결과값으로 전달하는 것이 아니라 result: @escaping (Int) -> Void) 라는 함수를 호출함으로써 전달하는 거에요. 
- 이게 무슨 뜻이냐면, sum 이라는 값이 나중에 들어오면 그때 그 결과가 발생하는 시점에 sum을 result에 매개변수로 호출하겠다라는 의미죠. 

```swift
//sync
func f(_ nums: [Int]) -> Int {
    sleep(3)
    let sum = nums.reduce(0, +)
    return sum
}

//async
func af(_ nums: [Int], _ result: @escaping (Int) -> Void) {
    DispatchQueue.main.async {
        sleep(3)
        let sum = nums.reduce(0, +)
        result(sum)
    }
}
```

## 3. FizzBuzz 실습하기 
- 간단한 프로그램인 fizzbuzz로 일반적인 로직을 함수형 프로그래밍으로 바꿔보겠습니다. 
- 일단 fizz와 buzz를 클로저로 각각 정의합니다. 그리고 그것을 바탕으로 fizzbuzz 또한 새롭게 정의했죠. 
- 그리고 일반적인 로직의 가장 큰 문제점이 i 변수인데요. 외부 변수이기 때문에 side-effect가 발생합니다. 그래서 이 녀석을 loop 라는 함수로 만들어줄 거에요. input 값에만 output이 변동됩니다. 

```swift
//일반적인 로직
var i = 1
while i <= 100 {
    if i % 3 == 0 && i % 5 == 0 {
        print("fizzbuzz")
    }
    else if i % 3 == 0 {
        print("fizz")
    }
    else if i % 5 == 0 {
        print("buzz")
    }
    else {
        print(i)
    }
    
    i += 1
}

//함수형 프로그래밍
let fizz = { i in i % 3 == 0 ? "fizz" : "" }
let buzz = { i in i % 5 == 0 ? "buzz" : "" }
let fizzbuzz: (Int) -> String = { i in { a, b in b.isEmpty ? a: b }("\(i)", fizz(i) + buzz(i)) }

func loop(min: Int, max: Int, do f: (Int) -> Void) {
    Array(min...max).forEach(f)
}

loop(min: 1, max: 100, do: { print(fizzbuzz($0)) })

```

## 4. 자판기 예제 실습하기
- 코드는 총 3단계로 나누어집니다. MODEL, UI, LOGIC 입니다. 먼저 MODEL에서 Product, Input, Output, State 값을 enum으로 정의합니다. 
- 그리고 다음은 UI를 구현하는데요. [여기](https://github.com/iamchiwon/VendingMachine)에 예제 코드가 있으니 필요하시다면 참고하시면 될 것 같습니다. @IBOutlet, @IBAction 밖에는 없습니다. 
- 마지막으로 LOGIC 부분입니다. 여기서는 위에서 배웠던 커링이나 합성같은 것들이 대거 쓰였죠. 사실 이 부분이 가장 중요합니다. 
- 하나씩 살펴보면, 일단 버튼을 클릭하면 handleProcess가 동작하죠. 얘는 (String) -> Void 타입입니다. 
- 그리고 같은 타입으로 processHandler가 있죠. money 라는 상태값이 있기 때문에 state를 매개변수로 받고 있는데요. 함수형 프로그래밍에서 가장 중요시됐던게 side-effect였죠? 원래라면 이 상태값은 외부에 전역변수로 나와있었을 겁니다. 
- 그런데, 지금은 handleProcess("100"), handleProcess("cola") 처럼 입력되면 String 값이 processHandler 내의 command로 넘어오죠. 이 command를 바탕으로 uiInput에서는 Input enum을 리턴하고 uiOutput에서는 레이블이나 이미지를 보여준다는 식의 이벤트를 처리합니다. 
- 또, operation 메소드에서 핵심은 processHandler에서 state를 받아서 적절하게 계산을 해준뒤에 그것을 다시 리턴한다는 겁니다. 
- 이렇게 하면, state라는 값이 processHandler라는 함수내에 갇혀있게 됩니다. state는 함수내에 갇혀있지만, 함수가 수행됨에 따라서 계속 갱신되고 저장될 수 있죠. 이런 기법을 메모이제이션(Memoization)이라고 합니다. 메모이제이션을 통해서 side-effect를 없앨 수 있게 되었습니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/bce0dd19-79a1-4cd0-b3b0-dd89601fcf76/image.png)


<details>
  <summary><a href=""></a>MODEL</summary>
  <p>

```swift
// MARK: MODEL

enum Product: Int {
    case cola = 1000
    case cider = 1100
    case fanta = 1200
    func name() -> String {
        switch self {
        case .cola: return "콜라"
        case .cider: return "사이다"
        case .fanta: return "환타"
        }
    }
}

enum Input {
    case inputMoney(Int)
    case selectProduct(Product)
    case reset
    case none
}

enum Output {
    case displayMoney(Int)
    case buyProduct(Product)
    case notEnoughMoneyError
    case returnMoney(Int)
}

struct State {
    let money: Int
    static func initial() -> State {
        return State(money: 0)
    }
}
```
  </p>
</details>

<details>
  <summary><a href=""></a>UI</summary>
  <p>

```swift
    // MARK: UI
        
    @IBOutlet weak var displayMoney: UILabel!
    
    @IBOutlet weak var productOut: UIImageView!
    
    @IBOutlet weak var textInfo: UILabel!
    
    @IBAction func money100(_ sender: Any) {
        handleProcess("100")
    }
    
    @IBAction func money500(_ sender: Any) {
        handleProcess("500")
    }
    
    @IBAction func money1000(_ sender: Any) {
        handleProcess("1000")
    }
    
    @IBAction func selectCola(_ sender: Any) {
        handleProcess("cola")
    }
    
    @IBAction func selectCider(_ sender: Any) {
        handleProcess("cider")
    }
    
    @IBAction func selectFanta(_ sender: Any) {
        handleProcess("fanta")
    }
    
    @IBAction func reset(_ sender: Any) {
        handleProcess("reset")
    }
```
  </p>
</details>

<details>
  <summary><a href=""></a>LOGIC</summary>
  <p>

```swift
    
    // MARK: LOGIC
    
    lazy var handleProcess: (String) -> Void = processHandler(State.initial())
    
    func processHandler(_ initState: State) -> (String) -> Void {
        var state = initState
        return { command in
            state = self.operation(self.uiInput(command), self.uiOutput)(state)
        }
    }
    
    func uiInput(_ command: String) -> () -> Input {
        return {
            switch command {
            case "100": return .inputMoney(100)
            case "500": return .inputMoney(500)
            case "1000": return .inputMoney(1000)
            case "cola": return .selectProduct(.cola)
            case "cider": return .selectProduct(.cider)
            case "fanta": return .selectProduct(.fanta)
            case "reset": return .reset
            default: return .none
            }
        }
    }
    
    func uiOutput(_ outp: Output) {
        switch outp {
        case .displayMoney(let m):
            displayMoney.text = "\(m)"
        case .buyProduct(let p):
            switch p {
            case .cola:
                productOut.image = #imageLiteral(resourceName: "coke")
            case .cider:
                productOut.image = #imageLiteral(resourceName: "cider")
            case .fanta:
                productOut.image = #imageLiteral(resourceName: "fanta")
            }
            DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
                self.productOut.image = nil
            }
        case .notEnoughMoneyError:
            textInfo.text = "잔액이 부족합니다."
            DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
                self.textInfo.text = ""
            }
        case .returnMoney(let m):
            textInfo.text = "\(m)"
            DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
                self.textInfo.text = ""
            }
        }
    }
    
    func operation(_ inp: @escaping () -> Input,_ outp: @escaping (Output) -> ()) -> (State) -> State {
        return { state in
            let input = inp()
            switch input {
            case .inputMoney(let m):
                let money = state.money + m
                outp(.displayMoney(money))
                return State(money: money)
            case .selectProduct(let p):
                if state.money < p.rawValue {
                    outp(.notEnoughMoneyError)
                    return state
                }
                outp(.buyProduct(p))
                let money = state.money - p.rawValue
                outp(.displayMoney(money))
                return State(money: money)
            case .reset:
                outp(.returnMoney(state.money))
                outp(.displayMoney(0))
                return State(money: 0)
            case .none:
                return state
            }
        }
    }
    
```
  </p>
</details>


<details>
  <summary><a href=""></a>TOTAL</summary>
  <p>

```swift

import UIKit

class ViewController: UIViewController {
    
    // MARK: MODEL
    
    enum Product: Int {
        case cola = 1000
        case cider = 1100
        case fanta = 1200
        func name() -> String {
            switch self {
            case .cola: return "콜라"
            case .cider: return "사이다"
            case .fanta: return "환타"
            }
        }
    }
    
    enum Input {
        case inputMoney(Int)
        case selectProduct(Product)
        case reset
        case none
    }
    
    enum Output {
        case displayMoney(Int)
        case buyProduct(Product)
        case notEnoughMoneyError
        case returnMoney(Int)
    }
    
    struct State {
        let money: Int
        static func initial() -> State {
            return State(money: 0)
        }
    }
    
    // MARK: UI
        
    @IBOutlet weak var displayMoney: UILabel!
    
    @IBOutlet weak var productOut: UIImageView!
    
    @IBOutlet weak var textInfo: UILabel!
    
    @IBAction func money100(_ sender: Any) {
        handleProcess("100")
    }
    
    @IBAction func money500(_ sender: Any) {
        handleProcess("500")
    }
    
    @IBAction func money1000(_ sender: Any) {
        handleProcess("1000")
    }
    
    @IBAction func selectCola(_ sender: Any) {
        handleProcess("cola")
    }
    
    @IBAction func selectCider(_ sender: Any) {
        handleProcess("cider")
    }
    
    @IBAction func selectFanta(_ sender: Any) {
        handleProcess("fanta")
    }
    
    @IBAction func reset(_ sender: Any) {
        handleProcess("reset")
    }
    
    
    // MARK: LOGIC
    
    //변수 아니라 함수다. (String) -> Void
    lazy var handleProcess = processHandler(State.initial())
    
    //이 함수는 커맨드를 Input으로 받고 처리하는 함수를 리턴한다.
    //기존에 밖에있던 state값을 없애고, 이 함수에 내장된 형태로 state값을 집어넣었다.
    //state는 함수내에 갇혀있지만 함수가 수행됨에 따라서 계속 갱신되고 저장된다.
    //이러한 기법을 memoization 이라고 한다. memoization 을 통해서 side effect을 없애고 함수형으로
    //state를 처리할 수 있도록 구현되었다.
    //https://ko.wikipedia.org/wiki/%EB%A9%94%EB%AA%A8%EC%9D%B4%EC%A0%9C%EC%9D%B4%EC%85%98
    
    func processHandler(_ initState: State) -> (String) -> Void {
        var state = initState
        return { command in
            state = self.operation(self.uiInput(command), self.uiOutput)(state)
        }
    }
    
    func uiInput(_ command: String) -> () -> Input {
        return {
            switch command {
            case "100": return .inputMoney(100)
            case "500": return .inputMoney(500)
            case "1000": return .inputMoney(1000)
            case "cola": return .selectProduct(.cola)
            case "cider": return .selectProduct(.cider)
            case "fanta": return .selectProduct(.fanta)
            case "reset": return .reset
            default: return .none
            }
        }
    }
    
    func uiOutput(_ outp: Output) {
        switch outp {
        case .displayMoney(let m):
            displayMoney.text = "\(m)"
        case .buyProduct(let p):
            switch p {
            case .cola:
                productOut.image = #imageLiteral(resourceName: "coke")
            case .cider:
                productOut.image = #imageLiteral(resourceName: "cider")
            case .fanta:
                productOut.image = #imageLiteral(resourceName: "fanta")
            }
            DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
                self.productOut.image = nil
            }
        case .notEnoughMoneyError:
            textInfo.text = "잔액이 부족합니다."
            DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
                self.textInfo.text = ""
            }
        case .returnMoney(let m):
            textInfo.text = "\(m)"
            DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
                self.textInfo.text = ""
            }
        }
    }
    
    func operation(_ inp: @escaping () -> Input,_ outp: @escaping (Output) -> ()) -> (State) -> State {
        return { state in
            let input = inp()
            switch input {
            case .inputMoney(let m):
                let money = state.money + m
                outp(.displayMoney(money))
                return State(money: money)
            case .selectProduct(let p):
                if state.money < p.rawValue {
                    outp(.notEnoughMoneyError)
                    return state
                }
                outp(.buyProduct(p))
                let money = state.money - p.rawValue
                outp(.displayMoney(money))
                return State(money: money)
            case .reset:
                outp(.returnMoney(state.money))
                outp(.displayMoney(0))
                return State(money: 0)
            case .none:
                return state
            }
        }
    }
    
}    
```
  </p>
</details>

## Reference

[https://www.inflearn.com/course/swift-fp](https://www.inflearn.com/course/swift-fp)  


