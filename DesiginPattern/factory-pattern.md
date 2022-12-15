# Factory Pattern(팩토리 패턴)
> - 객체 생성을 서브 클래스에 캡슐화 할 수 있다. 그러면 슈퍼클래스에 있는 클라이언트 코드와 서브클래스에 있는 객체 생성 코드를 분리할 수 있습니다.
> - 즉, 슈퍼클래스에 있는 코드에서 서브클래스에서 생성되는 객체가 무엇인지 알 수 없게 만드는 역할도 합니다. 

### Factory Method Pattern(팩토리 메소드 패턴)
> - 객체를 생성할 때 필요한 인터페이스를 만듭니다. 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정합니다. 팩토리 메소드 패턴을 사용하면 클래스 인스턴스 만드는 일을 서브클래스에게 맡기게 됩니다. 

### Abstract Factory Pattern(추상 팩토리 패턴)
> - 구상 클래스에 의존하지 않고도 서로 연관되거나 의존적인 객체로 이루어진 제품군을 생산하는 인터페이스를 제공합니다. 구상 클래스는 서브 클래스에서 만듭니다.


## 1. 피자가게 만들기 
- 맛있고 장사잘되는 피자가게를 만들려고 합니다. 하지만 문제가 생겼어요. 
- 기존에는 치즈/그리스/페페로니 피자만 있었는데, 잘 안팔리는 그리스 피자를 없애고 조개/야채 피자를 넣으려고 하니 코드를 변경해야만 합니다. 
- 앞으로도 메뉴는 시도때도없이 변할 것인데 그때마다 코드를 바꿔줄 순 없어요. 변하는 부분을 따로 빼서 캡슐화 해야 합니다. 

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
protocol Pizza {
    func prepare()
    func bake()
    func cut()
    func box()
    func getName() -> String
}

extension Pizza {
    func prepare() {
        print(#function)
    }
    
    func bake() {
        print(#function)
    }
    
    func cut() {
        print(#function)
    }
    
    func box() {
        print(#function)
    }
    
    func getName() -> String {
        let str = String(describing: self)
        let name = str.split(separator: ".").last ?? ""
        return String(name)
    }
}
    
class CheesePizza: Pizza {}
class GreekPizza: Pizza {}
class PepperoniPizza: Pizza {}
class ClamPizza: Pizza {}
class VeggiePizza: Pizza {}
    
class PizzaStore {
    func orderPizza(_ type: String) -> Pizza {
        var pizza: Pizza = CheesePizza()
        
        /*
         메뉴 추가/삭제에 따라 변하는 부분
         객체 생성 코드
         */
        if type == "cheese"{ //치즈
            pizza = CheesePizza()
        } else if type == "greek" { //그리스
            pizza = GreekPizza()
        } else if type == "pepperoni" { //페퍼로니
            pizza = PepperoniPizza()
        } else if type == "clam" { //조개
            pizza = ClamPizza()
        } else if type == "veggie" { //야채
            pizza = VeggiePizza()
        }
        
        /*
         변하지 않는 부분
         피자 공통
         */
        pizza.prepare()
        pizza.bake()
        pizza.cut()
        pizza.box()
        return pizza 
    }
}
```
  </p>
</details>


## 2. 간단한 팩토리(Simple Factory)
- 객체 생성을 처리하는 클래스를 팩토리(Factory)라고 부릅니다. 
- 위의 코드에서 변하는 부분을 빼내서 SimplePizzaFactory로 만들고, 앞으로 객체가 필요할 땐 SimplePizzaFactory 에게 객체를 만들어서 리턴해달라고 부탁하면 됩니다. 
- 단순히 객체 생성을 다른 클래스로 넘겨버린 것이지만, 메뉴를 추가/변경할 때 팩토리 클래스 하나만 고칠 수 있고 또 피자주문을 배달로 하는 HomeDeliveryPizza 시스템에도 이 팩토리를 재사용할 수 있습니다. 
- 이런 것을 Simple Factory라고 부릅니다. 패턴은 아닙니다. 그리고 간단한 팩토리 같은 경우 static 메소드도 사용하곤 하는데, 그것은 SimplePizzaFactory.createPizza("cheese") 처럼 인스턴스를 만들지 않기 위해 사용합니다. 다만 이 방법은 서브클래스를 만들어서 객체 생성 메소드의 행동을 변경할 수 없다는 단점은 있지요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/e4dbf005-ad43-4806-8696-283985f44732/image.png)

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class SimplePizzaFactory {
    func createPizza(_ type: String) -> Pizza {
        var pizza: Pizza = CheesePizza()
        /*
         메뉴 추가/삭제에 따라 변하는 부분
         객체 생성 코드
         */
        if type == "cheese"{ //치즈
            pizza = CheesePizza()
//        } else if type == "greek" { //그리스
//            pizza = GreekPizza()
        } else if type == "pepperoni" { //페퍼로니
            pizza = PepperoniPizza()
        } else if type == "clam" { //조개
            pizza = ClamPizza()
        } else if type == "veggie" { //야채
            pizza = VeggiePizza()
        }
        
        return pizza
    }
}
    
class PizzaStore {
    var factory: SimplePizzaFactory
    
    init(_ factory: SimplePizzaFactory) {
        self.factory = factory
    }
    
    func orderPizza(_ type: String) -> Pizza {
        var pizza: Pizza
        
        pizza = factory.createPizza(type)
        
        /*
         변하지 않는 부분
         피자 공통
         */
        pizza.prepare()
        pizza.bake()
        pizza.cut()
        pizza.box()
        return pizza
    }
}
```
```swift
let pizzaStore = PizzaStore(SimplePizzaFactory())
let pizza = pizzaStore.orderPizza("veggie")
print(pizza.getName())
//prepare()
//bake()
//cut()
//box()
//VeggiePizza
```
  </p>
</details>



## 3. 팩토리 메소드 패턴 
- 사업이 확장되어 같은 피자라도 뉴욕/시카고/캘리포니아 스타일의 피자를 생성해야 합니다. 각 피자는 지역에 따라 같은 종류의 피자라고 하더라도 레시피가 다릅니다. 뉴욕 스타일 피자는 빵이 얇고, 시카고 피자 스타일은 두껍죠.
- SimplePizzaFactory와 factory 인스턴스를 삭제하고 createPizza() 를 추상메소드로 선언합니다. 
- 각 지점에 맞는 서브클래스를 생성합니다. (NYPizzaStore/ChicagoPizzaStore/CaliforniaPizzaStore)
- 여기서 등장하는 것이 바로 팩토리 메소드 패턴입니다. 아래의 그림을 보면 더 이해가 빠르겠지만, 팩토리(객체)를 생성하는 메소드 패턴을 말합니다. 
- 설명하자면, orderPizza()는 피자를 만드는 과정을 나타내는 메소드죠. 얘는 피자를 만들기만 합니다. 결정적으로 피자 객체를 생성하는 팩토리 메소드는 createPizza()인 거죠. 하지만 위에서도 말했다시피 createPizza()를 추상 메소드로 설정했기 때문에, 부모 클래스인 PizzaStore는 createPizza()를 실행하면 피자가 리턴된다 정도만 알지, 무슨 피자가 리턴되는지 같은 것은 모릅니다. 캡슐화 되어 있다고 표현할 수 있고, Pizza와 PizzaStore가 서로 완전히 분리되어 있다고 말할 수도 있을 것 같아요. 
- 그러면 피자 종류는 누가 결정할까요? 자식 클래스인 NYPizzaStore 같은 녀석들이 합니다. 추상 메소드이기 때문에 createPizza()를 꼭 구현해야 하죠. 
- 자세한 것은 아래 코드로 보는 것이 더 편할 겁니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/2f8474f6-6b68-4d2d-a902-6dd0d86481ed/image.png)

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift

protocol Pizza {
    var name: String { get }
    var dough: String { get }
    var sauce: String { get }
    var toppings: [String] { get }
    
    func prepare()
    func bake()
    func cut()
    func box()
    func getName() -> String
}

extension Pizza {
    var name: String {
        return String(describing: self)
    }
    
    var dough: String {
        return ["치즈 크러스트 도우",
                "바이트 도우",
                "고구마 무스",
                "씬 크러스트 도우"].randomElement()!
    }
    
    var sauce: String {
        return ["토마토 소스",
                "마리나라 소스",
                "어니언 소스",
                "갈릭 소스"].randomElement()!
    }
    
    var toppings: [String] {
        let toppingList: [String] = [
            "모짜렐라 치즈", "레지아노 치즈",
            "파마산 치즈", "새우", "감자", "소시지"]
        let count = (1...toppingList.count).randomElement() ?? 0
        var toppings: [String] = []
        for _ in 0..<count {
            toppings.append(toppingList.randomElement() ?? "")
        }
        return toppings
    }
    
    func prepare() {
        print("\n\n===== Order =====")
        print("준비 중: \(name)")
        print("도우를 돌리는 중...")
        print("소스를 뿌리는 중...")
        print("토핑를 올리는 중...")
        toppings.forEach { topping in
            print(" \(topping)")
        }
    }
    
    func bake() {
        print("175도에서 25분 간 굽기")
    }
    
    func cut() {
        print("피자를 사선으로 자르기")
    }
    
    func box() {
        print("상자에 피자 담기")
    }
    
    func getName() -> String {
        return name
    }
}


class EmptyPizza: Pizza {}
class CheesePizza: Pizza {}
class GreekPizza: Pizza {}
class PepperoniPizza: Pizza {}
class ClamPizza: Pizza {}
class VeggiePizza: Pizza {}

class NYStyleCheesePizza: Pizza {}
class NYStyleGreekPizza: Pizza {}
class NYStylePepperoniPizza: Pizza {}
class NYStyleClamPizza: Pizza {}
class NYStyleVeggiePizza: Pizza {}

class ChicagoStyleCheesePizza: Pizza {
    //오버라이드, 시카고 피자는 네모나게 잘라야 한다.
    func cut() {
        print("네모난 모양으로 자르기")
    }
}
class ChicagoStyleGreekPizza: Pizza {
    //오버라이드, 시카고 피자는 네모나게 잘라야 한다.
    func cut() {
        print("네모난 모양으로 자르기")
    }
}
class ChicagoStylePepperoniPizza: Pizza {
    //오버라이드, 시카고 피자는 네모나게 잘라야 한다.
    func cut() {
        print("네모난 모양으로 자르기")
    }
}
class ChicagoStyleClamPizza: Pizza {
    //오버라이드, 시카고 피자는 네모나게 잘라야 한다.
    func cut() {
        print("네모난 모양으로 자르기")
    }
}
class ChicagoStyleVeggiePizza: Pizza {
    //오버라이드, 시카고 피자는 네모나게 잘라야 한다.
    func cut() {
        print("네모난 모양으로 자르기")
    }
}

class CaliforniaStyleCheesePizza: Pizza {}
class CaliforniaGreekPizza: Pizza {}
class CaliforniaPepperoniPizza: Pizza {}
class CaliforniaClamPizza: Pizza {}
class CaliforniaVeggiePizza: Pizza {}
```
```swift
protocol PizzaStore {
    func orderPizza(_ type: String) -> Pizza
    func createPizza(_ type: String) -> Pizza
}

extension PizzaStore {
    func orderPizza(_ type: String) -> Pizza {
        var pizza: Pizza

        pizza = createPizza(type)

        /*
         변하지 않는 부분
         피자 공통
         */
        pizza.prepare()
        pizza.bake()
        pizza.cut()
        pizza.box()
        return pizza
    }
}
```
```swift
class NYPizzaStore: PizzaStore {
    func createPizza(_ type: String) -> Pizza {
        if type == "cheese"{ //치즈
            return NYStyleCheesePizza()
        } else if type == "pepperoni"{ //페퍼로니
            return NYStylePepperoniPizza()
        } else if type == "clam" { //조개
            return NYStyleClamPizza()
        } else if type == "veggie" { //야채
            return NYStyleVeggiePizza()
        } else {
            return EmptyPizza()
        }
    }
}

class ChicagoPizzaStore: PizzaStore {
    func createPizza(_ type: String) -> Pizza {
        if type == "cheese"{ //치즈
            return ChicagoStyleCheesePizza()
        } else if type == "pepperoni"{ //페퍼로니
            return ChicagoStylePepperoniPizza()
        } else if type == "clam" { //조개
            return ChicagoStyleClamPizza()
        } else if type == "veggie" { //야채
            return ChicagoStyleVeggiePizza()
        } else {
            return EmptyPizza()
        }
    }
}
   
class CaliforniaPizzaStore: PizzaStore {
    func createPizza(_ type: String) -> Pizza {
        if type == "cheese"{ //치즈
            return CaliforniaStyleCheesePizza()
        } else if type == "pepperoni"{ //페퍼로니
            return CaliforniaPepperoniPizza()
        } else if type == "clam" { //조개
            return CaliforniaClamPizza()
        } else if type == "veggie" { //야채
            return CaliforniaVeggiePizza()
        } else {
            return EmptyPizza()
        }
    }
}
```
```swift
let nyStore = NYPizzaStore()
let chicagoStore = ChicagoPizzaStore()

var pizza = nyStore.orderPizza("veggie")
print("에단이 주문한 \(pizza.getName())")

pizza = chicagoStore.orderPizza("cheese")
print("조엘이 주문한 \(pizza.getName())")
/*
 ===== Order =====
 준비 중: FactoryPattern.NYStyleVeggiePizza
 도우를 돌리는 중...
 소스를 뿌리는 중...
 토핑를 올리는 중...
  소시지
 175도에서 25분 간 굽기
 피자를 사선으로 자르기
 상자에 피자 담기
 에단이 주문한 FactoryPattern.NYStyleVeggiePizza


 ===== Order =====
 준비 중: FactoryPattern.ChicagoStyleCheesePizza
 도우를 돌리는 중...
 소스를 뿌리는 중...
 토핑를 올리는 중...
  모짜렐라 치즈
  감자
  새우
 175도에서 25분 간 굽기
 네모난 모양으로 자르기
 상자에 피자 담기
 조엘이 주문한 FactoryPattern.ChicagoStyleCheesePizza
 Program ended with exit code: 0
 */
```
  </p>
</details>


## 4. 의존성 뒤집기 원칙(Dependency Inversion Principle)

-  Dependency(의존성)란 ? 
    - 객체에 의존한다는 건 아래의 그림과 같은 겁니다. DependentPizzaStore는 모든 피자 객체를 클래스 내에서 직접 만들고 있어요.
    - 각 피자 클래스의 클래스명이 변경된다거나, 또는 피자 메뉴가 추가된다면 PizzaStore까지 고쳐야해요. 상황에 따라서는 각 피자 클래스들의 내부 구현이 변경되도 바꿔야 할 수도 있죠.
    - 그렇기 때문에 DependentPizzaStore는 피자 클래스 구현에 의존한다고 말할 수 있습니다. 
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/efb2239d-341d-4297-afcf-86a78eaff369/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/87e316cf-e11c-4cfe-a202-bff81f374292/image.png)
    
- Dependency Inversion Principle(의존성 뒤집기 원칙)
    - "구현보다는 인터페이스에 맞춰서 프로그래밍 한다." 라는 원칙과 유사할 수 있지만, DIP는 추상화를 더 강조합니다. 고수준 구성요소가 저수준 구성요소에 의존하면 안되고 항상 추상화에 의존하게 만들어야 한다는 뜻이 있어요. 
    - 고수준 구성요소와 저수준 구성요소라 함은 그런겁니다. 피자스토어를 예로 들면, 피자 스토어는 다양한 피자 객체를 만들고, 피자를 준비하고, 굽고, 자르고, 포장하죠. 즉, 피자스토어의 행동은 피자에 의해 결정되죠. 그래서 피자스토어는 고수준 구성요소이고, 피자스토어에서 사용하는 피자 객체는 저수준 구성요소 입니다. 
    - 지금 구현해놓은 팩토리 메소드 패턴을 사용하면 DIP 원칙을 지킬 수 있는데요. 위에서 구현한 코드를 보면 PizzaStore는 인터페이스고 Pizza 클래스를 사용합니다. PizzaStore가 고수준 구성요소, Pizza 클래스(얘또한 추상클래스)가 저수준 구성요소죠. 
    - 또, 구상 클래스인 피자 클래스 또한 인터페이스인 Pizza를 준수하죠. Pizza에 의존적인거죠. 여기서 중요한 것이 화살표인데요. 보통 저수준 -> 고수준, 아래에서 위로 화살표가 향하는데 아래 그림을 보면 고수준 구성요소인 PizzaStore 또한 Pizza에 의존합니다. 화살표 방향이 반대로 바뀐거죠. 그래서 의존성을 뒤집었다고 의존성 뒤집기 원칙이라고 하는 겁니다. 
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/512df50f-52e8-4a1a-b021-dd8541de5550/image.png)

- 생각을 뒤집기 
    - 일반적인 생각(DIP 적용 전)
      - PizzaStore를 구현해야 한다고 합시다. 뭐부터 해야할까요 ? 
      - PizzaStore는 Pizza를 준비하고, 굽고, 포장할 수 있어야 겠죠. Pizza 종류도 많아야 하고요. 
      - 하지만 PizzaStore가 각 Pizza 종류같은 것들을 알면 구상 Pizza Class에 의존하는 문제가 생겨요. 그렇다면, PizzaStore부터 생각하지 말고 뒤집어서 Pizza부터 생각해보죠. 
    - 생각 뒤집기(DIP 적용 후)
      - Pizza는 종류별로 다 다르지만 전부 다 Pizza입니다. 그러면 Pizza Protocol를 공유하는 것이 좋겠죠? 
      - 그리고 피자를 Pizza라는 인터페이스로 추상화했으니 PizzaStore는 Pizza 클래스를 신경쓰지 않아도 될 거에요. 
      - 다만, Pizza 인터페이스를 만든다고 끝이 아니라 PizzaStore에서 각 Pizza 클래스들을 없애야 겠죠. 그리고 그 방법 여러가지 중에 팩토리 메소드 패턴을 우리는 사용한거구요. 
      - 즉, 이런식으로 생각을 위에서 아래로 내려오는 것을 뒤집는다고 해서 의존성 뒤집기 원칙이라고 말합니다. 
      - ![](https://velog.velcdn.com/images/dev_kickbell/post/7f9e032b-e1f8-4fe1-a914-aa1f13d56b9c/image.png)
      
- DIP를 지키는 가이드라인 
    - 변수에 구상 클래스의 레퍼런스를 저장하지 맙시다. 
        - let pizza = ChicagoCheesePizza() 같은 걸 하지 말라는 뜻입니다. 클래스의 인스턴스를 만들지 말라는 것이죠. 
    - 구상 클래스에서 유도된 클래스를 만들지 맙시다. 
    	- 인터페이스나 추상클래스처럼 추상화된 것으로 부터 클래스를 만들라는 말이에요. 그렇지 않으면 구상 클래스에 의존하게 되버리니까 말이죠.
    - 베이스 클래스에 이미 구현되어 있는 메소드를 오버라이드 하지 맙시다. 
    	- 이미 구현되어 있는 클래스를 오버라이드 해버리면 베이스 클래스가 제대로 추상화되지 않죠. 
    - 이 가이드라인을 다 지킬 수 있나요 ? 
        - 사실 불가능하고, 단지 지향해야 할 바를 알려주는 겁니다. 이 원칙을 알고 디자인하면 합리적인 이유로 불가피한 상황에서만 예외를 둘 수 있어요. 
        - 예를 들어, 어떤 클래스가 바뀌지 않는다면 그 클래스의 인스턴스를 만드는 코드를 작성해도 그리 큰 문제가 생기지 않죠. 
        - Swift는 Struct지만 보통 String 객체의 인스턴스는 생각없이 만들어서 쓰죠. 이것도 엄밀히 따지면 원칙 위배지만 String 클래스가 바뀌는 일은 정말 거~~의 없죠. 언어의 Version이 바뀌어도 기능이 추가되면 됐지 String 클래스 자체가 바뀌는 일은 잘 없으니까요. 
        - 하지만, 우리가 만드는 클래스가 바뀔 수 있다면 변경될 수 있는 부분은 캡슐화를 해야 합니다. 


## 5. 추상 팩토리 패턴
- 지역별로 피자의 종류는 같지만, 피자에 들어가는 재료가 다릅니다. 
- 모든 피자에 관한 원재료를 생산하는 PizzaIngredientFactory 인터페이스를 만들고, 각 지역별로 NYPizzaIngredientFactory, ChicagoPizzaIngredientFactory와 같은 팩토리를 만듭니다.
- 또 각 팩토리에서 사용할 원재료 클래스를 구현합니다. Dough를 예로 들면, Dough 라는 인터페이스로 ThinDough, ThickDough, CheeseCrustDough 재료 클래스를 만드는 식이죠. 
- 그리고 이렇게 만든 재료들을 PizzaStore에서 사용하도록 하나로 묶어야 합니다. 
- 기존에 하드코딩 했던 재료들을 없애고, 원재료들을 Pizza 클래스에 넣어줍니다. 그리고 prepare() 메소드 또한 추상메소드로 선언합니다. 여기에서 각 지역별로 필요한 재료들을 가져올거에요. 나머지 메소드는 그대로 두어도 괜찮습니다. 
- 그리고 이제 원재료를 팩토리에서 가져오도록 바꿔야 합니다. 원래는 CheesePizza도 각 지역별로 NYStyleCheesePizza와 같이 따로 따로 만들었지만, 이제 그럴 필요가 없어요. 
- 치즈/조개/페퍼로니/야채 피자를 큰 틀로 두고 피자를 이루는 재료들만 각 지역에 맞게 다르게 넣어주면 됩니다. 
- 마지막으로 각 지역별 PizzaStore에서 해당 지역에 맞는 팩토리를 넣어주면 됩니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/cc8e378e-0707-46f1-b2f3-06996520d81b/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/e6e38939-6513-441f-a67f-5779f782c00a/image.png)


<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
protocol PizzaIngredientFactory {
    func createDough() -> Dough
    func createSauce() -> Sauce
    func createCheese() -> Cheese
    func createVeggies() -> [Veggie]
    func createPepperoni() -> Peppernoni
    func createClam() -> Clams
}

protocol Dough {} //도우
protocol Sauce {} //소스
protocol Cheese {} //치즈
protocol Veggie {} //야채
protocol Peppernoni {} //페퍼로니
protocol Clams {} //조개

class BasicDough: Dough { }
class ThinCrustDough: Dough { }
class ThickCrustDough: Dough { }

class BasicSauce: Sauce { }
class MarinaraSauce: Sauce { }
class PlumTomatoSauce: Sauce { }

class BasicCheese: Cheese { }
class ReggianoCheese: Cheese { }
class MozzarellaCheese: Cheese { }

class Garlic: Veggie { }
class Onion: Veggie { }
class Mushroom: Veggie { }
class RedPepper: Veggie { } //고추
class BlackOlives: Veggie { } //블랙올리브
class Spinach: Veggie { } //시금치
class EggPlant: Veggie { } //가지

class BasicPeppernoni: Peppernoni { }
class SlicedPeppernoni: Peppernoni { }

class BasicClams: Clams { }
class FreshClams: Clams { }
class FrozenClams: Clams { }    
```
```swift
class NYPizzaIngredientFactory: PizzaIngredientFactory {
    func createDough() -> Dough {
        return ThinCrustDough()
    }
    
    func createSauce() -> Sauce {
        return MarinaraSauce()
    }
    
    func createCheese() -> Cheese {
        return ReggianoCheese()
    }
    
    //야채도 종류별로 더 어렵게 만들 수 있지만 너무 복잡해져서 일단 그냥 이렇게 사용.
    func createVeggies() -> [Veggie] {
        return [Garlic(), Onion(), Mushroom(), RedPepper()]
    }
    
    func createPepperoni() -> Peppernoni {
        return SlicedPeppernoni()
    }
    
    //뉴욕은 바닷가라 신선한 조개를 쓰지만, 시카고는 내륙이라 냉동 조개를 써야함.
    func createClam() -> Clams {
        FreshClams()
    }
}
    
class ChicagoPizzaIngredientFactory: PizzaIngredientFactory {
    func createDough() -> Dough {
        return ThickCrustDough()
    }
    
    func createSauce() -> Sauce {
        return PlumTomatoSauce()
    }
    
    func createCheese() -> Cheese {
        return MozzarellaCheese()
    }
    
    func createVeggies() -> [Veggie] {
        return [BlackOlives(), Spinach(), EggPlant()]
    }
    
    func createPepperoni() -> Peppernoni {
        return SlicedPeppernoni()
    }
    
    func createClam() -> Clams {
        return FrozenClams()
    }
}
```
```swift
protocol Pizza {
    var name: String { get set }
    var dough: Dough { get set }
    var sauce: Sauce { get set }
    var veggies: [Veggie] { get set }
    var cheese: Cheese { get set }
    var pepperoni: Peppernoni { get set }
    var clam: Clams { get set }
    
    func prepare() //추상메소드
    func bake()
    func cut()
    func box()
    func getName() -> String
}

extension Pizza {
//    var name: String { String(describing: self) }
//
//    var dough: String {
//        return ["치즈 크러스트 도우",
//                "바이트 도우",
//                "고구마 무스",
//                "씬 크러스트 도우"].randomElement()!
//    }
//
//    var sauce: String {
//        return ["토마토 소스",
//                "마리나라 소스",
//                "어니언 소스",
//                "갈릭 소스"].randomElement()!
//    }
//
//    var toppings: [String] {
//        let toppingList: [String] = [
//            "모짜렐라 치즈", "레지아노 치즈",
//            "파마산 치즈", "새우", "감자", "소시지"]
//        let count = (1...toppingList.count).randomElement() ?? 0
//        var toppings: [String] = []
//        for _ in 0..<count {
//            toppings.append(toppingList.randomElement() ?? "")
//        }
//        return toppings
//    }
    
//    func prepare() {
//        print("\n\n===== Order =====")
//        print("준비 중: \(name)")
//        print("도우를 돌리는 중...")
//        print("소스를 뿌리는 중...")
//        print("토핑를 올리는 중...")
//        toppings.forEach { topping in
//            print(" \(topping)")
//        }
//    }
    
    func bake() {
        print("175도에서 25분 간 굽기")
    }
    
    func cut() {
        print("피자를 사선으로 자르기")
    }
    
    func box() {
        print("상자에 피자 담기")
    }
    
    func getName() -> String {
        return name
    }
}
```
```swift
class BasicPizza: Pizza {
    var name: String = ""
    var dough: Dough = BasicDough()
    var sauce: Sauce = BasicSauce()
    var veggies: [Veggie] = []
    var cheese: Cheese = BasicCheese()
    var pepperoni: Peppernoni = BasicPeppernoni()
    var clam: Clams = BasicClams()
    func prepare() {}
}

/*
 lazy 키워드를 사용하면 초기값을 주지 않아도 되지만 이게 맞는지는 모르겠군.
 애초에 프로토콜을 준수하려면 꼭 구현해야 하기 때문..
 */
class CheesePizza: Pizza {
    var name: String = ""
    lazy var dough: Dough = ingredienFactory.createDough()
    lazy var sauce: Sauce = ingredienFactory.createSauce()
    var veggies: [Veggie] = []
    lazy var cheese: Cheese = ingredienFactory.createCheese()
    var pepperoni: Peppernoni = BasicPeppernoni()
    var clam: Clams = BasicClams()
    
    let ingredienFactory: PizzaIngredientFactory
    
    init(ingredienFactory: PizzaIngredientFactory) {
        self.ingredienFactory = ingredienFactory
    }
    
    func prepare() {
        print("\n\n===== Order =====")
        print("준비 중: " + name)
//        dough = ingredienFactory.createDough()
//        sauce = ingredienFactory.createSauce()
//        cheese = ingredienFactory.createCheese()
    }
}
//class GreekPizza: Pizza {}
class PepperoniPizza: Pizza {
    var name: String = ""
    var dough: Dough = BasicDough()
    var sauce: Sauce = BasicSauce()
    var veggies: [Veggie] = []
    var cheese: Cheese = BasicCheese()
    var pepperoni: Peppernoni = BasicPeppernoni()
    var clam: Clams = BasicClams()
    
    let ingredienFactory: PizzaIngredientFactory
    
    init(ingredienFactory: PizzaIngredientFactory) {
        self.ingredienFactory = ingredienFactory
    }
    
    func prepare() {
        print("\n\n===== Order =====")
        print("준비 중: " + name)
        dough = ingredienFactory.createDough()
        sauce = ingredienFactory.createSauce()
        cheese = ingredienFactory.createCheese()
        clam = ingredienFactory.createClam()
    }
}
class ClamPizza: Pizza {
    var name: String = ""
    var dough: Dough = BasicDough()
    var sauce: Sauce = BasicSauce()
    var veggies: [Veggie] = []
    var cheese: Cheese = BasicCheese()
    var pepperoni: Peppernoni = BasicPeppernoni()
    var clam: Clams = BasicClams()
    
    let ingredienFactory: PizzaIngredientFactory
    
    init(ingredienFactory: PizzaIngredientFactory) {
        self.ingredienFactory = ingredienFactory
    }
    
    func prepare() {
        print("\n\n===== Order =====")
        print("준비 중: " + name)
        dough = ingredienFactory.createDough()
        sauce = ingredienFactory.createSauce()
        cheese = ingredienFactory.createCheese()
        pepperoni = ingredienFactory.createPepperoni()
    }
}
class VeggiePizza: Pizza {
    var name: String = ""
    var dough: Dough = BasicDough()
    var sauce: Sauce = BasicSauce()
    var veggies: [Veggie] = []
    var cheese: Cheese = BasicCheese()
    var pepperoni: Peppernoni = BasicPeppernoni()
    var clam: Clams = BasicClams()
    
    let ingredienFactory: PizzaIngredientFactory
    
    init(ingredienFactory: PizzaIngredientFactory) {
        self.ingredienFactory = ingredienFactory
    }
    
    func prepare() {
        print("\n\n===== Order =====")
        print("준비 중: " + name)
        dough = ingredienFactory.createDough()
        sauce = ingredienFactory.createSauce()
        cheese = ingredienFactory.createCheese()
        veggies = ingredienFactory.createVeggies()
    }
}
```
```swift
class NYPizzaStore: PizzaStore {
    func createPizza(_ type: String) -> Pizza {
        var pizza: Pizza = BasicPizza()
        let ingredientFactory = NYPizzaIngredientFactory()
        if type == "cheese"{ //치즈
            pizza = CheesePizza(ingredienFactory: ingredientFactory)
            pizza.name = "뉴욕 스타일 치즈 피자"
        } else if type == "pepperoni"{ //페퍼로니
            pizza = PepperoniPizza(ingredienFactory: ingredientFactory)
            pizza.name = "뉴욕 스타일 페퍼로니 피자"
        } else if type == "clam" { //조개
            pizza = ClamPizza(ingredienFactory: ingredientFactory)
            pizza.name = "뉴욕 스타일 조개 피자"
        } else if type == "veggie" { //야채
            pizza = VeggiePizza(ingredienFactory: ingredientFactory)
            pizza.name = "뉴욕 스타일 야채 피자"
        }
        return pizza
    }
}
class ChicagoPizzaStore: PizzaStore {
    func createPizza(_ type: String) -> Pizza {
        var pizza: Pizza = BasicPizza()
        let ingredientFactory = NYPizzaIngredientFactory()
        if type == "cheese"{ //치즈
            pizza = CheesePizza(ingredienFactory: ingredientFactory)
            pizza.name = "시카고 스타일 치즈 피자"
        } else if type == "pepperoni"{ //페퍼로니
            pizza = PepperoniPizza(ingredienFactory: ingredientFactory)
            pizza.name = "시카고 스타일 페퍼로니 피자"
        } else if type == "clam" { //조개
            pizza = ClamPizza(ingredienFactory: ingredientFactory)
            pizza.name = "시카고 스타일 조개 피자"
        } else if type == "veggie" { //야채
            pizza = VeggiePizza(ingredienFactory: ingredientFactory)
            pizza.name = "시카고 스타일 야채 피자"
        }
        return pizza
    }
}
class CaliforniaPizzaStore: PizzaStore {
    func createPizza(_ type: String) -> Pizza {
        var pizza: Pizza = BasicPizza()
        let ingredientFactory = NYPizzaIngredientFactory()
        if type == "cheese"{ //치즈
            pizza = CheesePizza(ingredienFactory: ingredientFactory)
            pizza.name = "캘리포니아 스타일 치즈 피자"
        } else if type == "pepperoni"{ //페퍼로니
            pizza = PepperoniPizza(ingredienFactory: ingredientFactory)
            pizza.name = "캘리포니아 스타일 페퍼로니 피자"
        } else if type == "clam" { //조개
            pizza = ClamPizza(ingredienFactory: ingredientFactory)
            pizza.name = "캘리포니아 스타일 조개 피자"
        } else if type == "veggie" { //야채
            pizza = VeggiePizza(ingredienFactory: ingredientFactory)
            pizza.name = "캘리포니아 스타일 야채 피자"
        }
        return pizza
    }
}
```
```swift
let nyStore = NYPizzaStore()
let chicagoStore = ChicagoPizzaStore()

var pizza = nyStore.orderPizza("veggie")
print("< 에단이 주문한 \(pizza.getName()) >")
print("dough:", pizza.dough)
print("sauce:", pizza.sauce)
print("cheese:", pizza.cheese)
print("veggies:", pizza.veggies)
print("pepperoni:", pizza.pepperoni)
print("clam:", pizza.clam)

pizza = chicagoStore.orderPizza("cheese")
print("< 조엘이 주문한 \(pizza.getName()) >")
print("dough:", pizza.dough)
print("sauce:", pizza.sauce)
print("cheese:", pizza.cheese)
print("veggies:", pizza.veggies)
print("pepperoni:", pizza.pepperoni)
print("clam:", pizza.clam)
/*
 ===== Order =====
 준비 중: 뉴욕 스타일 야채 피자
 175도에서 25분 간 굽기
 피자를 사선으로 자르기
 상자에 피자 담기
 < 에단이 주문한 뉴욕 스타일 야채 피자 >
 dough: FactoryPattern.ThinCrustDough
 sauce: FactoryPattern.MarinaraSauce
 cheese: FactoryPattern.ReggianoCheese
 veggies: [FactoryPattern.Garlic, FactoryPattern.Onion, FactoryPattern.Mushroom, FactoryPattern.RedPepper]
 pepperoni: FactoryPattern.BasicPeppernoni
 clam: FactoryPattern.BasicClams


 ===== Order =====
 준비 중: 시카고 스타일 치즈 피자
 175도에서 25분 간 굽기
 피자를 사선으로 자르기
 상자에 피자 담기
 < 조엘이 주문한 시카고 스타일 치즈 피자 >
 dough: FactoryPattern.ThinCrustDough
 sauce: FactoryPattern.MarinaraSauce
 cheese: FactoryPattern.ReggianoCheese
 veggies: []
 pepperoni: FactoryPattern.BasicPeppernoni
 clam: FactoryPattern.BasicClams
 Program ended with exit code: 0
 */
```
  </p>
</details>


## 6. 팩토리 메소드 패턴 vs 추상 팩토리 패턴
- 비슷하지만 공통점, 차이점이 있어요. 
- 둘 다 객체를 만드는 일을 합니다. 
- 팩토리 메소드 패턴 
    - 상속(Inheritance)으로 객체를 만듭니다. 정확히는 서브클래스로 객체를 만들죠. 
    - 클래스를 확장하고 팩토리 메소드를 오버라이드합니다. 
    - PizzaStore 인터페이스, ChicagoPizzaStore 클래스에서 팩토리 메소드인 createPizza() 를 통해 Pizza 인터페이스를 return합니다. 
    - 그렇기 때문에 인터페이스(Protocol)는 메소드명, 리턴값만 알면 되죠. 구현은 서브클래스에서 하니까요. 
    - 정리하면, 서브클래스로 객체를 만들려고 사용합니다. 서브클래스에서 객체를 만ㄷ늘어서 주니까 인터페이스는 추상 형식만 알면 됩니다. 
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/0c833de3-b93e-4487-93e9-cab6f5da2a76/image.png)
- 추상 팩토리 패턴 
    - 구성(Composition)으로 객체를 만듭니다. 그래서 PizzaStore가 각 지점에 맞는 Factory 인스턴스를 가지고 있지요. 
    - 추상 형식 PizzaIngredientFactory 을 제공합니다. 그리고 서브클래스인 NYPizzaIngredientFactory 가 제품을 만들죠. 
    - 그리고 각 서브클래스 팩토리가 제품을 생산합니다. 제품은 종류별로 인터페이스로 구성되어 있고, 팩토리에서 지점의 특성에 따라 맞는 제품을 리턴하죠. (도우라는 인터페이스에 클래스로 씬도우/두꺼운도우/치즈도우)
	- 제품군을 확대하려면 인터페이스를 바꿔야 합니다. 그럼 서브클래스도 바꿔야 하죠. 하지만, 추상 팩토리는 보통 제품군을 만들 때 사용해요. (NYStyle, ChicagoStyle 처럼) 그래서 막 자주 바뀌진 않아요. 각 지역 스타일이 계속 바뀌지는 않을테니까요. 
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/10eceded-4d96-43f4-a3ac-e91a0c921d63/image.png)


## 7. 정리하기 
- 초반에는 Simple Factory를 사용했는데 차이점은? 
    - 팩토리 메소드 패턴은 실제 구현하는 구상 클래스를 만들 때 createPizza() 추상 메소드가 정의되어 있는 확장해서 만들었습니다. 서브 클래스는 해당 메소드를 오버라이드 해서 결정합니다. 
    - 반면에 Simple Factory를 사용할 때는 팩토리가 PizzaStore 안에 포함되는 별개의 인스턴스 였습니다. 전자가 캡슐화도 되어있고 더 유연하다고 볼 수 있죠. 
    - Simple Factory는 객체 생성을 캡슐화했고, 일회성인 반면 팩토리 메소드 패턴은 재사용이 가능한 프레임워크를 제공합니다. 그리고 더 유연하죠. 어떤 피자 스토어를 만드느냐에 따라서 생성하는 제품을 마음대로 변경할 수 있으니까요. 
- 구상 클래스가 하나밖에 없더라도 팩토리 메소드 패턴은 유용합니다. 생산하는 부분과 사용하는 부분을 분리할 수 있기 때문입니다. 새 메뉴가 나오더라도 PizzaStore는 수정할 게 없죠.(느슨한 결합) 
- 여기서는 type을 String으로 사용했는데, 사실 enum 을 사용하는 것이 더 좋습니다. 


## 8. 생각해보기 
- Factory라는 이름으로 createPizza() 메소드를 가지고 있는 인터페이스를 선언하고 PizzaStore는 하나만 두고, Factory를 NY, Chicago, California로 늘립니다. 
- 이것은 뭐가 다를까. 각 장/단점이 있는걸까. 전략패턴?이라고 볼 수 있나.  

## Reference 
[https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223](https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223)
