업무를 하다보니, 이니셜라이저에 대해서 너무 두루뭉실하게 알고 사용하는 것 같아서 한 번 정리해둘까 합니다. 매번 정리해야지, 해야지 했었는데 드디어 하게 됐네요. 하핳... 개인적으로 상대빈도가 적다고 생각했던 것들은 문서에서 제외했습니다. 그래도 생각보다 도움이 많이 되는 것 같아 뿌듯합니다. 헷갈릴 때마다와서 다시보고 또보고 해야겠어요. 그럼 고고!😄. 

 
 ## Default Initializers
 `기본 이니셜라이저`			
 					
 모든 프로퍼티의 초기값이 설정되어 있고, 하나 이상의 커스텀 이니셜라이저를 정의되지 않았다면 모든 프로퍼티를 초기값으로 초기화하는 기본 이니셜라이저 `ShoppingListItem()` 를 제공합니다.
 
 ```swift
 class ShoppingListItem {
     var name: String?
     var quantity = 1
     var purchased = false
 }

 ShoppingListItem()
 ```
  




 
 ## Memberwise Initializers for Structure Types
 `값타입을 위한 멤버별 이니셜라이저`			
 				
 클래스와 다르게 구조체 타입에서는 초기값이 없고 커스텀 이니셜라이저를 정의하지 않아도 멤버별 이니셜라이저를 제공합니다.
 초기값이 지정되어 있다면 `Size()`, `Size(width:, height:)` 둘 다 지원합니다.
 초기값이 없다면 멤버별 이니셜라이저 `Size(width:, height:)`만 지원합니다.
 
 ```swift
 struct Size {
   var width = 0.0, height = 0.0
 }

 let twoByOne = Size()
 twoByOne.width //0.0
 twoByOne.height //0.0

 let twoByTwo = Size(width: 2.0, height: 2.0)
 twoByTwo.width //2.0
 twoByTwo.height //2.0
 ```
  


 
 ## Initializer Delegation for Value Types
 `값타입을 위한 이니셜라이저 위임`			
 				
 이니셜라이저에서 다른 이니셜라이저를 호출할 수 있어요. 이것을 `이니셜라이저 위임(Initializer Delegation)`이라고 합니다.
 하지만 주의할 점은 값 타입(struct, enum)과 참조 타입(class)의 위임 방법이 다릅니다.
 
 - 값 타입(struct, enum)은 상속을 지원하지 않기 때문에 자기 자신의 다른 이니셜라이저에서만 사용할 수 있습니다. `self.init`
 - 참조 타입(class)은 상속이 가능하므로 super class의 이니셜라이저를 subclass에서 호출 가능합니다. `super.init`
 
 이런 제약들이 이니셜라이저의 복잡성을 낮추고, 이니셜라이저가 의도치않게 사용되는 것을 방지해줍니다.
  

 
 커스텀 이니셜라이저 선언시 기본 이니셜라이저 또는 멤버별 이니셜라이저를 사용할 수 없습니다.
 
 ```swift
 struct Car {
   var name: String = "k7"
   var color: UIColor = .blue
   
   //커스텀 이니셜라이저 사용
   init(name: String) {
     self.name = name
   }
 }

 Car() //Missing argument for parameter 'name' in call
 Car(name: "name", color: .blue) //Extra argument 'color' in call
 Car(name: "ionic")
 ```
  


 
 커스텀 이니셜라이저를 사용하면서 기본 이니셜라이저, 멤버별 이니셜라이저를 사용하고 싶다면 extension을 사용해야 한다.
 
 이게 무슨 말이냐면,
 
 1. 초기값 없이 사용
 ```swift
 struct Size {
     var width = 0.0, height = 0.0
 }
 struct Point {
     var x = 0.0, y = 0.0
 }
 struct Rect {
   var origin: Point
   var size: Size
 }
 Rect(origin: , size: )
 ```
 
 2. 초기값 넣어주고 사용
 ```swift
 struct Rect {
   var origin = Point()
   var size = Size()
 }
 Rect() //초기값이 들어가있으니 이녀석이 기본으로 추가제공
 Rect(origin: , size: )
 ```
 
 3. 초기값, 멤버별, 커스텀 이니셜라이저를 모두 사용하고 싶음
 (커스텀 이니셜라이저를 구현해주되, init을 총 3개 작성해야 한다.)
 ```swift
 struct Rect {
     var origin = Point()
     var size = Size()
     init() {}
     init(origin: Point, size: Size) {
         self.origin = origin
         self.size = size
     }
     init(center: Point, size: Size) {
         let originX = center.x - (size.width / 2)
         let originY = center.y - (size.height / 2)
         //값 타입(struct, enum)은 상속을 지원하지 않기 때문에 자기 자신의 다른 이니셜라이저에서만 사용할 수 있습니다.
         self.init(origin: Point(x: originX, y: originY), size: size)
     }
 }
 Rect()
 Rect(origin: , size: )
 Rect(center: , size: )
 ```
 
 4. 하지만, extension에 커스텀 이니셜라이저를 구현해주면 이렇게 해도 가능하다.
 ```swift
 struct Rect {
     var origin = Point()
     var size = Size()
 }
 extension Rect {
     init(center: Point, size: Size) {
         let originX = center.x - (size.width / 2)
         let originY = center.y - (size.height / 2)
         //값 타입(struct, enum)은 상속을 지원하지 않기 때문에 자기 자신의 다른 이니셜라이저에서만 사용할 수 있습니다.
         self.init(origin: Point(x: originX, y: originY), size: size)
     }
 }

 Rect()
 Rect(origin: , size: )
 Rect(center: , size: )
 ```
  




 
 ## Class Inheritance and Initialization
 `클래스 상속과 초기화`			
 				
 모든 클래스의 저장 프로퍼티와 super class로부터 상속받은 모든 프로퍼티는 초기화 단계에서 반드시 초기값이 할당되어야만 합니다.
 Swift에서는 모든 프로퍼티가 초기값을 갖는 것을 보장하기 위해서 2가지 방법을 사용합니다.
 
 - Designated Initializers(지정 이니셜라이저)
     - 클래스의 primary 이니셜라이저 입니다. 클래스의 모든 프로퍼티를 초기화 합니다.
     - 클래스는 반드시 하나 이상의 지정 이니셜라이저가 있어야 합니다.
     - 값타입 이니셜라이저와 문법이 같으며 init 키워드를 사용한다.
 - Convenience Initializers(편의 이니셜라이저)
     - 편의 이니셜라이저는 초기화 단계에서 미리 지정된 값을 사용해서 최소한의 입력으로 초기화를 할 수 있도록 해주는 이니셜라이저입니다.
     - 편의 이니셜라이저 내부에 반드시 지정 이니셜라이저가 호출되어야만 합니다.
     - 지정 이니셜라이저 앞에 convenience 키워드를 붙여줍니다.
  


 
 ## Initializer Delegation for Class Types
 `클래스 타입을 위한 이니셜라이저 위임`			
 			
 클래스 타입의 이니셜라이저 위임을 위한 꼭 지켜야할 3가지 규칙이 있습니다.
 
 1. 지정 이니셜라이저는 반드시 직계 super class의 지정 이니셜라이저를 호출해야 합니다.
 2. 편의 이니셜라이저는 반드시 같은 클래스의 다른 이니셜라이저를 호출해야 합니다.
 3. 편의 이니셜라이저는 궁극적으로 지정 이니셜라이저를 호출해야 합니다.
 
 즉 이 내용을 좀 더 쉽게 요약하면,		
 			
 - 지정 이니셜라이저는 반드시 위임을 super class로 해야한다.
 - 편의 이니셜라이저는 반드시 위임을 같은 레벨에서 해야한다.
 
 ex) 1.			                
 
 ![](https://velog.velcdn.com/images/dev_kickbell/post/ffa11f7d-c6f9-426e-9fcf-b4ca3b234454/image.png)      
 
 ex) 2.			        
 
 ![](https://velog.velcdn.com/images/dev_kickbell/post/2872bfc4-59f3-404e-b151-6a58d8aed952/image.png)			        


 ## Two-Phase Initialization
 `2단계 이니셜라이저`				
 			
 먼저 Swift의 컴파일러가 4가지의 검사를 진행합니다. 검사를 진행한 후에 안전 검사를 기반으로 2단계 초기화가 실행됩니다. 
- 지정된 이니셜라이저는 상위 클래스 이니셜라이저에 위임하기 전에 해당 클래스에 의해 도입된 모든 속성이 초기화되었는지 확인합니다.
- 지정된 이니셜라이저는 상속된 속성에 값을 할당하기 전에 상위 클래스 이니셜라이저까지 위임해야 합니다. 그렇지 않은 경우 지정된 초기화 프로그램이 할당하는 새 값은 자체 초기화의 일부로 슈퍼클래스가 덮어씁니다.
- 편의 이니셜라이저는 속성(동일한 클래스에서 정의한 속성 포함) 에 값을 할당 하기 전에 다른 이니셜라이저에 위임해야 합니다 . 그렇지 않은 경우 편의 이니셜라이저가 할당하는 새 값은 자체 클래스의 지정된 이니셜라이저가 덮어씁니다.
- 이니셜라이저는 2단계 이니셜라이저의 1단계가 완료될 때까지 self의 값을 참조하거나 어떤 인스턴스 프로퍼티, 메소드를 읽거나 값을 호출 할 수 없습니다. ( 그래서 우리는 lazy var를 사용하지요. )


#### 1단계 이니셜라이저
1. 클래스에서 지정 또는 편의 이니셜라이저가 호출됩니다.
2. 해당 클래스의 새 인스턴스에 대한 메모리가 할당됩니다. 메모리가 아직 초기화되지 않았습니다.
3. 해당 클래스의 지정된 이니셜라이저는 해당 클래스에 의해 도입된 모든 저장된 속성에 값이 있음을 확인합니다. 이제 이러한 저장된 속성에 대한 메모리가 초기화됩니다.
4. 지정된 이니셜라이저는 자신의 저장된 속성에 대해 동일한 작업을 수행하기 위해 수퍼클래스 이니셜라이저에 전달합니다.
5. 이것은 체인의 맨 위에 도달할 때까지 클래스 상속 체인을 계속합니다.
6. 체인의 맨 위에 도달하고 체인의 마지막 클래스가 저장된 모든 속성에 값이 있음을 확인하면 인스턴스의 메모리가 완전히 초기화된 것으로 간주되고 1단계가 완료됩니다.
          
![](https://velog.velcdn.com/images/dev_kickbell/post/34f389bf-a513-4f90-8b49-9e892b28d143/image.png)				      

#### 2단계 이니셜라이저
7. 체인의 맨 위에서 다시 아래로 작업하면 체인의 지정된 각 초기화 프로그램에는 인스턴스를 추가로 사용자 정의할 수 있는 옵션이 있습니다. 이니셜라이저는 이제 self에 액세스할 수 있으며 속성을 수정하고 인스턴스 메서드를 호출하는 등의 작업을 수행할 수 있습니다.
8. 마지막으로, 체인의 모든 편의 이니셜라이저는 인스턴스를 사용자 정의하고 self로 작업할 수 있는 옵션이 있습니다.(꼭 그럴 필요는 없지만)
        
![](https://velog.velcdn.com/images/dev_kickbell/post/00242212-058f-4172-8a54-696a8720c32b/image.png)           


## Initializer Inheritance and Overriding
`이니셜라이저 상속 및 재정의`				
				
Swift에서는 기본 값으로 `sub class`에서 `super class`의 이니셜라이저를 상속하고 있지 않습니다. 왜냐하면 `super class` 이니셜라이저의 무분별한 상속으로 `sub class`에서 이것들이 잘못 초기화 되거나 기타 발생할 `Side effect`들을 방지하기 위함입니다. 

하지만 당연히 할 수는 있습니다. `override` 키워드를 사용하고, 그 후에 `super.init()`(이니셜라이저 위임)을 합니다. 만약에 아래의 주석처럼 이니셜라이저 위임보다 `numberOfWheels = 2` 을 먼저 한다면, 위에서 말했던 것처럼 컴파일러에서 경고를 표시합니다. 

```swift
class Vehicle {
    var numberOfWheels = 0
    var description: String {
        return "\(numberOfWheels) wheel(s)"
    }
}

let vehicle = Vehicle()
print("Vehicle: \(vehicle.description)")
// Vehicle: 0 wheel(s)

class Bicycle: Vehicle {
    override init() {
        //numberOfWheels = 2 //'self' used in property access 'numberOfWheels' before 'super.init' call
        super.init()
        numberOfWheels = 2
    }
}

let bicycle = Bicycle()
print("Bicycle: \(bicycle.description)")
// Bicycle: 2 wheel(s)
```

## Automatic Initializer Inheritance

`자동 이니셜라이저 상속`		
		
위에서 언급했던 것처럼 Swift에서는 기본적으로 `sub class`는 `super class`의 이니셜라이저를 기본적으로 상속하지 않습니다. 그러나 특정 조건이 충족되면 `super class`의 이니셜라이저가 자동으로 상속됩니다. 그래서 사실 많은 상황에서 직접 이니셜라이저를 `Override` 할 필요가 없고 그렇게 하는 것이 최소한의 노력으로 더 안전한 코드를 작성할 수 있습니다.

#### 이니셜라이저 자동 상속 조건
- `sub class`가 `Designated Initializers(지정 이니셜라이저)`를 정의하지 않으면 자동으로 모든 `super class` 의 지정 이니셜라이저를 상속합니다.
- `sub class`가 모든 `super class`의 지정 이니셜라이저를 구현한 경우에는 자동으로 모든 `super class`의 `Convenience Initializers(편의 이니셜라이저)`를 상속합니다.

ex) 1.			

Food 라는 클래스가 있습니다. 지정 이니셜라이저와 편의 이니셜라이저가 각 1개씩 있고 상속은 없으므로 이 클래스의 이니셜라이저 체인은 아래 이미지와 같습니다.
 
```swift
class Food {
    var name: String
    init(name: String) {
        self.name = name
    }
    convenience init() {
        self.init(name: "[Unnamed]")
    }
}
```         
          
![](https://velog.velcdn.com/images/dev_kickbell/post/6c939e1c-b5af-44dd-b4a7-4a4398a69309/image.png)           


```swift
let f1 = Food(name: "pizza")
f1.name //pizza
```

클래스는 구조체와는 다르게 `Memberwise Initializers`가 없으므로 Food는 `name`이라는 단일 인수를 사용하는 지정된 이니셜라이저를 제공합니다.
또 Food는 지금 `super class` 가 없으므로 초기화를 완료하기 위해 `super.init` 을 호출할 필요도 없지요.

```swift
let f2 = Food()
f2.name //[Unnamed]
```
 
또, Food는 `아규먼트`가 없는 편의 이니셜라이저를 제공합니다. 클래스 내부에서 저장프로퍼티의 초기값을 다 지정해주었을 때 제공해주는 기본 이니셜라이저와는 다릅니다.
편의 이니셜라이저 내부에서 `이니셜라이저 위임`으로 같은 레벨의 지정 이니셜라이저를 호출 `self.init(name:)`해주었기 때문에 아규먼트가 없는 편의 이니셜라이저를 제공하고 있는 것이지요. 그래서 f2에서 `name`을 호출하면 편의 이니셜라이저에 입력된 값인 `[Unnamed]` 가 출력되는 것입니다.


ex) 2.			

다음은 Food를 상속하고 있는 `RecipeIngredient(레시피 성분)` 클래스 입니다. `quantity`라는 Int 타입의 저장 프로퍼티가 추가되었고, 지정 이니셜라이저와 편의 이니셜라이저가 각 1개씩 총 2개가 정의되어 있습니다. 

```swift
class RecipeIngredient: Food {
    var quantity: Int
    init(name: String, quantity: Int) {
        //super.init(name: name) //Property 'self.quantity' not initialized at super.init call
        self.quantity = quantity
        super.init(name: name)
    }
    override convenience init(name: String) {
        self.init(name: name, quantity: 1)
    }
}
```
          
![](https://velog.velcdn.com/images/dev_kickbell/post/73d82ceb-765f-4313-9d05-f2997e4bccd4/image.png)           

#### init(name: String, quantity: Int)

이 클래스의 모든 속성을 채우는 데 사용할 수 있는 지정 이니셜라이저 `init(name: String, quantity: Int)`가 있습니다. `super class`의 속성 name을 가지고 있고 내부 저장 프로퍼티인 quantity 도 있습니다. 		

하지만 여기서 `중요한 것은 바로 순서`입니다.

1. 이니셜라이저는 `RecipeIngredient`에 의해 도입된 유일한 새 속성인 quantity에 아규먼트로 전달된 quantity 를 할당하는 것부터 시작합니다.
2. 그렇게 한 후, 이니셜라이저는 `super class` 의 이니셜라이저 위임`super.init(name: name)`을 합니다.

자세히 보아야 할 부분은 이것이 위에서 말했던 위의 `2단계 초기화의 안전점검 1`을 만족하는 과정이라는 것입니다.

> 지정된 이니셜라이저는 상위 클래스 이니셜라이저에 위임하기 전에 해당 클래스에 의해 도입된 모든 속성이 초기화되었는지 확인합니다.

또, `super.init(name: name)`의 위치를 `self.quantity = quantity` 위로 옮겨보면 주석과 같은 에러가 발생하는데 이것 또한 `2단계 초기화의 안전점검 4`를 만족하는 과정입니다.

> 이니셜라이저는 2단계 이니셜라이저의 1단계가 완료될 때까지 self의 값을 참조하거나 어떤 인스턴스 프로퍼티, 메소드를 읽거나 값을 호출 할 수 없습니다.


#### override convenience init(name: String)

`RecipeIngredient`는 또한 `name`만으로 인스턴스를 생성할 수 있게 하는 `편의 이니셜라이저`를 정의합니다. 이 편의 이니셜라이저는 `quantity`없이 생성된 모든 인스턴스에 대해 의 `수량 1을 할당`합니다. 

이렇게 하면 `1. 더 빠르고 편리하게 생성할 수 있으며,` 
```swift
let r = RecipeIngredient(name: "이름만 넣었지만 수량이 1로 할당")
r.quantity // 1
```
`2. 여러 개의 인스턴스를 생성할 때 코드 중복을 방지`할 수 있습니다.
```swift
//수량은 다 1로 고정할 것이다. 이런 경우라면 굳이 필요없는 코드 중복이 발생한다. 
//100개면 100번의 quantity: 1 코드 중복이 발생. 
RecipeIngredient(name: "sugar", quantity: 1) 
RecipeIngredient(name: "salt", quantity: 1)
RecipeIngredient(name: "olive oli", quantity: 1)
RecipeIngredient(name: "potato", quantity: 1)
RecipeIngredient(name: "onion", quantity: 1)
```

또, 이 편의 이니셜라이저는 상위 클래스인 Food의 `지정 이니셜라이저`와 동일한 매개변수를 사용합니다. 즉, 이 편의 이니셜라이저는 상위 클래스의 `지정 이니셜라이저`를 `재정의`하므로 `override` 키워드를 사용해서 표시해야 합니다. 상위 클래스의 `편의 이니셜라이저`가 자동으로 입력되거나 그런 것이 아닙니다. Food에 있는 편의 이니셜라이저는 `convenience init()` 으로 매개변수가 분명히 다릅니다. `RecipeIngredient`의 `편의 이니셜라이저`는 `Food`에 있는 것과 `엄연히 다른 것`이며, 다만 `Food`의 `지정 이니셜라이저를 재정의` 하므로 `override` 키워드를 사용하고 있는 것입니다. 여기서 또 자세히 봐야 할 부분이 재정의를 하고 있음에도 편의 이니셜라이저에서는 `super.init`을 호출하지않고 `self.init`을 해주고 있다는 부분이겠죠.
			
그리고 `RecipeIngredient`는 내부에서 `init(name: String)` 이니셜라이저를 편의 이니셜라이저로 제공하지만, 상위 클래스의 `지정된 이니셜라이저 모두의 구현을 제공`했습니다. 따라서 `RecipeIngredient`는 위에서 배웠던 `이니셜라이저 자동 상속 조건`의 2번째에 근거하여 상위 클래스의 모든 `편의 이니셜라이저도 자동으로 상속`합니다.

> sub class가 모든 super class의 지정 이니셜라이저를 구현한 경우에는 자동으로 모든 super class의 Convenience Initializers(편의 이니셜라이저)를 상속합니다.
 
```swift
//이게 되는 것이지요.
let r = RecipeIngredient()
r.name //"[Unnamed]"
```
       
ex) 3.						

마지막 클래스는 `ShoppingListItem`이라는 `RecipeIngredient`의 하위 클래스입니다. `ShoppingListItem`는 쇼핑 목록에 나타나는 레시피 성분을 모델링합니다. 이 클래스에서는 구매여부를 나타내는 Bool 속성인 `purchased`과 아이템에 대한 설명을 제공하는 `description` 속성이 새롭게 추가되었습니다. 둘 다 초기값을 제공하고 있는데요, 생각해보면 모든 물품은 `unpurchased` 상태로 시작됩니다. 따라서 초기값을 `unpurchased` 상태로 시작하기 위해 이니셜라이저를 따로 정의하고 있지 않습니다. 

```swift
class ShoppingListItem: RecipeIngredient {
    var purchased = false
    var description: String {
        var output = "\(quantity) x \(name)"
        output += purchased ? " ✔" : " ✘"
        return output
    }
}

//ShoppingListItem의 초기값을 지정하고 있어서 자동으로 제공되는 기본 () 이니셜라이저 외에도 
//Food의 편의 이니셜라이저도 상속하기 때문에 .name값도 불러올 수가 있는 것이다.
let s = ShoppingListItem()
s.name // "[Unnamed]"
s.purchased // false
s.description // "1 x [Unnamed] ✘"

//RecipeIngredient의 편의 이니셜라이저
let r = ShoppingListItem(name: "onion")
r.name // "onion"
r.quantity // 1

//RecipeIngredient의 지정 이니셜라이저
let r2 = ShoppingListItem(name: "potato", quantity: 99)
r2.name // "potato"
r2.quantity // 99
```
`ShoppingListItem`은 모든 속성에 대한 기본값을 제공하고 있고, 이니셜라이저 자체를 정의하지 않기 때문에 위에서 보았던 `이니셜 라이저 자동상속조건`에 따라 상위 클래스에서 지정된 모든 `지정 이니셜라이저`와 `편의 이니셜라이저`를 자동으로 상속합니다.
            
![](https://velog.velcdn.com/images/dev_kickbell/post/183e03ed-c3c3-4d61-8f2c-1ff90d93dace/image.png)               


 ## Failable Initializers
 		
 `실패가능한 이니셜라이저`			
 		
초기화 중에 실패할 가능성이 있는 이니셜라이저에 ? 를 사용할 수 있습니다. 이니셜라이저는 이름이 따로 있는 것이 아니라 파라미터로 구분하기 때문에 실패 가능한 초기자와 실패 불가능한 초기자를 같은 파라미터 타입과 이름으로 동시에 사용할 수 없습니다. 

```swift
class Food {
    var name: String
    init(name: String) {
        self.name = name
    }
//    invalid redeclaration of 'init(name:)'
//    'init(name:)' previously declared here
//    init?(name: String) {
//        self.name = name
//    }
}
```

실패 가능한 초기자는 반환값으로 `옵셔널 값`을 생성합니다. 초기화에 실패하는 부분에서 nil을 반환하는 코드를 작성해 초기화가 실패했다는 것을 나타낼 수 있습니다. 엄밀히 말하면 `init은 값을 반환하지 않습니다.` 무슨 말이냐면, 이니셜라이징에 `성공해도 값을 반환하진 않는다`는 뜻입니다. 물론 실패 가능한 이니셜라이저 같은 경우는 `실패시에 nil을 반환`하지만요. 

```swift
let wholeNumber: Double = 12345.0 // 나머지가 0이므로 Int로 변환가능
let pi = 3.14159 // 나머지가 14159 이므로 Int로 변환 불가능

// init?(exactly source: Double)
let value1 = Int(exactly: wholeNumber) // 12345
let value2 = Int(exactly: pi) // nil
```

`빈것(Empty)`과 `nil`은 다릅니다. 그래서 `if species.isEmpty { return nil }` 를 해주지 않으면 `""` 값으로 초기화가 됩니다. 이것은 의도한 바가 아니므로 `isEmpty 를 사용해서 실패시 nil을 반환`하도록 구현해야 합니다.

```swift
struct Animal {
    let species: String
    init?(species: String) {
        if species.isEmpty { return nil } // 중요. 이거 없으면 "" 을 넣어도 초기화가 성공합니다.
        self.species = species
    }
}

let a1 = Animal(species: "")  // isEmpty: true -> 초기화 실패
let a2 = Animal(species: " ") // 공백도 문자열 값이므로 isEmpty: false -> 초기화 성공
```
 					
#### Failable Initializers for Enumerations			
enum에서도 실패 가능한 이니셜라이저를 사용할 수 있습니다. 

```swift
enum TemperatureUnit {
    case kelvin, celsius, fahrenheit
    init?(symbol: Character) {
        switch symbol {
        case "K":
            self = .kelvin
        case "C":
            self = .celsius
        case "F":
            self = .fahrenheit
        default:
            return nil
        }
    }
}

let fahrenheitUnit = TemperatureUnit(symbol: "F") //fahrenheit
let unknownUnit = TemperatureUnit(symbol: "X") // nil
```
사실, `Enum`에서는 따로 init? 구현하지 않아도 Swift에서 `rawValue` 값을 사용한 `init?(rawValue: Self.RawValue)`를 제공해주고 있습니다. 따라서 아래처럼 구현해도 됩니다.

```swift
enum TemperatureUnit: Character {
    case kelvin = "K"
    case celsius = "C"
    case fahrenheit = "F"
}

let fahrenheitUnit = TemperatureUnit(rawValue: "F") // fahrenheit
let unknownUnit = TemperatureUnit(rawValue: "X") // nil
```

#### Propagation of Initialization Failure

실패가능한 초기자에서 실패가 발생하면 `즉시 관련된 초기자가 중단` 됩니다. 이것을 활용하면 `특정 상황에만 실패하는 이니셜라이저`를 만들 수 있습니다. 

```swift
class Product {
    let name: String
    init?(name: String) {
        if name.isEmpty { return nil }
        self.name = name
    }
}

class CartItem: Product {
    let quantity: Int
    init?(name: String, quantity: Int) {
        if quantity < 1 { return nil }
        self.quantity = quantity
        super.init(name: name)
    }
}

let c1 = CartItem(name: "sock", quantity: 2) // 초기화 성공
let c2 = CartItem(name: "shirt", quantity: 0) // quantity < 1 때문에 초기화 실패 nil
let c3 = CartItem(name: "", quantity: 5) // name == isEmpty 때문에 초기화 실패 nil
```

#### Overriding a Failable Initializer

수퍼클래스의 실패가능한 이니셜라이저를 서브클래스에서 실패불가능한 이니셜라이저로 오버라이딩 할 수 있습니다. `실패가능한 초기자`를 `실패불가능한 초기자`로 `오버라이드` 할 수 있지만, 그 반대는 `불가능` 합니다.

```swift
class Document {
    var name: String?
    // this initializer creates a document with a nil name value
    init() {}
    // this initializer creates a document with a nonempty name value
    init?(name: String) {
        if name.isEmpty { return nil }
        self.name = name
    }
}

class AutomaticallyNamedDocument: Document {
    override init() {
        super.init()
        self.name = "[Untitled]"
    }
    override init(name: String) { // 실패가능한 이니셜라이저를 실패불가능한 이니셜라이저로 오버라이딩
        super.init()
        if name.isEmpty {
            self.name = "[Untitled]"
        } else {
            self.name = name
        }
    }
}
```
`AutomaticallyNamedDocument`의 `override init(name: String)` 에서 이니셜라이저 위임을 
`super.init(name: name)` 으로 하면 `실패불가능한 이니셜라이저는 실패가능한 super class의 init?(name: String)에 연결할 수 없습니다.` 라는 에러가 발생합니다. 이것은 이미 오버라이딩해서 실패불가능한 이니셜라이저로 만들었는데 그걸 다시 실패가능한 이니셜라이저로 연결하려고 하니 발생하는 에러입니다. 

## Required Initializers
		
`필수 초기자`		
		
모든 `sub class` 에서 반드시 구현해야 하는 초기자에는 아래 예제와 같이 `required` 키워드를 붙여 줍니다. 필수초기자를 상속받은 `sub class`에서도 반드시 `required` 키워드를 붙여서 다른 `sub class`에게도 이 초기자는 필수 초기자라는 것을 알려야 합니다.

```swift
class SomeClass {
    required init() {
        // initializer implementation goes here
    }
}

class SomeSubclass: SomeClass {
    required init() {
        // subclass implementation of the required initializer goes here
    }
}
```
또한 필수 이니셜라이저의 모든 하위 클래스 구현 전에 필수 수정자를 작성하여 이니셜라이저 요구 사항이 체인의 하위 클래스에 적용됨을 나타내야 합니다. 필수 지정 이니셜라이저를 재정의할 때 `override` 키워드는 없어도 괜찮습니다. 그리고 만약 상속된 이니셜라이저로 요구 사항을 충족할 수 있다면 하위 클래스에서 필수 이니셜라이저를 작성하지 않아도 됩니다.  

## Required Initializers For Protocols
		
`프로토콜을 위한 필수 초기자`	
			
프로토콜은 준수하는 유형으로 구현되는 특정 이니셜라이저를 요구할 수 있습니다. 이 이니셜라이저는 중괄호나 이니셜라이저 본문은 사용하지 않습니다. 지정 이니셜라이저 또는 편의 이니셜라이저로 이니셜라이저 요구 사항을 구현할 수 있습니다. 참조 타입의 경우 이니셜라이저 앞에 `required` 키워드가 필요하지만, 값 타입의 경우 상속이 없기 때문에 `required` 가 필요하지 않습니다. 

```swift
protocol SomeProtocol {
    init(someParameter: Int)
}

class SomeClass: SomeProtocol {
    required init(someParameter: Int) {
        
    }
}

struct SomeStruct: SomeProtocol {
    init(someParameter: Int) {
        
    }
}
```

상속을 필요하지 않은 최종 클래스라면 보통 `final` 키워드를 사용합니다. 이런 최종클래스는 서브클래싱 할 수 없기 때문에 `final` 키워드로 표시된 클래스에 `required` 키워드로 프로토콜 이니셜라이저 구현을 표시할 필요가 없습니다. 

```swift
protocol SomeProtocol {
    init(someParameter: Int)
}

final class ViewContorller: SomeProtocol {
    init(someParameter: Int) { // required 키워드 없음. 상속을 안할거니까.
        
    }
}
```

서브클래스가 슈퍼클래스의 지정 이니셜라이저를 재정의하고 프로토콜에서 일치하는 이니셜라이저 요구사항도 구현하는 경우 `required` 및 `override` 키워드로 이니셜라이저 구현을 표시합니다.					

```swift
protocol SomeProtocol {
    init()
}

class SomeSuperClass {
    init() {
        // initializer implementation goes here
    }
}

class SomeSubClass: SomeSuperClass, SomeProtocol {
    // "required" from SomeProtocol conformance; "override" from SomeSuperClass
    required override init() {
        // initializer implementation goes here
    }
}
```

## Setting a Default Property Value with a Closure or Function
				
`클로저나 함수를 이용해 기본 프로퍼티 값을 설정하기`
			
기본 값 설정이 단순히 값을 할당하는 것이 아니라 다소 복잡한 계산을 필요하다면 클로저나 함수를 이용해 값을 초기화 하는데 이용할 수 있습니다. 기본값을 지정하기 위해 클로저를 사용하는 형태의 코드는 보통 다음과 같습니다. 클로저를 초기자에서 사용하면 클로저 안에 self나 다른 프로퍼티를 사용할 수 없습니다. 그 이유는 클로저가 실행될 시점에 다른 프로퍼티는 초기화가 다 끝난 것이 아니기 때문입니다.

```swift
import UIKit

let customLabel: UILabel = {
    let label = UILabel()
    label.text = "temp"
    label.textColor = .brown
    label.font = UIFont.systemFont(ofSize: 14)
    return label
}()


class ViewController: UIViewController {
    lazy var customBuntton: UIButton = {
        let button = UIButton()
        button.setTitle("temp", for: .normal)
        button.setTitleColor(.white, for: .normal)
        button.setTitleColor(.black, for: .selected)
        button.backgroundColor = .blue
        button.setImage(UIImage(systemName: "house"), for: .normal)
        button.addTarget(self, action: #selector(tap), for: .touchUpInside)
        button.center = view.center
        return button
    }()

    @objc func tap() {
        
    }
}

struct Chessboard {
    let boardColors: [Bool] = {
        var temporaryBoard = [Bool]()
        var isBlack = false
        for i in 1...8 {
            for j in 1...8 {
                temporaryBoard.append(isBlack)
                isBlack = !isBlack
            }
            isBlack = !isBlack
        }
        return temporaryBoard
    }()
    func squareIsBlackAt(row: Int, column: Int) -> Bool {
        return boardColors[(row * 8) + column]
    }
}
```

## Reference
			
[https://docs.swift.org/swift-book/LanguageGuide/Initialization.html](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html)











 




