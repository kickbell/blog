
# Adapter, Facade Pattern(어댑터, 퍼사드 패턴)
### Adapter Pattern
> - 특정 클래스 인터페이스를 클라이언트에서 요구하는 다른 인터페이스로 변환합니다. 인터페이스가 호환되지 않아 같이 쓸 수 없었던 클래스를 사용할 수 있게 도와줍니다. 

### Facade Pattern
> - 서브시스템에 있는 일련의 인터페이스를 모아서 사용하기 쉽게 통합 인터페이스로 묶어줍니다. 
> - facade: 겉모양, 외관 

## 1. 오리와 칠면조 어댑터 구현하기 
- 외주넣은 업체에서 제공한 클래스 라이브러리를 사용해야 합니다. 근데 그 인터페이스가 기존에 사용하던 인터페이스와 다른 상황이 생깁니다. 
- 하지만, 기존에 사용하던 코드는 바꿀 수가 없는 상황이에요. 괜히 건드렸다가 무슨 사이드이펙트가 발생할 지 모르는 것이죠. 
- 이럴 때, 외주 받은 클래스 라이브러리를 기존 인터페이스에 적응시켜주는 클래스를 만드는 것이 어댑터 패턴입니다. 
- 아래 코드를 보면, MallardDuck은 Duck 프로토콜을 준수하는 클래스이고 WildTurkey는 Turkey를 준수하는 전혀 다른 인터페이스 입니다. 
- 하지만, Duck을 준수하는 TurkeyAdapter 클래스에서 turkey 인스턴스를 구성(Composition)으로 가지고 있고, 그 인스턴스를 통해 메소드를 실행하고 있죠. 물론 그 반대도 가능합니다.		
			
![](https://velog.velcdn.com/images/dev_kickbell/post/9f4c8248-50ed-4873-81a5-769c0cad2103/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/0076c5b4-a675-4f24-a8ab-af8143c4f75a/image.png)

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
protocol Duck {
    func quack()
    func fly()
}

class MallardDuck: Duck {
    func quack() {
        print("꽥")
    }
    
    func fly() {
        print("날고 있어요!")
    }
}

//칠면조
protocol Turkey {
    func gobble() //꽥이 아니라 골골골~
    func fly() //칠면조는 날긴 날지만, 오리처럼은 잘 못납니다.
}

class WildTurkey: Turkey {
    func gobble() {
        print("골골")
    }
    
    func fly() {
        print("짧은 거리를 날고 있어요!")
    }
}
```
```swift
class DuckAdapter: Turkey {
    let duck: Duck
    
    init(_ duck: Duck) {
        self.duck = duck
    }
    
    func gobble() {
        duck.quack()
    }
    
    func fly() {
        duck.fly()
    }
}

class TurkeyAdapter: Duck {
    let turkey: Turkey
    
    init(_ turkey: Turkey) {
        self.turkey = turkey
    }
    
    func quack() {
        turkey.gobble()
    }
    
    func fly() {
        (0...5).forEach { _ in
            turkey.fly()
        }
    }
}
```
```swift
import Foundation

let duck = MallardDuck()
let turkey = WildTurkey()

let turkeyAdapter: Duck = TurkeyAdapter(turkey)
let duckAdapter: Turkey = DuckAdapter(duck)

print("\n--- 칠면조가 말하길 ---")
turkey.gobble()
turkey.fly()

print("\n--- 오리가 말하길 ---")
duck.quack()
duck.fly()

print("\n--- 칠면조 어댑터가 말하길 ---")
//TurkeyAdapter 클래스지만, Duck 프로토콜을 준수, 다형성
turkeyAdapter.quack()
turkey.fly()

print("\n--- 오리 어댑터가 말하길 ---")
duckAdapter.gobble()
duckAdapter.fly()

/*
 --- 칠면조가 말하길 ---
 골골
 짧은 거리를 날고 있어요!

 --- 오리가 말하길 ---
 꽥
 날고 있어요!

 --- 칠면조 어댑터가 말하길 ---
 골골
 짧은 거리를 날고 있어요!

 --- 오리 어댑터가 말하길 ---
 꽥
 날고 있어요!
 */
```
  </p>
</details>



## 2. 어댑터 패턴 
- 어댑터(TurkeyAdapter)에서 타깃 인터페이스(Duck)를 구현합니다. 
- 어댑터(TurkeyAdapter)는 어댑티(Turkey) 인터페이스의 인스턴스를 구성(Composition)으로 가지고 있지요. 이런 접근법은 어댑티(Turkey) 인터페이스의 모든 서브 클래스에 어댑터(TurkeyAdapter)를 쓸 수 있다는 장점이 있습니다.
- 그 말은, 나중에 어댑티(Turkey) 인터페이스를 준수하는 서브 클래스를 추가해도 호환된다는 뜻입니다. 
- 어댑터 패턴은 타깃 인터페이스가 크다면 그에 비례해서 복잡해집니다. 코드를 엄청나게 많이 고쳐야 된다는 소리죠. 그냥 모든 변경 사항을 캡슐화할 클래스 하나만 제공하는 방법도 고려해보면 좋습니다. 
- 대부분은 어댑터는 하나의 클래스만 감싸지만, 그렇지 않은 경우도 있습니다. 
- 코드에 오래된 인터페이스와 새로만든 인터페이스가 있어서 한쪽만 어댑터를 사용해야 되나 고민된다면, 두 인터페이스를 모두 지원하는 다중 어댑터(Two Way Adapter)를 만들면 됩니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/3962652a-6548-4a40-b4ff-2797a8d008af/image.png)


## 3. 객체 어댑터와 클래스 어댑터
- 클래스 어댑터 
    - 다중 상속(Multiple Inheritance)을 사용합니다. 다중 상속을 지원하는 언어에서만 사용 가능합니다. 
    - 인터페이스를 준수하는 어댑터 클래스를 만드는 객체 어댑터와는 다르게 인터페이스가 아닌 MallardDuck과 WildTurkey 클래스 2개를 다중 상속해서 어댑터 클래스를 만듭니다.
    - 그렇기 때문에, 특정 어댑티 클래스에만 적용할 수 있다는 단점이 있습니다. 하지만 서브클래스이기 때문에 어댑티의 행동을 오버라이드 할 수 있는 장점이 있습니다. 
    - 상속을 사용하므로 코드 분량은 상대적으로 적습니다.  
- 객체 어댑터 
    - 구성(Composition)을 사용합니다. 
    - 구성으로 어댑티에 요청을 전달합니다.
    - 구성을 사용하므로 어댑터 클래스와 그 서브클래스들에 대해서도 어댑터 역할이 가능합니다.	
    - 어댑터 코드에 어떤 행동을 추가하면 어댑티와 모든 서브클래스에 그대로 적용되기 때문에 상대적으로 유연성이 뛰어납니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/6e5bff51-a4f2-4f2e-b0c7-c06304fd5e3c/image.png)

## 4. Enumeration을 Iterator에 적응시키기
- 둘 다 자바의 인터페이스인데, Enumeration이 구형 Iterator가 신형이랍니다. iOS도 objc와 swift가 있으니 대충 그런가보다 하고 이해하면 되겠네요. 
- hasNext()는 hasMoreElements()로, next()는 nextElement()로 대응됩니다. 
- 하지만 Iterator의 remove()는 Enumeration에 해당하는 기능을 제공하지 않습니다. 이처럼 메소드가 일대일로 대응되지 않는 상황에서는 어댑터 차원에서 완벽한 구현 방법은 없습니다. 그나마 가장 좋은 방법은 런타임 예외를 던지는 것입니다. 자바에서는 UnsupportedOperationException이라는 게 있다고 합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/818e131c-6d8f-4057-a6db-9917647c5124/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/de27abc9-e9fa-4e60-8e70-3128d76d5b3d/image.png)

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
protocol Iterator {
    associatedtype Item //타입은 나중에 결정할게!
    func hasNext() -> [Item]
    func next() -> Item
    func remove()
}
    
//원래는 얘에도 타입이 지정되어야 하는데, 구성으로 해버리면 Swift에서는 컴파일 에러가 발생.
protocol Enumeration {
    func hasMoreElements() -> [String]
    func nextElement() -> String
}
    
class EnumerationIterator {
    let enumeration: Enumeration
    
    init(enumeration: Enumeration) {
        self.enumeration = enumeration
    }

    typealias Item = String //String 타입으로 리턴하기로 결정 !

    func hasNext() -> [String] {
        return enumeration.hasMoreElements()
    }

    func next() -> String {
        return enumeration.nextElement()
    }

    func remove() {
        fatalError("지원하지 않는 메소드입니다.")
    }
}
```
  </p>
</details>

## 5. 데코레이터, 어댑터, 퍼사드 패턴 
- 데코레이터 패턴 
    - 여러개의 클래스를 감쌀 수 있습니다.
    - 인터페이스는 바꾸지 않고 데코레이터로 감싸질 때 마다 기능/책임을 추가하는 용도로 사용된다. 
- 어댑터 패턴
    - 여러개의 클래스를 감쌀 수 있습니다.
    - 인터페이스를 다른 인터페이스로 용도로 사용된다. 
- 퍼사드 패턴 
    - 여러개의 클래스를 감쌀 수 있습니다.
    - 복잡한 인터페이스를 단순하게 만드는 용도로 사용된다.

![](https://velog.velcdn.com/images/dev_kickbell/post/213574a8-1ed6-49a9-99ea-75b85e04d03b/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/1178c1d2-00da-4b85-98aa-a0af65104cfa/image.png)


<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
//데코레이터 패턴 
var darkroast: Beverage = DarkRoast()
darkroast = Mocha(beverage: darkroast)
darkroast = Whip(beverage: darkroast)
print("\(darkroast.getDescription()) $\(darkroast.cost())")
/*
 다크 로스트 원두, 모카, 휘핑크림 $5.99
 */
    

//어댑터 패턴
let duck = MallardDuck()
let turkey = WildTurkey()
let turkeyAdapter: Duck = TurkeyAdapter(turkey)

print("\n--- 칠면조가 말하길 ---")
turkey.gobble()
turkey.fly()
print("\n--- 오리가 말하길 ---")
duck.quack()
duck.fly()
print("\n--- 칠면조 어댑터가 말하길 ---")
turkeyAdapter.quack()
turkey.fly()

/*
 --- 칠면조가 말하길 ---
 골골
 짧은 거리를 날고 있어요!
    
 --- 오리가 말하길 ---
 꽥
 날고 있어요!

 --- 칠면조 어댑터가 말하길 ---
 골골
 짧은 거리를 날고 있어요!
 */
```
  </p>
</details>


## 6. 퍼사드 패턴 
- 복잡한 인터페이스를 단순하게 바꾸려고 인터페이스를 변경합니다. 
- 하나 이상의 클래스 인터페이스를 깔끔하면서도 효과적인 퍼사드(겉모양, 외관)으로 덮어줍니다. 
- 퍼사드 클래스는 서브 시스템 클래스(Light, Screen, Popper...)를 캡슐화 하지 않습니다. 그냥 더 쉽게 사용하기 위한 인터페이스만 제공할 뿐이죠. 이게 대표적인 장점인데, 캡슐화 하지 않기 때문에 원한다면 얼마든지 원래 서브 시스템 클래스 인스턴스를 생성한다던지 해서 계속 사용할 수 있게 합니다. 
- 최소 지식 원칙(Principle of Least Knowledge, LoD, Law of Demeter)을 준수하는데 도움이 됩니다.  
				
![](https://velog.velcdn.com/images/dev_kickbell/post/86224944-0cb4-4161-847f-d0137463be7e/image.png)

## 7. 홈시어터 퍼사드 구현하기 

- 공통 코드 

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class Popper {
    func on() {
        print("팝콘 기계가 켜졌습니다.")
    }
    
    func off() {
        print("팝콘 기계가 꺼졌습니다.")
    }
    
    func pop() {
        print("팝콘 기계에서 팝콘을 튀기고 있습니다.")
    }
}

class Light {
    func dim(_ num: Int) {
        print("조명 밝기를 \(num)%로 설정합니다.")
    }
    
    func on() {
        print("조명이 켜졌습니다.")
    }
}

class Screen {
    func down() {
        print("스크린이 내려옵니다.")
    }
    
    func up() {
        print("스크린이 올라갑니다.")
    }
}

class Projector {
    func on() {
        print("프로젝터가 켜졌습니다.")
    }
    
    func off() {
        print("프로젝터가 꺼졌습니다.")
    }
    
    func wideScreenMode() {
        print("프로젝터 화면 비율을 와이드 모드로 설정합니다.")
    }
}

class Amp {
    func on() {
        print("앰프가 켜졌습니다.")
    }
    
    func off() {
        print("앰프가 꺼졌습니다.")
    }
    
    func setStreamingPlayer(_ player: String) {
        print("앰프를 스트리밍 플레이어와 연결합니다.")
    }
    
    func setSurroundSound() {
        print("앰프를 서라운드 모드로 설정합니다(5.1채널).")
    }
    
    func setVolume(_ volume: Int) {
        print("앰프 볼륨을 \(volume)으로 설정합니다.")
    }
}

class Player {
    func on() {
        print("스트리밍 플레이어가 켜졌습니다.")
    }
    
    func off() {
        print("스트리밍 플레이어가 꺼졌습니다.")
    }
    
    func stop(_ movieName: String) {
        print("스트리밍 플레이어에서 \(movieName)를 종료합니다.")
    }
    
    func play(_ movieName: String) {
        print("스트리밍 플레이어에서 \(movieName)를 재생합니다.")
    }
}
```
  </p>
</details>

- 퍼사드 패턴 적용 전의 코드 
    - 영화를 보기 위해서는 할 일이 아주 많습니다. 
    - 팝콘 기계를 켜고, 튀기고, 조명을 조절하고, 스크린을 내리고, 프로젝트를 켜고... 
    - 영화를 끌 때도 마찬가지로 위의 과정을 역순으로 해야 합니다. 힘들어요. 
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/1f1fab23-8033-422c-bc6d-a0e0ac53face/image.png)
    
<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
let popper = Popper()
let lights = Light()
let screen = Screen()
let projector = Projector()
let amp = Amp()
let player = Player()

print("\n--- 영화 볼 준비 중 ---")
popper.on()
popper.pop()
lights.dim(10)
screen.down()
projector.on()
projector.wideScreenMode()
amp.on()
amp.setSurroundSound()
amp.setVolume(5)
player.on()
player.play("탑 건: 매버릭")

print("\n--- 홈시어터를 끄는 중 ---")
popper.off()
lights.on()
screen.up()
projector.off()
amp.off()
player.stop("탑 건: 매버릭")
player.off()
    
/*
 --- 영화 볼 준비 중 ---
 팝콘 기계가 켜졌습니다.
 팝콘 기계에서 팝콘을 튀기고 있습니다.
 조명 밝기를 10%로 설정합니다.
 스크린이 내려옵니다.
 프로젝터가 켜졌습니다.
 프로젝터 화면 비율을 와이드 모드로 설정합니다.
 앰프가 켜졌습니다.
 앰프를 서라운드 모드로 설정합니다(5.1채널).
 앰프 볼륨을 5으로 설정합니다.
 스트리밍 플레이어가 켜졌습니다.
 스트리밍 플레이어에서 탑 건: 매버릭를 재생합니다.

 --- 홈시어터를 끄는 중 ---
 팝콘 기계가 꺼졌습니다.
 조명이 켜졌습니다.
 스크린이 올라갑니다.
 프로젝터가 꺼졌습니다.
 앰프가 꺼졌습니다.
 스트리밍 플레이어에서 탑 건: 매버릭를 종료합니다.
 스트리밍 플레이어가 꺼졌습니다.
 */
```
  </p>
</details>
    

- 퍼사드 패턴 적용 후의 코드 
    - 팝콘, 조명, 스크린, 프로젝트 같은 서브 시스템을 합쳐서 통합 인터페이스를 만들어버립니다. 
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/9f68958c-f952-4ebd-a9e2-b4920c2a4be7/image.png) 

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class HomeTheaterFacade {
    let popper: Popper
    let lights: Light
    let screen: Screen
    let projector:Projector
    let amp: Amp
    let player: Player
    
    init(_ popper: Popper,
         _ lights: Light,
         _ screen: Screen,
         _ projector: Projector,
         _ amp: Amp,
         _ player: Player) {
        self.popper = popper
        self.lights = lights
        self.screen = screen
        self.projector = projector
        self.amp = amp
        self.player = player
    }
    
    func watchMovie(_ movieName: String) {
        print("\n--- 영화 볼 준비 중 ---")
        popper.on()
        popper.pop()
        lights.dim(10)
        screen.down()
        projector.on()
        projector.wideScreenMode()
        amp.on()
        amp.setSurroundSound()
        amp.setVolume(5)
        player.on()
        player.play(movieName)
    }
    
    func endMovie(_ movieName: String) {
        print("\n--- 홈시어터를 끄는 중 ---")
        popper.off()
        lights.on()
        screen.up()
        projector.off()
        amp.off()
        player.stop(movieName)
        player.off()
    }
}    
```
```swift
let popper = Popper()
let lights = Light()
let screen = Screen()
let projector = Projector()
let amp = Amp()
let player = Player()

let homeTheater = HomeTheaterFacade(popper, lights, screen, projector, amp, player)
homeTheater.watchMovie("탑 건: 매버릭")
homeTheater.endMovie("탑 건: 매버릭")

/*
 --- 영화 볼 준비 중 ---
 팝콘 기계가 켜졌습니다.
 팝콘 기계에서 팝콘을 튀기고 있습니다.
 조명 밝기를 10%로 설정합니다.
 스크린이 내려옵니다.
 프로젝터가 켜졌습니다.
 프로젝터 화면 비율을 와이드 모드로 설정합니다.
 앰프가 켜졌습니다.
 앰프를 서라운드 모드로 설정합니다(5.1채널).
 앰프 볼륨을 5으로 설정합니다.
 스트리밍 플레이어가 켜졌습니다.
 스트리밍 플레이어에서 탑 건: 매버릭를 재생합니다.

 --- 홈시어터를 끄는 중 ---
 팝콘 기계가 꺼졌습니다.
 조명이 켜졌습니다.
 스크린이 올라갑니다.
 프로젝터가 꺼졌습니다.
 앰프가 꺼졌습니다.
 스트리밍 플레이어에서 탑 건: 매버릭를 종료합니다.
 스트리밍 플레이어가 꺼졌습니다.
 */

```
  </p>
</details>


	

## 8. 최소 지식 원칙(Principle of Least Knowledge)
- 디미터/데메테르 법칙(Law of Demeter)이나 축약해서 LoD 라고도 불린다. 
- 한 마디로 정의하면 객체 사이의 상호작용은 될 수 있으면 아주 가까운 '친구'사이에만 허용하는 것이 좋다는 말이다.  
- 즉, 시스템을 디자인 할 때, 어떤 객체든 그 객체와 상호작용을 하는 클래스의 개수와 상호작용 방식에 주의를 기울여야 한다는 뜻이다. 
- 이 원칙을 잘 따르면 여러 클래스가 복잡하게 얽혀 있어서 시스템의 어느 한 부분을 변경했을 때, 다른 부분까지 줄줄이 고쳐야 하는 상황을 방지할 수 있습니다. 
- 이 원칙을 잘 따르지 않는 코드는 여러 클래스가 서로 복잡하게 의존하고 있어 관리하기도 힘들고, 남들이 이해하기 어려운 코드가 됩니다. 
- 장점이 있는만큼 단점도 있습니다. 원칙을 적용하다보면 '래퍼'클래스를 더 만들어야 할 수도 있습니다. 그러면 시스템이 복잡해지고, 개발 시간도 늘어나고, 성능도 떨어집니다. 
- 친구를 만들지 않는 4개의 가이드라인 
    - 객체 자체는 호출 (O)
    - 메소드에 매개변수로 전달된 객체는 호출 (O)
    - 메소드를 생성하거나 인스턴스를 만든 객체는 호출 (O)
    - 객체에 속하는 구성 요소는 호출 (O)				
    
![](https://velog.velcdn.com/images/dev_kickbell/post/03071ce2-5a81-45af-9fdf-7d68cd0bdd93/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/a7a6b03a-ac5a-4268-863f-163f8afda404/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/73cb24b2-9323-4039-9bce-726a008d99d5/image.png)


## 9. 퍼사드 패턴과 최소 지식 원칙 
- 아래 클라이언트의 친구는 퍼사드 하나 뿐이죠. 친구는 찐친구 하나만 있어도 됩니다. 
			
![](https://velog.velcdn.com/images/dev_kickbell/post/94e50383-c8f7-4294-909d-3a82ffd2ac5e/image.png)


## Reference 
[https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223](https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223)



