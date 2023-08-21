# 2015, Protocol-Oriented Programming in Swift

> _이 글은_ [_WWDC 2016 - Protocol-Oriented Programming in Swift_](https://developer.apple.com/videos/play/wwdc2015/408)_의 내용을 공부하고, 개인적으로 정리해놓은 글입니다._

## 1. Class는 좋습니다

* 클래스는 좋습니다. 캡슐화, 접근제어자, 추상화, 네임스페이스¹, 표현구문, 확장성같은 것들을 제공합니다. 그리고 구조체 또한 이와같은 것들을 똑같이 할 수 있죠.
* 클래스만 가능한 것을 따지자면 상속이 있죠. 슈퍼클래스가 복잡한 로직을 가진 메소드를 정의하면, 서브클래스는 단지 슈퍼클래스를 상속하는 것만으로 슈퍼클래스의 모든 기능을 사용할 수 있습니다. 마법이죠. 또, 서브클래스는 슈퍼클래스의 메소드를 재정의 할 수도 있습니다. 코드의 유연성을 유지하면서 복잡한 슈퍼클래스의 코드를 재사용할 수 있는 것이죠.
* 하지만, 클래스는 꽤나 큰 단점들도 가지고 있죠.

![](https://velog.velcdn.com/images/dev\_kickbell/post/9ca6f156-fa9c-4515-a502-91ce46a1becd/image.png) ![](https://velog.velcdn.com/images/dev\_kickbell/post/de138279-ec28-49dd-875a-ab3ce03728b0/image.png) ![](https://velog.velcdn.com/images/dev\_kickbell/post/1b9567bd-9f1a-41bb-993e-c19ef6152453/image.png)

## 2. Class의 3가지 문제점들

### 암시적 공유

* A와 B는 C라는 데이터를 공유하지만, A가 C를 변경한다면 B도 영향을 받을 수 밖에 없습니다. 참조 타입이니까요.
* 그래서 방어적 복사를 하고, race condition이 발생했고, 스레드의 불변 상태를 보호하기 위해 lock을 추가하고, 교착상태가 발생하고, 결론적으로 버그가 발생하게 됩니다. 당연히 이것들은 코드를 느리게 하지요.
* 하지만 이것들은 Swift에는 적용되지 않죠. Swift의 Collection은 모두 값 타입이니까요.

![](https://velog.velcdn.com/images/dev\_kickbell/post/b1206df8-170c-49b4-ab22-e917a93eb9d6/image.png) ![](https://velog.velcdn.com/images/dev\_kickbell/post/7ba58118-eea0-4a41-8b78-7551530eb37f/image.png)

### Class의 상속은 너무 참견합니다

* 클래스는 오직 하나의 슈퍼클래스를 얻습니다. 단일 상속이죠. 다중 추상 모델이 필요하면 어떻게 하나요? 불가능합니다. 클래스는 관련된 것들로 비대해질테죠.
* 또, 클래스를 정의하는 순간 슈퍼클래스를 선택해야 하죠. 슈퍼클래스가 저장 속성을 갖는다면 선택권이 없이 받아들여야만 합니다. 초기화를 해야 하구요.
* 마지막으로 작성자가 무엇을 재정의해야 하고, 무엇을 재정의하지 말아야하는지 알기가 힘듭니다. final 키워드를 사용하지 않을 수 있고 덮어쓰는 메서드를 고려하지 않을 수도 있습니다. 이것들이 Cocoa의 모든 곳에 우리가 delegate pattern을 사용하는 정확한 이유입니다.

![](https://velog.velcdn.com/images/dev\_kickbell/post/d1785993-332d-462d-92d6-c547f5b6acd3/image.png)

### 잃어버린 타입 관계

* 비교와 같은 대칭 연산을 나타내기 위해 클래스를 사용하려고 합니다. 이진 탐색을 해야하는데, 그러려면 비교가 필수죠.
* 추상화를 위해서 ²Ordered라는 클래스를 생성하고 안에 precedes라는 메소드도 넣어주었습니다. 근데 어쩌죠? precedes 메소드안에 비교를 위한 로직을 넣어야 되는데 넣을 수가 없네요? 왜? 자식 클래스가 무슨 타입이 될지 알 수가 없기 때문입니다.
* 그래서 숫자 타입인 Number 클래스를 보면 Ordered에는 value라는 Double 타입이 없기 때문에 어쩔 수 없이 다운캐스팅을 해줘야 합니다.
* 그렇다고 Ordered에 Double, String 같은 타입을 다 넣어버릴 순 없겠죠. 타입간의 관계를 잃어버린 느낌입니다.
* 결론적으로 더 나은 추상화 매커니즘이 필요합니다. 그리고 그 정답이 protocol입니다.

```swift
class Ordered {
  func precedes(other: Ordered) -> Bool {
    fatalError("implement me!")
  }
}

func binarySearch(sortedKeys: [Ordered], forKey k: Ordered) -> Int {
  var low = 0, high = sortedKeys.count

  while high > low {
    let mid = low + (high - low) / 2

    if sortedKeys[mid].precedes(k) {
      low = mid + 1
    } else {
      high = mid
    }
  }

  return low
}

class Number: Ordered {
  var value: Double = 0

  override func precedes(other: Ordered) -> Bool {
	return value < (other as! Number).value
  }
}
```

## 3. Swift는 프로토콜 지향 프로그래밍 언어

### 더 나은 추상화 매커니즘

* 값 타입과 클래스를 지원
* 정적 타입 관계 및 동적 디스패치 지원
* ³비모놀리식 ( 수직 확장보다는 수평 확장 )
* 작은 모델링 지원 ( extension에서 다른 타입의 요구 사항을 따를 수 있음 )
* 모델에 원치않는 인스턴스 데이터를 강제하지 않음
* 모델에 초기화 부담을 주지 않음
* 재정의 대상을 모호하게 남기지 않고 명확하게 함

### 프로토콜로 시작

* 프로토콜은 메서드 본문을 허용하지 않기 때문에 재정의 할 항목은 없습니다. 그리고 Number는 class일 필요가 없으므로 struct로 변경합니다.
* 이것의 이점은 프로토콜은 직접 구현되어 있지 않고 명세만 되어 있죠? 이러면 컴파일러가 static check를 할 수 있게 되어 성능에 이롭습니다. 런타임 시간에 체크하는 것이 아니라 컴파일 시점에 자료형 검사라던지 그런것들을 미리 체크할 수 있어서 성능에 좋다는 거에요.

```swift
protocol Ordered {
  func precedes(other: Ordered) -> Bool
}

struct Number: Ordered {
  var value: Double = 0

  func precedes(other: Ordered) -> Bool {
    return self.value < (other as! Number).value
  }
}
```

* 그리고 other as! Number의 타입 캐스팅을 변경합니다. protocol에는 해당 프로토콜을 준수하는 타입을 지칭하는 placeholder인 Self가 있습니다.
* 그리고 Self를 사용하면 Number는 Ordered를 준수하고 있으니 대체가 가능하겠죠? 그러면 타입캐스팅 없이 바로 value 속성을 불러올 수 있게 됩니다.

```swift
protocol Ordered {
  func precedes(other: Self) -> Bool
}

struct Number: Ordered {
  var value: Double = 0

  func precedes(other: Number) -> Bool {
    return self.value < other.value
  }
}
```

* 다음은 binarySearch를 보죠. 위에서도 말했지만, \[Ordered] 라고 지정해놓으면 Ordered를 준수하는 모든 타입이 들어올 수 있게 되죠. 근데 저기에는 종류가 다른 애들이 다 들어올 수 있어요. 숫자, text, 이런 것들이요. 다른 타입 3개가 하나의 배열이 될 수도 있죠.
* 그런데, 이것을 Swift의 제네릭을 사용해서 리팩토링 해줍니다. 이게 무슨의미냐면요. 제네릭은 정적 다형성 이라고하는 기능을 지원하는데, 요약하면 \[숫자, text, ??] 같이 다른 종류의 배열(heterogeneous array)이 올 수 있던 것들이 \[숫자, 숫자, 숫자], \[text, text, text]처럼 같은 종류의 배열(homogeneous array)만 올 수 있게 되는거에요.
* 어떻게 보면 당연한거기도 하죠. 숫자와 text를 대소비교 할 수 는 없잖아요? 애초에 우리가 원하던 것이죠. Ordered를 준수하는 타입으로 제약을 둔 것인데 T는 같은 타입이다라는 것이죠.

```swift
func binarySearch<T: Ordered>(sortedKeys: [T], forKey k: T) -> Int { ... }
```

### 결론

* 결론적으로 비교를 해보자면, 타입으로만 사용가능/제약 조건으로 사용가능, 여러종류의 배열/한 종류의 배열, Self사용으로 서로 다른 타입 모델들 간의 상호작용 가능, 직접 구현을 하지 않으므로서 컴파일 시점에 미리 static하게 check 할 수 있어서 최적화된다 정도가 되겠네요.

![](https://velog.velcdn.com/images/dev\_kickbell/post/227f4f75-e12a-460c-9e51-fe676f6158fd/image.png)

## 4. Diagram 그리기

### POP없이 Diagram 그리기

* 원과 다각형을 그리는 프로그램을 만들어보려고 합니다.
* Renderer가 있고, 다각형, Drawable 프로토콜이 있습니다.
* 그리고 완성했죠. 다만 테스트를 해보니 텍스트가 출력됩니다. 하지만 이것이 원인지 삼각형인지 직관적으로 알 방법이 없죠. 그려보질 않았으니까요.
* 하지만 텍스트로 출력되는 코드가 테스트하기엔 더 용이합니다. 값이 같은지 비교하면 되니까요.

```swift
struct Renderer {
  func move(to point: CGPoint) {
    print("Move to (\(point.x), \(point.y))")
  }

  func line(to point: CGPoint) {
    print("Line to (\(point.x), \(point.y))")
  }

  func arc(at center: CGPoint, radius: CGFloat, startAngle: CGFloat, endAngle: CGFloat) {
    print("Arc at \(center), radius: \(radius), startAngle: \(startAngle), endAngle: \(endAngle)")
  }
}

protocol Drawable {
  func draw(using renderer: Renderer)
}

struct Polygon: Drawable {
  var corners = [CGPoint]()

  func draw(using renderer: Renderer) {
    renderer.move(to: corners.last!)

    for point in corners {
      renderer.line(to: point)
    }
  }
}

struct Circle: Drawable {
  var center: CGPoint
  var radius: CGFloat

  func draw(using renderer: Renderer) {
    renderer.arc(at: center, radius: radius, startAngle: 0.0, endAngle: twoPi)
  }
}

struct Diagram: Drawable {
  var elements = [Drawable]()

  func draw(using renderer: Renderer) {
    for element in elements {
      element.draw(using: renderer)
    }
  }
}

//Test It! 
var circle = Circle(center: CGPoint(x: 187.5, y: 333.5), radius: 93.75)

var triangle = Polygon(corners: [
  CGPoint(x: 187.5, y: 427.25),
  CGPoint(x: 268.69, y: 286.625),
  CGPoint(x: 106.31, y: 286.625)
])

var diagram = Diagram(elements: [circle, triangle])
diagram.draw(using: Renderer())

/*
$ ./test
Arc at (187.5, 333.5), radius: 93.75, startAngle: 0.0, endAngle: 6.28318530717959
Move to (106.310118395209, 286.625)
Line to (187.5, 427.25)
Line to (268.689881604791, 286.625)
Line to (106.310118395209, 286.625)
*/
```

### 테스트에 좋은 POP 적용하기

* 테스트 하기엔 좋지만, 저게 원인지 삼각형인지를 판별할 수가 없습니다. 문제를 고쳐볼게요.
* Renderer를 프로토콜로 선언합니다. 그리고 TestRenderer를 생성하죠. 또 Renderer가 프로토콜로 선언되어있기 때문에 CGContext를 확장으로 Renderer를 준수하게 해서 실제로 그리는 기능을 그려줍니다. 이러면 원인지 삼각형인지 판별할 수 있겠죠?
* POP를 적용하면 소급 모델링(작게작게 바꿀수 있는)도 가능하고 테스트에도 용이합니다.

```swift
protocol Renderer {
  func move(to point: CGPoint)
  func line(to point: CGPoint)
  func arc(at center: CGPoint, radius: CGFloat, startAngle: CGFloat, endAngle: CGFloat)
}

struct TestRenderer: Renderer {
  func move(to point: CGPoint) {
    print("Move to (\(point.x), \(point.y))")
  }

  func line(to point: CGPoint) {
    print("Line to (\(point.x), \(point.y))")
  }

  func arc(at center: CGPoint, radius: CGFloat, startAngle: CGFloat, endAngle: CGFloat) {
    print("Arc at \(center), radius: \(radius), startAngle: \(startAngle), endAngle: \(endAngle)")
  }
}

extension CGContext: Renderer {
  func move(to point: CGPoint) {
    CGContextMoveToPoint(self, position.x, position.y)
  }

  func line(to point: CGPoint) {
    CGContextAddLineToPoint(self, position.x, position.y)
  }

  func arc(at center: CGPoint, radius: CGFloat, startAngle: CGFloat, endAngle: CGFloat) {
    let arc = CGPathCreateMutable()
    CGPathAddArc(arc, nil, center.x, center.y, radius, startAngle, endAngle, true)
    CGContextAddPath(self, arc)
  }
}
```

### 반복제거를 위한 프로토콜 확장하기

* 버블을 그리려고 합니다. 아래의 그림처럼 원안에 원이 있는 그런 느낌이죠.
* 하지만 .arc 코드가 여러번 호출이 되네요. 그래서 프로토콜에 circle이라는 요구사항을 추가했습니다.
* 그런데, 중복 구현이 그래도 남아있습니다. 어떻게 해야 할까요? 이럴 때 코드를 복제하는 대신에 프로토콜 확장을 사용할 수 있습니다.
* 프로토콜에는 extension을 사용해서 기본 구현을 제공할 수 있습니다.

![](https://velog.velcdn.com/images/dev\_kickbell/post/b562dc17-1cb7-46c7-90fb-1adfce2b6710/image.png)

```swift
//버블 그리기, 코드 중복 
struct Bubble: Drawable {
  // ...
  func draw(using renderer: Renderer) {
    renderer.arc(at: center, radius: radius, startAngle: 0, endAngle: twoPi)
    renderer.arc(at: highlightCenter, radius: highlightRadius, startAngle: 0, endAngle: twoPi)
  }
}

struct Circle: Drawable {
  // ...
  func draw(using renderer: Renderer) {
    renderer.arc(at: center, radius: radius, startAngle: 0.0, endAngle: twoPi)
  }
}

//새로운 요구사항 추가하기, circle 
protocol Renderer {
  // move(to:), line(to:), arc(at:radius:startAngle:endAngle)
  func circle(at center: CGPoint, radius: CGFloat)
}

extension TestRenderer {
  func circle(at center: CGPoint, radius: CGFloat) {
    arc(at: center, radius: radius, startAngle: 0, endAngle: twoPi)
  }
}

extension CGContext {
  func circle(at center: CGPoint, radius: CGFloat) {
    arc(at: center, radius: radius, startAngle: 0, endAngle: twoPi)
  }
}


//프로토콜 확장을 통해 기본 구현 사용하기 
extension Renderer {
  func circle(at center: CGPoint, radius: CGFloat) {
    arc(at: center, radius: radius, startAngle: 0, endAngle: twoPi)
  }
}
```

### 프로토콜의 Customization Point

* 프로토콜에서의 사용자 지정 지점 생성이 무슨말인지 저도 조금 헷갈렸었는데요. 그냥 이런 느낌입니다. 아래의 코드를 예시로 볼게요.
* 요구사항이 없는 Car가 있죠. Car에는 기본구현으로 a(), b()가 있습니다. 그리고 아무것도 없는 TestCar라는 구조체가 있구요.
* 만약에 TestCar: Car를 하면 a(), b()를 사용할 수 있을까요? 네. 있죠. 어차피 기본 구현이 되어있으니까요. a와 b를 출력할 겁니다.
* 그러면, TestCar를 확장해서 Car의 기본구현을 재정의하면 어떻게 될까요? 결과는 아래 코드와 같습니다.
* 타입 어노테이션으로 아무것도 지정하지 않으면 TestCar로 타입추론이 되서 11, 22가 출력되고 반대로 타입 어노테이션으로 Car라고 지정을 하면 a, b가 출력이 되는거죠.
* 즉, 정리하면 extension TestCar: Car를 하면 TestCar는 a(), b()를 재정의 할 수 있는 권한을 얻게 되는거죠. 이런 부분들을 커스터마이징 포인트를 생성한다 라고 보는 것 같아요.
* 하지만, 무분별하게 프로토콜의 기본 구현을 남발하는 것은 또 좋지 못합니다. 요구사항으로 둬야할 것이 있고 기본구현이 나을 때가 있는 거겠죠. 그것을 잘 구분해서 사용해야 합니다.

```swift
protocol Car {}
extension Car {
    func a() { print("a") }
    func b() { print("b") }
}

struct TestCar {}
extension TestCar: Car {
    func a() { print("11") }
    func b() { print("22") }
}

let testCar = TestCar()
testCar.a() //11
testCar.b() //22

let testCar: Car = TestCar()
testCar.a() //a
testCar.b() //b
```

### 더 많은 프로토콜의 기능들

* 제한된 확장
  * 컬렉션 타입이라고 전부다 == 연산자를 사용해서 비교할 수 있는 것은 아닙니다. 그래서 where절을 이용해서 컬렉션 요소의 타입이 Equatable을 준수하는 녀석들만 확장할 수 있도록 제한할 수 있습니다.
  * ![](https://velog.velcdn.com/images/dev\_kickbell/post/e4995913-229c-470a-8f4e-10cb2b5836c0/image.png)
* 소급 적용
  * 프로토콜에서 Self로 바꿔주고, 아까 작성한 binarySearch를 사용하려고 하면 에러가 발생합니다. 아직 기본 구현을 안해줘서 그렇겠죠. 저희 Self로 바꿔주기만 했지 아무것도 안했습니다 아직.
  * 그런데, extension Int, String: Ordered { ... } 를 해서 각각 작성해주어도 되지만, 그렇게 하지 않아도 됩니다.
  * Int, String, Double 등이 모두 Comparable을 따르기 때문에 Comparable 만 따로 작업해주고, 다른 것들은 extension Int : Ordered {} 와 같은 식으로만 처리한다면 사용할 수 있습니다.
  * 그런데 또 문제는 Comparable에 바로 extension을 해버리면 extension Double : Ordered {}을 없애더라도 Double이 Comparable를 준수하기 때문에, 자꾸 세번째 사진처럼 Double에서 precedes() 메소드를 사용할 수 있게 된다. 그래서 세번째 사진처럼 Comparable에 바로 확장하지 말고 Ordered에 where절로 제한을 둬서 extension Double : Ordered {} 이런식으로 해준 경우에만 해당 precedes() 메소드를 사용할 수 있게 해야 한다.
  * ![](https://velog.velcdn.com/images/dev\_kickbell/post/1ad0b332-a4d8-47b5-97fc-621b672052ab/image.png)
  * ![](https://velog.velcdn.com/images/dev\_kickbell/post/29bf26b9-bf73-49d0-bd50-2417487257d5/image.png)
  * ![](https://velog.velcdn.com/images/dev\_kickbell/post/f16a57f6-7081-4a3e-9706-42de1733242e/image.png)
* 일반적인 제네릭
  * swift 1에는 첫번쨰 사진처럼 온갖 꺾쇠와 괄호가 많이 있었지만, swift 2에서 프로토콜 확장을 통해 개선된 점이 많다고 합니다.
  * ![](https://velog.velcdn.com/images/dev\_kickbell/post/65e0409b-f17f-4568-9a06-329727af3b68/image.png)
  * ![](https://velog.velcdn.com/images/dev\_kickbell/post/1c4261e1-c3cc-4ee1-8a93-e7eb1b509a7f/image.png)
* 인터페이스 생성
  * Swift에서 미리 만들어놓은 프로토콜이 아주 많습니다. 예를 들어 OptionSetType 같은 것들이죠. 이런 녀석들을 준수하기만 하면 얻을 수 있는 광범위한 인터페이스가 많습니다.
  *

      ![](https://velog.velcdn.com/images/dev\_kickbell/post/755c8717-b785-4869-9c0d-eb0bdc3a615b/image.png)

## 5. 정리하기

* 추상에서는 프로토콜은 슈퍼클래스보다 더 위대합니다.
* 프로토콜 확장, 이 새로운 기능은 여러분에게 거의 마법 같은 것들을 할 수 있도록 합니다.

## Reference

[https://developer.apple.com/videos/play/wwdc2015/408](https://developer.apple.com/videos/play/wwdc2015/408)

## Endnotes

### ¹Namespace

* 말그대로 이름이 존재하는 공간이라고 볼 수 있다. 네임스페이스 기능을 제공하는 대표적인 언어는 C++, Java가 있는데 Java는 package라는 개념을 사용한다.
* 이런 느낌이라고 볼 수 있다. 이름이 add()인 함수 2개를 구분한다고 할 때, 다른 class에 각각 add()라는 함수가 당연히 있을 수 있다. 그것을 그냥 namespace라는 키워드로 선언하고 불러와서 사용하는 것이다.

```c
#include <iostream>
 
namespace A {
    void add() {
        std::cout << "call add() from A" << std::endl;
    }
}
 
namespace B {
    void add() {
        std::cout << "call add() from B" << std::endl;
    }
}
 
int main()
{
    A::add();
    B::add();
 
    return 0;
}

```

* Swift에서는 정식으로 namespace 기능을 제공하지는 않지만, 이미 흔하게 사용하고 봐왔을 것이다.
* init()이 없는 struct나 case 없는 enum을 통해 사용할 수 있다.

```swift
enum API {
	private init() {}
    static let BaseURL = "https://example.com/v1/"
    static let signIn = "signIn/"
    static let signOut = "signOut/"
}

struct Colors {
    private init() {}
    static let Red = UIColor(red: 1.0, green: 0.1491, blue: 0.0, alpha: 1.0)
    static let Green = UIColor(red: 0.0, green: 0.5628, blue: 0.3188, alpha: 1.0)
}

enum Colors {
    static let Red = UIColor(red: 1.0, green: 0.1491, blue: 0.0, alpha: 1.0)
    static let Green = UIColor(red: 0.0, green: 0.5628, blue: 0.3188, alpha: 1.0)
}

/*
API.signIn
API.signOut
Colors.Red
Colors.Green
*/
```

### ²class Ordered {...}

* 이 부분이 좀 헷갈릴만합니다. 저도 처음에 이해를 못했어요. 무슨 말이냐면, 일단 저 코드와 이진탐색을 이해해야 글이 이해가 될 수 있어요.
* 이진 탐색은 아래의 그림과 같은 겁니다. 37이라는 숫자를 찾는데, 정렬이 되어있다는 전제하의 배열에서 중간거를 찝고, 그거에 대해서 UP/DOWN을 해서 배열의 검색할 시작과 끝을 재조정하죠. 그리고 그걸 반복해서 찾는 것이죠.

![](https://www.mathwarehouse.com/programming/images/binary-vs-linear-search/binary-and-linear-search-animations.gif)

* 그래서 그게 class와 무슨 관계가 있냐?라고 하실 수 있어요. 그러면 밑에 코드를 보죠. binarySearch라는 메소드가 있고, 계속 반반반으로 바뀔 sortedKeys라는 \[Ordered]가 있어요. 그리고 if sortedKeys\[mid].precedes(k) 이면 배열의 조회할 범위를 계속 반반으로 줄여주는 거죠.
* 그러면 결국엔 if sortedKeys\[mid].precedes(k)의 값이 true/false 인가가 중요하죠. 그리고 precedes 안에서는 비교 작업을 해야겠죠. 큰지 작은지 그런것을 비교를 해야 배열의 순회 범위를 재조정할 수 있을 테니까요.
* 그래서 지금 추상화를 하겠다고 Ordered라는 클래스를 만든거에요. 선행하다 라는 의미의 precedes라는 메소드도 만들어놓구요. 하지만 문제는, 얘를 상속하는 자식 클래스들이 무슨 타입일지를 모르는 상황인거죠. 숫자일 수도 있고, String일 수도 있습니다. String도 비교를 할 수 있으니까요. 지금 상태로는 Ordered는 뭐든 될 수 있지요.
* 그래서 결국엔 아래의 Number처럼 자식클래스에서 다운캐스팅을 해야되는 상황이 와버리는 것이죠. (other as! Number).value 이렇게요. 왜요? Ordered에는 value가 없잖아요. 그렇다고 Ordered에 value: Double을 포함해서 온갖 것들을 다 넣을수도 없는 것이구요.

```swift
class Ordered {
  func precedes(other: Ordered) -> Bool
}

func binarySearch(sortedKeys: [Ordered], forKey k: Ordered) -> Int {
  var low = 0, high = sortedKeys.count

  while high > low {
    let mid = low + (high - low) / 2

    if sortedKeys[mid].precedes(k) {
      low = mid + 1
    } else {
      high = mid
    }
  }

  return low
}

class Number: Ordered {
  var value: Double = 0

  func precedes(other: Ordered) -> Bool {
    return self.value < (other as! Number).value
  }
}
```

### ³Monolithic, Non-monolithic

* 모놀리식이라는 말은 좀 생소한데요. 보통 하나의 서비스 또는 앱이 거대한 아키텍쳐를 가질 때, 모놀리식하다고들 합니다.
* 여기서의 이 말의 뜻은, 음 그런 느낌이죠. UILabel이 있어요. 근데 얘는 UIView를 부모 클래스로 두고 있습니다. 근데 UILabel에는 필요 없는 코드들이 UIView에 있을 수 있죠. Label하고 관련된 코드만 필요하니까요. 그리고 저는 저만의 Label이 필요해서 MyLabel을 생성했습니다. UILabel를 상속하구요. 근데 MyLabel은 또 UIView, UILabel의 사용하지 않는 코드가 있어요. 그리고 또 다른 누군가는 MyLabel을 상속받습니다.
* 약간 이런것처럼 상속을 받고, 받고, 또 받고... 하다보면 피라미드식으로 맨 위는 그런의도가 아니었는데 맨 아래칸의 이따만큼 넓어진거죠. 수직적 확장이라고 할 수가 있겠네요.
* 반면에 protocol같은 경우에는 내가 딱 필요한 기능만 protocol로 잘게잘게 설계를 하죠. 그리고 1\~10가지의 기능이 있다면 MyLabel에는 3,5번 기능만 필요하다 하면 3,5번 프로토콜만 넣어주는거죠. 딱 맞는 그 기능 조각만요. 그래서 이런 것을 수평적 확장이라고 합니다.
* 결론적으로 이런 부분 때문에 프로토콜은 비모놀리식 이라고 하는 것이구요.
