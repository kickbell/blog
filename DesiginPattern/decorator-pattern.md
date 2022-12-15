# Decorator Pattern(데코레이터 패턴)
> - 객체에 추가 요소를 동적으로 더할 수 있습니다. 
> - 데코레이터를 사용하면 서브클래스를 만들 때보다 훨씬 유연하게 기능을 확장할 수 있습니다. 

## 1. 스타벅스 메뉴를 만듭니다. 
- 처음엔 Beverage(음료) 인터페이스를 통해 하우스블렌드, 다크로스트 원두와 디카페인 그리고 에스프레소라는 큰 종류로 나뉘었어요. 
- 그런데, 우유/데운우유/두유/휘핑크림/모카...와 같은 메뉴 추가사항이 늘어납니다. 이때 추가된 메뉴별로 비용이 추가되기 때문에 여기서 상속을 사용하면 서브클래스가 폭발하게 됩니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/3189eaf0-8db8-43c7-970f-cb6a276bf77c/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/1387d242-a9f8-4e47-82cc-f81eeb69b26c/image.png)

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
protocol Beverage {
    var description: String { get }
    
    func getDescription() -> String
    func cost() -> Double
}

class HouseBlend: Beverage {
    let description: String = "하우스 블렌드 원두"
    
    func getDescription() -> String {
        return description
    }
    
    func cost() -> Double {
        return 2.49
    }
}

class DarkRoast: Beverage {
    let description: String = "다크 로스트 원두"
    
    func getDescription() -> String {
        return description
    }
    
    func cost() -> Double {
        return 2.49
    }
}

class Decaf: Beverage {
    let description: String = "디카페인"
    
    func getDescription() -> String {
        return description
    }
    
    func cost() -> Double {
        return 2.29
    }
}

class Espresso: Beverage {
    let description: String = "에스프레소"
    
    func getDescription() -> String {
        return description
    }
    
    func cost() -> Double {
        return 1.99
    }
}
```
```swift
/*
 milk 우유
 steamed milk 데운 우유
 soy milk 두유
 whip 휘핑크림
 mocha 모카
 ...
 */
class HouseBlendWithMilk: Beverage { ... }
class HouseBlendWithSteamedMilk: Beverage { ... }
class HouseBlendWithSoy: Beverage { ... }
class HouseBlendWithMocha: Beverage { ... }
class HouseBlendWithWhip: Beverage { ... }
class HouseBlendWithMilkandSoy: Beverage { ... }
class HouseBlendWithSteamedMilkandWhip: Beverage { ... }
class HouseBlendWithSoyandWhip: Beverage { ... }
class HouseBlendWithMochaandWhip: Beverage { ... }
class HouseBlendWithWhipandMilkandSoy: Beverage { ... }

class DarkRoastWithMilk: Beverage { ... }
class DarkRoastWithSteamedMilk: Beverage { ... }
class DarkRoastWithSoy: Beverage { ... }
class DarkRoastWithMocha: Beverage { ... }
class DarkRoastWithWhip: Beverage { ... }
class DarkRoastWithMilkandSoy: Beverage { ... }
class DarkRoastWithSteamedMilkandWhip: Beverage { ... }
class DarkRoastWithSoyandWhip: Beverage { ... }
class DarkRoastWithMochaandWhip: Beverage { ... }
class DarkRoastWithWhipandMilkandSoy: Beverage { ... }

class EspressoWithMilk: Beverage { ... }
class EspressoWithSteamedMilk: Beverage { ... }
class EspressoWithSoy: Beverage { ... }
class EspressoWithMocha: Beverage { ... }
class EspressoWithWhip: Beverage { ... }
class EspressoWithMilkandSoy: Beverage { ... }
class EspressoWithSteamedMilkandWhip: Beverage { ... }
class EspressoWithSoyandWhip: Beverage { ... }
class EspressoWithMochaandWhip: Beverage { ... }
class EspressoWithWhipandMilkandSoy: Beverage { ... }

class DecafWithMilk: Beverage { ... }
class DecafWithSteamedMilk: Beverage { ... }
class DecafWithSoy: Beverage { ... }
class DecafWithMocha: Beverage { ... }
class DecafWithWhip: Beverage { ... }
class DecafWithMilkandSoy: Beverage { ... }
class DecafWithSteamedMilkandWhip: Beverage { ... }
class DecafWithSoyandWhip: Beverage { ... }
class DecafWithMochaandWhip: Beverage { ... }
class DecafWithWhipandMilkandSoy: Beverage { ... }

```
  </p>
</details>

## 2. 변수와 상속을 사용해서 변경해보기
- 부모 클래스에 각 추가옵션의 Bool 타입을 정의하고, get/set 메소드 또한 정의합니다.
- 해당 Bool 타입의 여부에 따라서 cost() 메소드를 통해 비용을 추가해줍니다. 
- 자식 클래스에서는 본인 메뉴의 비용 + super.cost()를 호출하여 비용을 추가합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/681b7e16-a922-4037-bbbb-92d8824d7d54/image.png)

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class Beverage {
    private var description: String
    private var milk: Bool = false
    private var soy: Bool = false
    private var mocha: Bool = false
    private var whip: Bool = false
    
    init(description: String) {
        self.description = description
    }
    
    func getDescription() -> String {
        self.description
    }
    
    private func hasMilk() -> Bool { self.milk }
    private func hasSoy() -> Bool { self.soy }
    private func hasMocha() -> Bool { self.mocha }
    private func hasWhip() -> Bool { self.whip }
    
    func setMilk(_ b: Bool) { self.milk = b }
    func setSoy(_ b: Bool) { self.soy = b }
    func setMocha(_ b: Bool) { self.mocha = b }
    func setWhip(_ b: Bool) { self.whip = b }
    func cost() -> Double {
        var condimentCost: Double = 0.0 //첨가물 비용
        let milkCost: Double = 2.0
        let soyCost: Double = 3.0
        let mochaCost: Double = 2.5
        let whipCost: Double = 1.0
        
        if hasMilk() { condimentCost += milkCost }
        if hasSoy() { condimentCost += soyCost }
        if hasMocha() { condimentCost += mochaCost }
        if hasWhip() { condimentCost += whipCost }
        
        return condimentCost
    }
    
}
```
    
```swift
class HouseBlend: Beverage {
    override func cost() -> Double {
        return 2.49 + super.cost()
    }
}

class DarkRoast: Beverage {
    override func cost() -> Double {
        return 2.49 + super.cost()
    }
}

class Decaf: Beverage {
    override func cost() -> Double {
        return 2.29 + super.cost()
    }
}

class Espresso: Beverage {
    override func cost() -> Double {
        return 1.99 + super.cost()
    }
}
```
    
```swift
let cafelatte = Espresso(description: "카페라떼")
cafelatte.setMilk(true)

//에스프레소 1.99 + 우유 2.0
print(cafelatte.getDescription(), cafelatte.cost())

let cafeMocha = Espresso(description: "카페모카")
cafeMocha.setMocha(true)
cafeMocha.setWhip(true)

//에스프레소 1.99 + 모카 2.5 + whip 1.0
print(cafeMocha.getDescription(), cafeMocha.cost())
    
    
/*
카페라떼 3.99
카페모카 5.49
*/
```
  </p>
</details>

## 3. 상속을 사용한 설계의 단점 알아보기 
- 첨가물 가격이 바뀔 때마다 기존 코드를 수정해야 합니다.
- 첨가물의 종류가 많아지면 새로운 get/set 메소드를 추가해야 하고, 부모클래스의 cost() 메소드 또한 고쳐야 합니다. 
- 새로운 음료가 나올 수 있어요. 아이스티같은. 근데 아이스티는 hasWhip()같은 메소드는 상속받지 않아야 하지만, 상속을 받을 것입니다. 이건 Strategy Pattern을 공부할 때도 말했듯이, 고무 오리가 날아다닌다던지 하는 예상치 못한 치명적인 오류를 발생시킬 수 있어요. 
- 고객이 더블 모카를 주문하면 어떻게 하나요 ? ( 현재는 hasMocha()로만 작업되어있으므로 주문 불가하죠. ) 
- 결론적으로 우리의 목표는 기존 코드는 건드리지 않고, 확장으로만 새로운 행동을 추가하는 것입니다. 

## 4. 설계하기 1
- Decorator(장식)의 슈퍼클래스는 자신이 장식하고 있는 객체의 슈퍼클래스와 같습니다. 
- 한 객체를 여러 개의 데코레이터로 감쌀 수 있습니다. 
- Decorator(장식)는 자신이 감싸고 있는 객체와 같은 슈퍼클래스를 가지고 있기에 원래 객체가 들어갈 자리에 데코레이터 객체를 넣어도 상관없습니다.
- Decorator(장식)는 자신이 장식하고 있는 객체에게 어떤 행동을 위임하는 일 말고도 추가 작업을 수행할 수 있습니다. 
- 객체는 언제든지 감쌀 수 있으므로 실행 중에 필요한 데코레이터를 마음대로 적용할 수 있습니다. 
			
![](https://velog.velcdn.com/images/dev_kickbell/post/217b4137-28a8-4cf8-ac7a-342f4b48bd03/image.png)

## 5. 설계하기 2
- 이해를 돕기위해 다이어그램을 참고하자. 
- 중요한 CondimentDecorator에서 Beverage를 상속하고 있다는 점과 구성(Composition)으로 Beverage를 가지고 있다는 점이다. 
- 위에서 상속 안좋다고 그러고 왜 상속을 쓰냐고 할 수도 있다만 이건 다른 너낌이다. 왜냐면 이따가 코드를 보면 알겠지만, Berverage 객체를 생성하고 그 녀석을 Decorator(장식)로 감싸서 할당해주기 때문이다. 
- 즉, 기존의 상속처럼 부모 클래스의 행동을 상속하기 위한 상속이 아니라 생성한 Berverage 객체에 데코레이터로 감싼 객체가 들어가야되서 상속을 받고 있는 것이다. 
- 그리고 또 다이어그램을 보면 있는 우유, 모카, 두유, 휘핑크림같은 Decorator(장식)들이 CondimentDecorator를 상속한다. 
- 이건 무슨의미냐면, CondimentDecorator는 부모 클래스인 Berverage 객체를 구성으로 가지고 있다. 그리고 메뉴 주문에 따라 모카로 래핑, 모카 또 래핑, 휘핑 래핑, 우유 래핑... 같은 식으로 계속 래핑이 되는데 최종 비용을 계산하려면 Berverage 객체가 필요하다. 
- 자세한 내용은 아래의 구현된 코드를 보자. 

![](https://velog.velcdn.com/images/dev_kickbell/post/a7c7eba5-448e-488c-a9de-ebb125231a90/image.png)


## 6. 구현하기 
- Beverage 클래스가 있고, 서브 클래스로 HouseBlend, DarkRoast, Decaf, Espresso가 있다. 이 클래스들은 내부에 description, cost() 이 구현되어 있다. 
- CondimentDecorator 는 Beverage 을 상속하며, Beverage 객체를 Composition(구성) 으로 가지고 있다. 따라서 Beverage 혹은 Beverage 의 서브 클래스들에 접근이 가능하다. 
- Decorator(장식)들은 모카, 두유, 우유, 휘핑크림이 있는데 CondimentDecorator를 상속하고 있고 생성자를 통해 Beverage 를 받는다. 
- 또, Decorator(장식)들은 getDescription(), cost()을 Beverage 객체를 통해서 누적으로 계산할 수 있다. 

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class Beverage {
    var description: String = "제목 없음"
    
    func getDescription() -> String {
        return self.description
    }
    
    func cost() -> Double {
        return 0.0
    }
}

class CondimentDecorator: Beverage {
    var beverage = Beverage()
}

class HouseBlend: Beverage {
    override init() {
        super.init()
        description = "하우스 블렌드 원두"
    }
    
    override func cost() -> Double {
        return 2.49
    }
}

class DarkRoast: Beverage {
    override init() {
        super.init()
        description = "다크 로스트 원두"
    }
    
    override func cost() -> Double {
        return 2.49
    }
}

class Decaf: Beverage {
    override init() {
        super.init()
        description = "디카페인"
    }
    
    override func cost() -> Double {
        return 2.29
    }
}

class Espresso: Beverage {
    override init() {
        super.init()
        description = "에스프레소"
    }
    
    override func cost() -> Double {
        return 1.99
    }
}

```
    
```swift
class Mocha: CondimentDecorator {
    init(beverage: Beverage) {
        super.init()
        self.beverage = beverage
    }
    
    override func getDescription() -> String {
        return beverage.getDescription() + ", 모카"
    }
    
    override func cost() -> Double {
        return beverage.cost() + 2.5
    }
}

class Soy: CondimentDecorator {
    init(beverage: Beverage) {
        super.init()
        self.beverage = beverage
    }
    
    override func getDescription() -> String {
        return beverage.getDescription() + ", 두유"
    }
    
    override func cost() -> Double {
        return beverage.cost() + 3.0
    }
}

class Milk: CondimentDecorator {
    init(beverage: Beverage) {
        super.init()
        self.beverage = beverage
    }
    
    override func getDescription() -> String {
        return beverage.getDescription() + ", 우유"
    }
    
    override func cost() -> Double {
        return beverage.cost() + 2.0
    }
}

class Whip: CondimentDecorator {
    init(beverage: Beverage) {
        super.init()
        self.beverage = beverage
    }
    
    override func getDescription() -> String {
        return beverage.getDescription() + ", 휘핑크림"
    }
    
    override func cost() -> Double {
        return beverage.cost() + 1.0
    }
}
```
```swift
/*
 <메뉴판>
 
 [메인메뉴]
 2.49 하우스블렌드
 2.49 다크로스트
 2.29 디카페인
 1.99 에스프레소
 
 [첨가물]
 2.0 우유
 3.0 두유
 2.5 모카
 1.0 휘핑크림
 
 */

var beverage: Beverage = Espresso()
print("\(beverage.getDescription()) $\(beverage.cost())")
//에스프레소 $1.99

var beverage2: Beverage = DarkRoast()
beverage2 = Mocha(beverage: beverage2)
beverage2 = Mocha(beverage: beverage2)
beverage2 = Whip(beverage: beverage2)
print("\(beverage2.getDescription()) $\(beverage2.cost())")
//다크 로스트 원두, 모카, 모카, 휘핑크림 $8.49

var beverage3: Beverage = HouseBlend()
beverage3 = Soy(beverage: beverage3)
beverage3 = Mocha(beverage: beverage3)
beverage3 = Whip(beverage: beverage3)
print("\(beverage3.getDescription()) $\(beverage3.cost())")
//하우스 블렌드 원두, 두유, 모카, 휘핑크림 $8.99
```
  </p>
</details>

## 7. 정리하기 
- 아래의 코드는 아래의 그림을 코드로 구현해서 실행하는 부분이다.
- 그림을 보면 계속 모카, 휘핑 크림 추가 처럼 첨가물을 계속 실행 중에 추가할 수 있게 되어 있다. 
- 왜? 데코레이터가 Berverage를 상속해서 타입이 같고, 모카/우유 애들은 CondimentDecorator를 상속하고 있으니까 그렇다. 즉, 상속을 행동을 받으려고 한 것이 아니라 타입을 맞추려고 한 것이다. 
- 또, 생성자로 구성으로 가지고 있는 beverage을 입력받아 기존에 래핑 되어있는 데이터에 접근이 가능해서 그 값에 추가해서 최종결과를 리턴해준다. 
- 이런식이면, 예를들어 첨가물이 새롭게 추가된다고 했을 때 기존 코드는 건드리지 않고 데코레이터를 상속한 첨가물을 새로 만들면 된다. 그래서 실행 중에 마음대로 조합해서 사용할 수 있다. 
- 즉, 목표대로 기존 코드는 건드리지 않고(변경에는 닫혀있고), 새로운 행동을 추가(확장에는 열려있다)할 수 있는 것이다.
- 유의할 점은 아래의 코드에 나와 있는 것 처럼 한 번 DarkRoast를 Beverage로 감싸고 나면, 그게 DarkRoast인지 뭔지 알 수가 없다는 부분이다. 			
			
![](https://velog.velcdn.com/images/dev_kickbell/post/04a366b3-651e-41ee-a1f3-c345dfe58cd4/image.png)


<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
var darkroast: Beverage = DarkRoast()
darkroast = Mocha(beverage: darkroast)
darkroast = Whip(beverage: darkroast)
print("\(darkroast.getDescription()) $\(darkroast.cost())")
//다크 로스트 원두, 모카, 휘핑크림 $5.99
```
  </p>
</details>


## 8. 커피 메뉴에 사이즈 추가하기 
- 커피에 Tall, Grande, Venti 사이즈가 새로 생겼다. 
- 그리고 사이즈에 따라 첨가물도 그만큼 양이 추가될 것이므로 첨가물 가격도 다르게 받으려고 한다. 

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class Beverage {
    enum Size { case TALL, GRANDE, VENTI }
    var size: Size = .TALL
    var description: String = "제목 없음"
    
    func getDescription() -> String {
        return self.description
    }
    
    func cost() -> Double {
        return 0.0
    }
    
    func setSize(_ size: Size) {
        self.size = size
    }
    
    func getSize() -> Size {
        return self.size
    }
}
    
class CondimentDecorator: Beverage {
    var beverage = Beverage()
    
    override func getSize() -> Beverage.Size {
        return beverage.getSize()
    }
}
    
class Whip: CondimentDecorator {
    init(beverage: Beverage) {
        super.init()
        self.beverage = beverage
    }
    
    override func getDescription() -> String {
        return beverage.getDescription() + ", 휘핑크림"
    }
    
    override func cost() -> Double {
        var cost = beverage.cost()
        switch beverage.getSize() {
        case .TALL: cost += 1.0
        case .GRANDE: cost += 1.5
        case .VENTI: cost += 2.0
        }
        return cost
    }
}
    
```
    
```swift
var darkroast: Beverage = DarkRoast()
darkroast = Mocha(beverage: darkroast)
darkroast = Whip(beverage: darkroast)
print("\(darkroast.getDescription()) $\(darkroast.cost())")
//다크 로스트 원두, 모카, 휘핑크림 $5.99

var ventiDarkroast: Beverage = DarkRoast()
ventiDarkroast.setSize(.VENTI)
ventiDarkroast = Mocha(beverage: ventiDarkroast)
ventiDarkroast = Whip(beverage: ventiDarkroast)
print("\(ventiDarkroast.getDescription()) $\(ventiDarkroast.cost())")
//다크 로스트 원두, 모카, 휘핑크림 $6.99
```
  </p>
</details>


## Reference 
[https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223](https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223)



 

