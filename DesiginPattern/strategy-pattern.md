
# Strategy Pattern(전략 패턴)
> - 알고리즘군을 정의하고 캡슐화해서 각각의 알고리즘군을 수정해서 쓸 수 있게 해준다. 
> - 클라이언트로부터 알고리즘을 분리해서 독립적으로 변경할 수 있다. 

## 1. 오리게임에서 사용할 오리가 있어요.

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class Duck {
    func quack() {
        print("꽥꽥하고 웁니다.")
    }
    func swim() {
        print("수영을 합니다.")
    }
    func display() {
        print("오리 종류별로 이미지를 표시합니다.")
    }
}

//청둥오리 또는 물오리
class MallardDuck: Duck {
    
    override func display() {
        print("저는 청둥오리 입니다.")
    }

}

class RedHeadDuck: Duck {
    
    override func display() {
        print("저는 청둥오리 입니다.")
    }
    
}
```
  </p>
</details>



## 2. 경쟁사와의 경쟁을 위해 fly()라는 획기적인 기능을 넣어야 합니다.

## 3. 상속으로 fly() 기능을 넣어보기 
- 상속을 사용했기 때문에 코드를 재사용할 수 있었다. 수정이 발생하면 Duck(부모)클래스만 수정하면 되는 기가막힌 객체지향 프로그래밍 코드이다. 맞을까요 ? 
- 문제가 발생했다. 고무오리가 생겼다. 고무오리는 진짜 동물처럼 꽥꽥하고 울지도, 그리고 날지도 않는다. 
- 괜찮다. 오버라이드하면 되지. 안그래? 그렇지 않다. 만약 나무(tree)오리, 모형(model)오리, 강철(steel)오리가 생긴다면? 
- 그렇게 되면, 매번 새로운 오리가 생길 때마다 fly(), quack() 메소드를 일일히 살펴보고 문제가 있는지 없는지 확인 후 override 해야 한다. 
- 만약에 fly(), quack() 말고 다른 이와 같은 기능이 추가된다면? 또, 나무오리를 상속하는 자작나무오리, 참나무오리, 단풍나무 오리는? 단풍나무 오리를 상속하는 한국단풍나무오리와 일본단풍나무 오리는 어떨까? 
- 결론적으로, 상속은 좋은 해결책이 아니다. 

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
/*
이 코드에서 상속을 사용할 때의 단점 
1. 서브클래스에서 코드가 중복된다. (날지않는 오리에게도 오버라이드를 해야함) 
2. 실행시에 특징을 바꿀 수 없다. 
3. 모든 오리의 행동을 알기 힘들다. (오버라이드를 남발했기 때문에 일일히 확인해야 한다.)
4. 부모 클래스의 코드를 변경했을 때 다른 오리들에게 원치않는 영향을 끼칠 수 있다. (고무 오리가 날아다닌다던지)
*/
    
class Duck {
    func quack() {
        print("꽥꽥하고 웁니다.")
    }
    func swim() {
        print("수영을 합니다.")
    }
    func display() {
        print("오리 종류별로 이미지를 표시합니다.")
    }
    func fly() {
        print("오리는 날기도 합니다.")
    }
}

class RubberDuck: Duck {
    
    override func quack() {
        //고무오리는 꽥꽥하고 울지 않으므로
        //삑삑 소리를 내기위해 오버라이드
    }
    
    override func fly() {
        //고무오리는 날지 않으므로 날지않기 위해
        //아무것도 하지 않도록 오버라이드
    }
    
}
```
  </p>
</details>

## 4. 인터페이스(protocol)로 fly() 기능을 넣어보기
- 그렇다면 아래와 같은 그림처럼 인터페이스를 활용하는 방법은 어떨까 ? 
- fly(), quack() 기능만 인터페이스로 빼서 그것을 사용하는 오리에게만 구현하도록 하는 것이다. 좋은 해결책이지 않을까요 ? 
- 슬프게도 이것 또한 정답이 아닙니다. 인터페이스(protocol)만을 사용하게 된다면, 실제 구현은 각 클래스에서 하게 되므로 코드를 재사용하지 않기 때문에 오리가 꽥꽥하고 울다가 꽥-꽥--꽥 하고 울게 변경되면 Quackable를 준수하는 200마리의 종류별 오리 코드를 다 수정해야 합니다. 
- 결국 인터페이스(protocol)도 해결책은 아닙니다. 
- Swift에서는 protocol + extension이 있죠. 얘는 그럼 해결책일까요 ? 

![](https://velog.velcdn.com/images/dev_kickbell/post/38817e7d-ee90-49e4-ab0e-4c8ea1bbcc01/image.png)

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class Duck {
    func swim() {
        print("수영을 합니다.")
    }
    func display() {
        print("오리 종류별로 이미지를 표시합니다.")
    }
}

protocol Flyable {
    func fly()
}

protocol Quackable {
    func quack()
}

//청둥오리 또는 물오리
class MallardDuck: Duck, Flyable, Quackable {
    
    func fly() {
        print("저는 날 수 있어요.")
    }
    
    func quack() {
        print("꽥꽥")
    }
    
    override func display() {
        print("저는 청둥오리 입니다.")
    }

}

class RedHeadDuck: Duck, Flyable, Quackable {
    
    func fly() {
        print("저는 날 수 있어요.")
    }
    
    func quack() {
        print("꽥꽥")
    }
    
    override func display() {
        print("저는 청둥오리 입니다.")
    }
    
}

//고무오리는 날 수 없기 때문에 Flayable은 준수하지 않아요.
class RubberDuck: Duck, Quackable {
    
    func quack() {
        print("삑삑")
    }
    
}
```
  </p>
</details>


## 5. 바뀌는 부분을 뽑아서 클래스 집합(set)으로 만들기  
- 아래의 그림처럼 바뀌는 부분인 fly()와 quack() 행동을 따로 뽑아서 클래스 집합으로 만듭니다.
- 첫번째 목표는 오리를 유연하게 만들기 위해서 특정 타입으로 초기화 할 수 있게 상위타입으로 protocol을 사용합니다.
- 두번째 목표는 오리의 행동을 동적으로 바꿀 수 있으면 좋을 것 같아요. setter 메소드를 추가합니다.
- 목표만 확인하고 다음으로 넘어가도 좋아요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/a8b6e7c0-272c-4d8d-a506-9caa74414edc/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/78c8588b-db13-4006-bc95-0bbf2f5f0cd6/image.png)

## 6. 전략  구현하기
- 각 행동은 클래스로 구현되어 FlyBehavior, QuackBehavior 프로토콜을 준수합니다.
- Duck 클래스에는 변수로 FlyBehavior, QuackBehavior을 가지고 있죠. 이렇게 하면 FlyBehavior, QuackBehavior가 더 상위 타입이기 때문에, 여러 가지의 행동 중에 하나로 초기화 할 수 있지요.
- 또, 상위 타입으로 구현했기 때문에 func setFlyBehavior()가 있어서 언제든지 런타임에서 행동을 바꿔줄 수도 있습니다. 
- 결론적으로 각 행동이 수정되면 각 클래스에서 변경해주면 됩니다. 코드가 재사용되고 있고, 만약 새로운 기능이 추가된다고 하면 새로운 행동 클래스를 만들어주면 되겠죠. 		

![](https://velog.velcdn.com/images/dev_kickbell/post/ea2630de-4d81-4d21-a90a-f8d89a0622c8/image.png)		
<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class Duck {
    var flyBehavior: FlyBehavior
    var quackBehavior: QuackBehavior
    
    init(flyBehavior: FlyBehavior,
         quackBehavior: QuackBehavior) {
        self.flyBehavior = flyBehavior
        self.quackBehavior = quackBehavior
    }
    
    func setFlyBehavior(_ fb: FlyBehavior) {
        self.flyBehavior = fb
    }
    
    func setQuackBehavior(_ qb: QuackBehavior) {
        self.quackBehavior = qb
    }
    
    func performFly() {
        flyBehavior.fly()
    }
    
    func performQuack() {
        quackBehavior.quack()
    }
    
    func swim() {
        print("수영을 합니다.")
    }
    
    func display() {
        print("오리 종류별로 이미지를 표시합니다.")
    }
}
    
```
    
```swift
protocol FlyBehavior {
    func fly()
}

class FlyWithWings: FlyBehavior {
    func fly() {
        print("저는 날 수 있어요.")
    }
}

class FlyNoWay: FlyBehavior {
    func fly() {
        print("저는 날 수 없어요.")
    }
}

class FlyRocketPowered: FlyBehavior {
    func fly() {
        print("저는 모형오리라 로켓파워로 날아갑니다.")
    }
}
 

protocol QuackBehavior {
    func quack()
}

class Quack: QuackBehavior {
    func quack() {
        print("꽥꽥")
    }
}

//고무오리 우는소리
class Squack: QuackBehavior {
    func quack() {
        print("삑삑")
    }
}

class MuteQuack: QuackBehavior {
    func quack() {
        print("저는 소리를 낼 수 없어요.")
    }
}

```
    
```swift
//청둥오리 또는 물오리
class MallardDuck: Duck {
    
    init() {
        super.init(
            flyBehavior: FlyWithWings(),
            quackBehavior: Quack()
        )
    }
    
    override func display() {
        print("저는 청둥오리 입니다.")
    }

}
    
class ModelDuck: Duck {
    
    init() {
        super.init(
            flyBehavior: FlyNoWay(),
            quackBehavior: Quack()
        )
    }
    
    override func display() {
        print("저는 모형오리 입니다.")
    }
    
}
    
```
    
```swift
var mallardDuck = MallardDuck()
mallardDuck.performFly() //저는 날 수 있어요.
mallardDuck.performQuack() //꽥꽥

var modelDuck = ModelDuck()
modelDuck.performFly() //저는 날 수 없어요.
modelDuck.setFlyBehavior(FlyRocketPowered())
modelDuck.performFly() //저는 모형오리라 로켓파워로 날아갑니다.

```
  </p>
</details>


## 7. 전략 패턴 정리하기 
- 바뀌는 부분은 캡슐화한다.
    - 달라지는 부분을 찾아서 나머지 코드에 영향을 주지 않도록 캡슐화하는 것이 핵심입니다. 
    - 그렇게 하면, 코드를 변경하는 과정에서 의도치 않게 발생하는 일을 줄이면서 코드의 유연성을 향상시킬 수 있어요. 
- 구현보다는 인터페이스에 맞춰서 프로그래밍한다.
    - 인터페이스에 맞춰서 프로그래밍한다라는 말은 사실 상위 형식에 맞춰서 프로그래밍한다는 말입니다.
    - 객체지향의 다형성을 활용해야 한다는 것이죠. 
- 상속보다는 구성을 활용한다.
    - 위에서 본 것처럼 구성을 활용해서 시스템을 만들면 유연성을 크게 향상시킬 수 있습니다. 
    - A는 B이다 보다 A에는 B가 있다가 더 나을 수도 있는 것이죠.

## 8. Swift의 Protocol-Extension
- Swift의 Protocol-Extension 조합으로 대체할 수 있을까? 

## Reference 
[https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223](https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223)
