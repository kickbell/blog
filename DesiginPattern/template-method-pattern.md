
# Template Method Pattern(템플릿 메소드 패턴)
> - 알고리즘의 골격을 정의합니다. 알고리즘의 일부 단계를 서브클래스에서 구현할 수 있고, 알고리즘의 구조는 그대로 유지하면서 알고리즘의 특정 단계를 서브클래스에서 재정의 할 수도 있습니다.

## 1. Coffee와 Tea클래스 만들기
- 커피와 홍차는 만드는 방법이 유사합니다. 
- 대략적으로 코드로 구현한다면 아래와 같을 겁니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/851f44f3-135d-4302-9633-603420e447bc/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/baa7da67-f8f4-4ada-831a-ae427275d52a/image.png)

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
//카페인 음료
protocol CaffeineBeverage {
    func prepareRecipe()
    func boilWater()
    func pourInCup()
}
    
class Coffee: CaffeineBeverage {
    func prepareRecipe() {
        boilWater()
        brewCoffeeGrinds()
        pourInCup()
        addSugarAndMilk()
    }
    
    func boilWater() {
        print("물 끓이는 중")
    }
    
    func brewCoffeeGrinds() {
        print("필터로 커피를 우려내는 중")
    }
    
    func pourInCup() {
        print("컵에 따르는 중")
    }
    
    func addSugarAndMilk() {
        print("설탕과 우유를 추가하는 중")
    }
}
    
class Tea: CaffeineBeverage {
    func prepareRecipe() {
        boilWater()
        steepTeaBag()
        addLemon()
        pourInCup()
    }
    
    func boilWater() {
        print("물 끓이는 중")
    }
    
    func steepTeaBag() {
        print("찻잎을 우려내는 중")
    }
    
    func addLemon() {
        print("레몬을 추가하는 중")
    }
    
    func pourInCup() {
        print("컵에 따르는 중")
    }
}
    
let coffee = Coffee()
let tea = Tea()

print("--- 커피 ----")
coffee.prepareRecipe()
print("\n")
print("--- 홍차 ----")
tea.prepareRecipe()
/*
 --- 커피 ----
 물 끓이는 중
 필터로 커피를 우려내는 중
 컵에 따르는 중
 설탕과 우유를 추가하는 중


 --- 홍차 ----
 물 끓이는 중
 찻잎을 우려내는 중
 레몬을 추가하는 중
 컵에 따르는 중
 */
```
  </p>
</details>


## 2. 템플릿 메소드 패턴 적용하기 
- 생각해보면 커피와 홍차 클래스를 더 추상화할 수 있을 것 같습니다. 커피를 우려내나 홍차 티백을 우려내나 어차피 우려내는 것이고, 설탕과 우유를 추가하나 레몬을 추가하나 어차피 첨가물인 무엇인가를 추가하는 것입니다. 
- 기존에 달랐던 메소드를 삭제하고 brew(), addCondiments()으로 수정합니다. 
- 그리고 각 클래스에 다르게 구현해야 하는 brew(), addCondiments() 메소드를 추상메소드로 선언합니다. 
- 여기서 원래는 final func prepareRecipe() 로 지정해서 오버라이드를 방지해야 하나, Swift는 protocol 에서 final 키워드를 사용하지 못하므로 제외합니다. 서브클래스에서 오버라이드가 가능할 수 있습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/f0d01cf4-9dba-4791-8160-c1933158a2bc/image.png)


<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
//카페인 음료
protocol CaffeineBeverage {
    //다른 언어 였다면 prepareRecipe 앞에 final 키워드를 넣어서 오버라이드를
    //방지했을 것이나 swift는 사용할 수 없다.
    func prepareRecipe()
    
    func boilWater()
    func pourInCup()
    
    //추상메소드
    func brew()
    func addCondiments() //첨가물 
}

extension CaffeineBeverage {
    func prepareRecipe() {
        boilWater()
        brew()
        addCondiments()
        pourInCup()
    }
    
    func boilWater() {
        print("물 끓이는 중")
    }
    
    func pourInCup() {
        print("컵에 따르는 중")
    }
}
```
```swift
class Coffee: CaffeineBeverage {
    func brew() {
        print("커피를 우려내는 중")
    }
    
    func addCondiments() {
        print("우유와 설탕을 추가하는 중")
    }
}
    
class Tea: CaffeineBeverage {
    func brew() {
        print("찻잎을 우려내는 중")
    }
    
    func addCondiments() {
        print("레몬을 추가하는 중")
    }
}

let coffee = Coffee()
let tea = Tea()

print("--- 커피 ----")
coffee.prepareRecipe()
print("\n")
print("--- 홍차 ----")
tea.prepareRecipe()
/*
 --- 커피 ----
 물 끓이는 중
 필터로 커피를 우려내는 중
 컵에 따르는 중
 설탕과 우유를 추가하는 중


 --- 홍차 ----
 물 끓이는 중
 찻잎을 우려내는 중
 레몬을 추가하는 중
 컵에 따르는 중
 */
```
  </p>
</details>


## 3. 템플릿 메소드 패턴 

- 알고리즘의 템플릿(틀)을 만듭니다. 틀은 그저 여러 단계로 알고리즘을 정의한 메소드이죠. 
- 여러 단계 가운데 하나 이상의 단계가 추상 메소드로 정의되고, 서브클래스에서 구현됩니다. 
- 템플릿 메소드 패턴 적용 전 
    - Coffee와 Tea 클래스가 각자 알고리즘을 수행합니다.
    - Coffee와 Tea 클래스에 중복된 코드가 있습니다.
    - 알고리즘이 바뀌면 서브클래스를 열어서 하나하나 다 고쳐야 합니다.
    - 알고리즘 지식과 구현 방법이 여러 클래스에 분산되어 있습니다.
- 템플릿 메소드 패턴 적용 후 
    - CaffeineBeverage 클래스에서 작업을 처리합니다. 알고리즘을 독점하죠. 
    - Coffee와 Tea 클래스에 중복된 코드가 없습니다.
    - 추상메소드를 제외한 공통 부분이 바뀌면 인터페이스 한 군데만 고치면 됩니다. 
    - CaffeineBeverage 클래스에 알고리즘 지식이 집중되어 있고, 일부 구현만 서브클래스에 의존합니다. 
    
![](https://velog.velcdn.com/images/dev_kickbell/post/e5ae35cf-9c8a-4af1-b49c-6221d9c4d92b/image.png)

## 4. 템플릿 메소드 속의 후크(hook)
- 후크는 추상 클래스에서 선언되지만, 기본적인 내용만 구현되어 있거나 아무 코드도 들어있지 않은 메소드 입니다. 
- 알고리즘에서 메소드가 선택이라면 후크를 쓰면 되고, 필수라면 추상 메소드를 쓰면 될 겁니다.
- 후크를 사용하면 상황에 따라 알고리즘 진행을 변경할 수 있습니다. 아래 코드에서는 서브클래스가 추상 클래스에서 진행되는 작업을 처리할지 말지 결정하게 하는 기능을 부여하는 용도로 쓰였습니다. 
- 템플릿 메소드에서는 알고리즘의 단계를 너무 자잘하게 쪼개놓으면 코드가 복잡해지고, 그렇다고 너무 큼직하게 쪼개놓으면 유연성이 떨어질 수 있습니다. 여기서 필수가 아닌 부분은 후크로 구현하면 코드가 조금 줄어들 수도 있을테니 잘 생각하고 설계를 해야 합니다.


<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
//카페인 음료
protocol CaffeineBeverage {
    //다른 언어 였다면 prepareRecipe 앞에 final 키워드를 넣어서 오버라이드를
    //방지했을 것이나 swift는 사용할 수 없다.
    func prepareRecipe()
    
    func boilWater()
    func pourInCup()
    
    //hook 메소드
    func customerWantsCondiments() -> Bool
    
    //추상메소드
    func brew()
    func addCondiments()
}

extension CaffeineBeverage {
    func prepareRecipe() {
        boilWater()
        brew()
        pourInCup()
        if customerWantsCondiments() {
            addCondiments()
        }
    }
    
    func boilWater() {
        print("물 끓이는 중")
    }
    
    func pourInCup() {
        print("컵에 따르는 중")
    }
    
    func customerWantsCondiments() -> Bool {
        return false
    }
}
```
```swift
class Coffee: CaffeineBeverage {
    func brew() {
        print("커피를 우려내는 중")
    }
    
    func addCondiments() {
        print("우유와 설탕을 추가하는 중")
    }
    
    func customerWantsCondiments() -> Bool {
        let result = getUserInput()
        if result == "y" {
            return true
        }
        return false
    }
    
    func getUserInput() -> String {
        print("커피에 우유와 설탕을 넣을까요? (y/n)")
        return readLine() ?? ""
    }
}
    
let coffee = Coffee()
let tea = Tea()

print("--- 커피 ----")
coffee.prepareRecipe()
/*
 --- 커피 ----
 물 끓이는 중
 커피를 우려내는 중
 컵에 따르는 중
 커피에 우유와 설탕을 넣을까요? (y/n)
 y
 우유와 설탕을 추가하는 중
 
 --- 커피 ----
 물 끓이는 중
 커피를 우려내는 중
 컵에 따르는 중
 커피에 우유와 설탕을 넣을까요? (y/n)
 n
 Program ended with exit code: 0
 */
```
  </p>
</details>


## 5. 할리우드 원칙(Hollywood Principle) 
> - 먼저 연락하지 마세요. 저희가 연락 드리겠습니다. 
> - 저수준 구성요소가 시스템에 접속할 수는 있지만, 그 구성요소를 언제/어떻게 사용할 지는 고수준 구성요소가 결정합니다. 

- 할리우드 원칙을 활용하며 의존성 부패(dependency rot)을 방지할 수 있습니다. 
- 의존성 부패(dependency rot)란, 어떤 고수준 구성요소가 저수준 구성요소에 의존하고, 그 저수준 구성요소는 다시 고수준 구성요소에 의존하고, 그 고수준 구성요소는 또 다른 구성요소에 의존하고, 그 또 다른 구성요소는 또 저수준 구성요소에 의존하고...와 같이 의존성이 복잡하게 꼬여있는 상황을 말합니다. 
- 이렇게 되버리면, 시스템이 어떻게 디자인 되어 있는지 아무도 알아볼 수 없게 되죠. 
- 할리우드 원칙은 저수준 구성요소가 시스템에 접속할 수는 있지만, 그 구성요소를 언제/어떻게 사용할 지는 고수준 구성요소가 결정합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/fe949b43-cf4c-4df7-ab09-b9e9ae578d1f/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/0a400987-788c-4d7d-896a-284c9ee2abdb/image.png)

## 6. 할리우드 원칙과 템플릿 메소드 패턴 
- 템플릿 메소드 패턴을 사용하면, 할리우드 원칙을 지키게 됩니다. 
- CaffeineBerverage는 고수준 구성요소입니다. 음료를 만드는 방법에 해당하는 알고리즘을 독점하고 있죠(final로 오버라이드가 불가능하다는 전제하에). 그리고 각 서브클래스에서 메소드 구현이 필요한 경우에만 서브클래스를 불러냅니다. 
- 우리가 구현한 코드와 아래 그림을 예시로 들면, 저수준 구성요소인 Coffee/Tea 서브클래스는 특화된 메소드 구현을 제공하는데만 쓰입니다. 고수준 구성요소인 CaffeineBerverage에서 호출 "당하기" 전까지는 추상 클래스를 직접 호출하지 않죠. 
- 사실 위에 있는 코드에서도 boilWater(), pourInCup()과 같은 코드를 호출할 수 없는 것은 아니죠. 하지만, 이러면 순환 의존성이 생겨버리는 거죠. 그래서 호출 "당하는"게 중요합니다.
- 템플릿 메소드 패턴 말고도 팩토리 메소드, 옵저버 패턴이 할리우드 원칙을 준수합니다.


![](https://velog.velcdn.com/images/dev_kickbell/post/ee7b62be-3811-4799-b85a-49b8f8e7122e/image.png)

> - 의존성 뒤집기 원칙과 할리우드 원칙 						
> - DIP는 될 수 있으면 구상 클래스를 줄이고 추상화된 인터페이스를 사용하라는 원칙이죠. 할리우드 원칙이나 DIP나 객체를 분리한다는 목표는 공유하지만, 의존성을 피하는 방법에 있어서 DIP가 훨씬 더 강하고 일반적인 내용을 담고 있습니다. 할리우드 원칙은 저수준 구성요소를 다양하게 사용할 수 있으면서도, 다른 클래스가 구성요소에 너무 의존하지 않게 만들어 주는 디자인 구현 기법을 제공합니다. 

## 7. swift의 sort() 템플릿 메소드 
- 변형된 템플릿 메소드가 있답니다. Swift에서 기본적으로 제공해주는 sort() 같은 것들인데요. 
- 음, 무슨 말이냐면 우리가 위에서 구현한 코드의 템플릿 메소드는 prepareRecipe() 였죠? 얘는 final로 되어 있어 우리는 절대로 수정할 수 없었어요. 오버라이드가 불가능하니까. (다른 언어에서는 말이죠.)
- 그리고, Coffee/Tea 서브클래스에서 특화된 메소드를 구현해줬죠. 이것을 아래의 코드와 대응을 해보면 됩니다. 
- swift 에서 class/struct/enum 등의 대소비교를 위해서는 Comparable 프로토콜을 준수해야 하죠. 객체를 비교할 건데, 객체의 어떤 속성을 비교해야 하는지 알려줘야 하니까요. 
- Comparable은 대소비교를 위한 거고, Comparable이 준수하고 있는 Equatable도 준수되어야 합니다. 즉, static func < (), static func == () 이 2가지 메소드는 우리가 만드는 서브 클래스에서 구현을 해줘야 된다는 거죠. 
- 이런 부분이 sort()가 템플릿 메소드 라는 것입니다. sort()는 알고리즘이고 내부에는 mergeSort(), swap() 같은 내부 로직이 있겠죠? 우리가 구현한 prepareRecipe() 처럼요. 그 내부 로직에 static func < (), static func == () 도 있는겁니다. 대소 비교나 같은지 여부를 판단해야 정렬을 할 수 있을테니까요. 


![](https://velog.velcdn.com/images/dev_kickbell/post/a1242bd4-8ebd-4191-aee8-71f1cbd5a0d5/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/b1e1602b-5050-473f-8b1f-d787b4a4802f/image.png)


<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class Duck: Comparable {
    static func < (lhs: Duck, rhs: Duck) -> Bool {
        return lhs.weight < rhs.weight
    }
    
    static func == (lhs: Duck, rhs: Duck) -> Bool {
        return lhs.weight == lhs.weight
    }
    
    let name: String
    let weight: Int
    
    init(name: String, weight: Int) {
        self.name = name
        self.weight = weight
    }
    
    func toString() -> String {
        return "\(name) 체중: \(weight)"
    }
}
    
var ducks = [
    Duck(name: "A", weight: 8),
    Duck(name: "E", weight: 2),
    Duck(name: "C", weight: 7),
    Duck(name: "D", weight: 5),
    Duck(name: "B", weight: 10)
]

print("\n정렬 전:")
ducks.forEach { print($0.toString()) }

ducks.sort()

print("\n정렬 후:")
ducks.forEach { print($0.toString()) }

/*
 정렬 전:
 A 체중: 8
 E 체중: 2
 C 체중: 7
 D 체중: 5
 B 체중: 10
 정렬 후:
 E 체중: 2
 D 체중: 5
 C 체중: 7
 A 체중: 8
 B 체중: 10
 */
```
  </p>
</details>
    
    

## 8. 템플릿 메소드 패턴과 전략 패턴 
    
- 템플릿 메소드 패턴(Template Method Pattern)
    - 알고리즘의 개요를 정의하는 일을 한다. 진짜 작업 중 일부는 밑에 있는 서브클래스에서 처리한다. 알고리즘의 각 단계에서 다른 구현을 사용하면서도 알고리즘의 구조 자체는 유지할 수 있다.  
    - 알고리즘을 구현할 때 상속을 사용한다.
    - 알고리즘을 꽉 잡고 있고, 코드 중복도 거의 없다. 알고리즘이 전부 똑같고 코드 한 줄만 다르다면, 템플릿 메소드 패턴이 전략 패턴보다 효율적이다. 중복되는 코드는 슈퍼클래스에 들어있으니 서브 클래스도 공유할 수 있다. 
    - 상대적으로 의존성이 크다. 알고리즘의 일부를 슈퍼클래스에서 구현한 메소드에 의존해야 한다.
- 전략 패턴(Strategy Pattern)
    - 일련의 알고리즘군을 정의하고 그걸 바꿔가면서 쓸 수 있게 해준다. 알고리즘은 캡슐화 되어 있어 손쉽게 서로 다른 알고리즘을 사용할 수 있다. 
    - 알고리즘을 구현할 때 클라이언트에게 구성으로 구현 할지 말지 선택할 기회를 준다.
    - 객체 구성을 사용해서 더 유연하다. 클라이언트에서 다른 전략 객체를 사용해서 알고리즘 변경도 가능하다. 
    - 상대적으로 의존성이 적다. 어떤 것에도 의존하지 않는다. 알고리즘이 캡슐화 되어있기 때문. 
    
![](https://velog.velcdn.com/images/dev_kickbell/post/e3129f0a-dcbd-4bce-aa53-a3530e769179/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/3b2a737b-9dd3-418a-99c1-e76c76193407/image.png)



## Reference 
[https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223](https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223)


 
