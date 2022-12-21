# Building Better Apps with Value Types in Swift

> _이 글은 [WWDC 2015 - Building Better Apps with Value Types in Swift](https://developer.apple.com/videos/wwdc2015)의 내용을 공부하고, 개인적으로 정리해놓은 글입니다._


## 1. 참조 의미론(Reference semantics)

### 온도 클래스(A Temperature Class)  
- swift에서는 class로 참조 의미론을 얻습니다. 아래의 코드를 예로 들면, 온도조절기(thermostat)이죠.
- 집(house)의 온도조절기를 화씨 75도(섭씨 24도)로 설정하고, 저녁을 위해 오븐(oven)을 가열하면 집이 갑자기 더워집니다. 의도치 않게 같은 temp를 공유했고, 온도 조절기와 오븐이 모두 화씨 425도(섭씨 218도)로 설정되어 있기 때문입니다. 
- 의도지 않은 공유를 방지하기 위해 copy()를 사용합니다. 그리고 기술적으로는 마지막 사본이 필요하지 않지만 다음 번에 동일한 문제가 발생하지 않도록 안전하게 가기 위해 방어적 복사(Defensive Copying)를 하겠습니다. 

```swift
class Temperature {
  var celsius: Double = 0
  var fahrenheit: Double {
    get { return celsius * 9 / 5 + 32 }
    set { celsius = (newValue - 32) * 5 / 9 }
  }
}

let home = House()
let temp = Temperature()
temp.fahrenheit = 75
home.thermostat.temperature = temp

temp.fahrenheit = 425
home.oven.temperature = temp
home.oven.bake()
```
![](https://velog.velcdn.com/images/dev_kickbell/post/87f3c012-be3e-4162-baf6-c5af11ef1559/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/b0077b75-2cc2-467c-a9a4-e3378de1dda0/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/2ae4bcfe-1fd7-4e89-a062-f5d7078c5a74/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/738779eb-d389-48dd-9f7c-9f5d509a75a0/image.png)


### Cocoa와 Objective-C에서의 방어적 복사
![](https://velog.velcdn.com/images/dev_kickbell/post/e78e32d4-6c30-436c-ac08-c24d76fc807a/image.png)

- Cocoa에서, 복사본을 제공하기 위한 NSCopying 프로토콜이 있습니다. 그리고 NSString, NSArray, NSURLRequest과 같은 것들은 복사하여 안전하므로 NSCopying에 적합합니다.
- 복사가 필요한 시스템에 있고 그래서 타당한 이유로 많은 방어 복사를 봅니다. 예를 들어,  NSDictionary는 dictionary에 있는 key를 방어 복사(defensively copy)합니다. NSDictionary key를 얻어 삽입하고 나서 hash 값이 바뀌는 방법으로 변경된다면, 모든 NSDictionary의 내부 변수는 깨지고 버그가 발생할 수 있기 때문이죠. 
- 위의 시스템에서는 정답이지만, 운 나쁘게도 성능 하락이 있습니다.


## 2. 불변성(Immutability)
### Mutation 제거
- 함수형 프로그래밍 언어에는 불변성을 가진 참조 의미 체계가 있습니다. 이렇게 하면 의도하지 않은 부작용과 같은 Mutation가 있는 참조 의미 체계로 인해 발생하는 많은 문제가 제거됩니다. 그러나 변경할 수 없는 참조 의미 체계에는 다음과 같은 몇 가지 문제가 있습니다.
- 우리는 변하기 쉬운 세상에서 살고 생각하기 때문에 어색한 인터페이스로 이어질 수 있습니다.
- 기계 모델에 효율적으로 매핑되지 않습니다.

### 불변 온도 클래스 
- celsius을 let으로 변경했습니다. 하지만 어색해요. 가변성이 있던 때는 온도를 올리고 싶다면 += 10.0만 해주면 됐지만, 가변성이 없으면 온도를 가져와서 새 온도 개체를 만들어야 합니다. 
- 이것은 힙에 다른 개체를 할당해야 하기 때문에 어색하고 성능이 떨어집니다. 

```swift
class Temperature {
  // Celsius is now always the point of reference, and it's immutable.
  let celsius: Double = 0

  var fahrenheit: Double {
    return celsius * 9 / 5 + 32
  }

  // Convenience initializers.
  init(celsius: Double) { self.celsius = celsius }
  init(fahrenheit: Double) { self.celsius = (fahrenheit - 32) * 5 / 9 }
}

// With mutability
home.oven.temperature.fahrenheit += 10.0


// Without mutability
let temp = home.oven.temperature
home.oven.temperature = Temperature(fahrenheit: temp.fahrenheit + 10.0)
```

### 에라토스테네스의 체
- 조금 더 수학적으로 접근해볼게요. 소수를 계산하기 위한 고대 알고리즘인 에라토스테네스의 체입니다. 변경을 기반으로 한 알고리즘과 불변을 기반으로 한 알고리즘 2개를 보겠습니다. 
- 변경기반 알고리즘과 불변 알고리즘은 똑같은 결과를 내지만, 불행하기도 불변 알고리즘은 성능면에서 더 비효율적입니다. 재귀를 사용하기 때문이겠죠. 

```swift
//변경 가능한 알고리즘 
func primes(n: Int) -> [Int] {
  var numbers = [Int](2..<n)

  for i in 0..<n - 2 {
    guard let prime = numbers[i] where prime > 0 else { continue }
    for multiple in stride(from: 2 * prime - 2, to: n - 2, by: prime) {
      numbers[multiple] = 0
    }
  }

  return numbers.filter { $0 > 0 }
}

//불변 알고리즘 
func sieve(numbers: [Int]) -> [Int] {
  guard !numbers.isEmpty else { return [] }
  let p = numbers[0]
  return [p] + sieve(numbers[1..<numbers.count]).filter { $0 % p > 0 }
}
```

![](https://velog.velcdn.com/images/dev_kickbell/post/6ed4f912-9820-4f95-a309-396ad7ac1306/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/fea36ff4-b0ce-401f-a90b-7177b43ddcd4/image.png)


### 코코아 터치의 불변성 
- 코코아 터치에서 불변 클래스가 여러개 있습니다. NSDate, UIImage, NSNumber, NSURL 같은 것들이죠. 불변 타입을 갖는 것은 안정성을 향상시킵니다. 복사에 대해 걱정하지 않아도 되기 때문에 의도치 않은 공유를 생각할 필요가 없지요. 
- 하지만, 불변 타입의 단점도 있습니다. home 디렉토리와 일부 디렉토리에 도달하는 연속적인 경로 요소를 추가로 시작하는 NSURL을 만들려고 합니다. 그런데, 매번 loop를 통해, 다음 경로 요소를 추가한 새로운 URL을 만들었습니다. 이는 훌륭한 알고리즘이 아닙니다.
- 코드를 개선할 수 있습니다. 배열에 추가하고 NSURL끝에 단일 새 인스턴스를 만들 수 있습니다. 그러나 진정으로 불변이 되려면 루프의 각 반복마다 새 배열을 만들어야 합니다. 
- 모든 요소를 NSMutableArray에 모읍니다. 그러고 나서 불변 NSURL로 돌아가기 위해 fileURLWithPathComponents을 사용합니다.
- 불변성은 좋습니다. 참조 의미론 세계를 더 쉽게 추론할 수 있지만 여전히 완전히 불변으로 갈 수는 없습니다.

```swift
//기존 코드 
NSURL *url = [[NSURL alloc] initWithString: NSHomeDirectory()];
NSString *component;
while ((component = getNextSubdir())) {
  // Create new object on heap, copy over all string data, discard old object...
  url = [url URLByAppendingPathComponent: component];
}

//코드 개선하기 1
NSArray<NSString *> *array = [NSArray arrayWithObject: NSHomeDirectory()];
NSString *component;
while ((component = getNextSubdir())) {
  // Create new array, copy over elements, discard old array...
  array = [array arrayByAddingObject: component];
}
url = [NSURL fileURLWithPathComponents: array];

//코드 개선하기 2
NSMutableArray<NSString *> *array = [NSMutableArray array];
[array addObject: NSHomeDirectory()];
NSString *component;
while ((component = getNextSubdir())) {
  [array addObject: component];  // Mutate array object.
}
url = [NSURL fileURLWithPathComponents: array];
```

## 3. 값 의미론(Value semantics)
- 다르게 접근해보겠습니다. 올바르게 수행하면 사용하기 쉽기 때문에 변경을 좋아하지만 문제는 의도치 않은 공유이죠. 값 타입은 이 문제를 해결합니다. 일부 값 타입의 변수 하나를 변경해도 다른 변수에는 영향을 주지 않습니다.

```swift
var a: Int = 17
var b = a
assert(a == b)
b += 25
print("a = \(a), b = \(b)")  // Prints "a = 17, b = 42"
```

### 값 타입의 구성 
- Swift의 모든 기본 타입은 값 타입입니다. 
- Swift의 모든 컬렉션은 값 타입입니다. 
- Swift에서는 값 타입만 포함하는 모든 튜플, 구조체 또는 열거형도 값 타입입니다. 값 타입의 세계에서 완전히 풍부한 추상화를 구축하는 것은 매우 쉽습니다.

### 값 유형은 값으로 구분됩니다.
- a와 b가 같은지는 값 자체로 구분됩니다. 
- CGPoint 같은 녀석의 같음을 따질때는 Equatable 프로토콜을 준수해야 합니다. 

```swift
var a: Int = 5
var b: Int = 2 + 3
assert(a == b)  // True

var a: [Int] = [1, 2, 3]
var b: [Int] = [3, 2, 1].sort(<)
assert(a == b)  // True

protocol Equatable {
  /// Reflexive: `x == x` is `true`
  /// Symmetric: If `x == y` then `y == x`
  /// Transitive: If `x == y` and `y == z`, then `x == z`
  func ==(lhs: Self, rhs: Self) -> Bool
}

extension CGPoint: Equatable {}

func ==(lhs: CGPoint, rhs: CGPoint) -> Bool {
  return lhs.x == rhs.x && lhs.y == rhs.y
}
```

### 온도의 값 의미론 
- 온도를 struct로 바꿔줍니다. 그리고 해줘야 할 일은 let temp를 var로 다시 바꿔주는 것 뿐입니다. 
- 더 이상 의도치 않은 공유도 없고 값도 컴파일러가 인라이닝 할 수 있으므로 더 나은 메모리 사용과 성능을 얻을 수 있습니다. 

```swift
struct Temperature: Equatable {
  var celsius: Double = 0
  var fahrenheit: Double {
    get { return celsius * 9 / 5 + 32 }
    set { celsius = (newValue - 32) * 5 / 9 }
  }
}

func ==(lhs: Temperature, rhs: Temperature) -> Bool {
  return lhs.celsius == rhs.celsius
}
```
![](https://velog.velcdn.com/images/dev_kickbell/post/265cd6cb-d2c2-4337-b44d-240ffc2777f2/image.png)

### Race Condition으로부터의 자유 
- 아래의 첫번째 그림에서 참조 타입이었으면 레이스 컨디션을 유발할 수 있지만, 값 타입이라면 복사하기 때문에 레이스 컨디션에서 자유를 얻을 수 있습니다. 
- 또, 값 타입은 비용이 저렴합니다. Int, Double같은 애들은 상수 시간이고 CGPoint 같은 애들도 상수시간 입니다. 그리고 Array, Dictionary, String, Set 같은 확장 가능한 구조들은 copy-on-write를 사용합니다. 실제로 변경지점에서만 복사를 한다는 거죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/96ec034c-ccf2-416f-8659-8b33df9d5ab2/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/63fa1922-13fd-4d90-80bd-498d29cd18dc/image.png)


## 4. 값 타입 연습(Value types in practice)
- 원과 다각형을 만들겁니다. 그리고 다이어그램도 만들건데, protocol을 사용합니다. 
- 다이어그램에 아이템을 추가하면 items는 2개의 Drawable을 가집니다. 
- 그리고 doc2를 만들어서 그것을 복사하면 값 타입이기 때문에 복사되어 새로운 인스턴스가 생깁니다. doc2를 변경해도 doc에는 아무런 영향을 미치지 않는 것이죠. 
- 다이어그램을 Drawable을 준수하게 합니다. 그리고 doc.addItem(doc)를 해줍니다. 어떻게 될까요? 흥미롭게도 아무일도 없습니다. 
- 만약에 참조 의미론이었다면 무한 재귀를 발생시켰을 거에요. 하지만 값 타입이기 때문에 인스턴스가 완전히 나뉘고 구분됩니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/8325fb76-ffcb-492a-885b-2fb3f626e9a5/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/0c426cce-bfa8-4db3-98dd-7d0a0f9b9a4f/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/3d5fb1dc-9c92-42bb-83d5-180423e38326/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/352fc62b-1953-415d-89b4-a6b8d91e90ea/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/4daa2bd9-e026-43c3-8bb6-ff58cc250974/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/c58df9b8-ac51-4924-a6e7-959ac276fa9b/image.png)


```swift
struct Circle: Equatable {
  var center: CGPoint
  var radius: Double
}

func ==(lhs: Circle, rhs: Circle) {
  return lhs.center == rhs.center && lhs.radius == rhs.radius
}

struct Polygon: Equatable {
  var corners = [CGPoint]()
}

func ==(lhs: Polygon, rhs: Polygon) {
  return lhs.corners == rhs.corners
}

protocol Drawable {
  func draw()
}

extension Polygon: Drawable {
  func draw() {
    let ctx = UIGraphicsGetCurrentContext()
    CGContextMoveToPoint(ctx, corners.last!.x, corners.last!.y)
    for point in corners {
      CGContextAddLineToPoint(ctx, point.x, point.y)
    }
    CGContextClosePath(ctx)
    CGContextStrokePath(ctx)
  }
}

extension Circle: Drawable {
  func draw() {
    let arc = CGPathCreateMutable()
    CGPathAddArc(arc, nil, center.x, center.y, radius, 0, 2 * π, true)
    CGContextAddPath(ctx, arc)
    CGContextStrokePath(ctx)
  }
}

struct Diagram: Drawable {
  var items = [Drawable]()

  mutating func addItem(item: Drawable) {
    items.append(item)
  }

  func draw() {
    for item in items {
      item.draw()
    }
  }
}
```

## 5. 값 타입과 참조 타입 섞기(Mixing value types and reference types)
- 참조 타입에 값 타입이 포함될 수도 있겠고, 값 타입이 참조 타입을 포함할 수도 있겠죠. 
- 값 의미론을 유지하려면 아래 그림의 질문을 고려해야 합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/49bc097a-94d1-4fd1-80a0-8c56f199ce99/image.png)

### 불변 객체의 참조 
- 불변 클래스인 UIImage의 예제입니다. image 인스턴스를 만들고 나서, image를 image2에 할당한다면 image와 image2는 둘 다 같은 객체를 가리킵니다.
- 위의 Temperature 될까봐 걱정했을 수 있지만, UIImage는 불변이라 괜찮습니다. image가 변경된 것에 대해 image2는 걱정하지 않아도 됩니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/35d8c3c5-2ce3-4fbe-a341-3f9b90441a5c/image.png)

### 불변 객체의 참조와 Equatable 
- 같음을 구별하려면 어떻게 해야 할까요? 그냥 === 연산자를 활용하면 되나요 ? 언뜻 괜찮을 수 있습니다. copy한 경우엔 그럴 수 있겠죠. 근데 이런 경우가 있을 수 있어요. 
- 같은 bitmap을 이용해서 2개의 인스턴스를 생성한다면 어떨까요? 이것도 같아야 하잖아요? 근데 참조값은 다르겠죠. 인스턴스가 다르니까요. 
- 그래서 이럴때는 NSObject로부터 상속받은 UIImage에서 비교하는 isEqual()을 사용해야 합니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/64cba427-0f8a-44ee-b300-2166c1aeaaa0/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/3e70a44c-0574-4ad5-9dc1-84259578ef3d/image.png)

### 가변 객체의 참조 
- struct BezierPath를 구현합니다. 그러나 내부에는 가변 객체인 UIBezierPath()가 있습니다. 
- isEmpty는 괜찮습니다. 딱히 변경하지 않기 때문이죠. 그런데 addLineToPoint는 문제가 발생합니다. path를 변경하기 때문이지요. 그리고 또 addLineToPoint에 mutating 키워드가 없습니다. String 같은 값 타입이면 컴파일러가 알려주었을텐데, UIBezierPath이 참조타입이기 때문에 알려주지 않습니다. 
- BezierPath 인스턴스가 두 개 있다면, 참조를 통해 같은 UIBezierPath 인스턴스를 둘 다 가리키고, 인스턴스 하나가 변경된다면 다른 하나는 의도치 않게 공유가 됩니다. 값 의미론이 유지되지 않는거죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/26136f4e-c10e-44ab-a55a-d541683c980c/image.png)

### Copy-on-Write
- _path를 private으로 만듭니다. 그리고 pathForReading과 pathForWriting을 각각 만들어줍니다. 
- 그리고 읽을 때는 그냥 return을 시키고, 수정할때는 copy()해서 _path를 변경하고 return 합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/3a26464b-4daf-4ba1-a48b-7da39aa16bde/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/d6a6cd0b-3bc9-4355-91c3-46c639b30f97/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/ff029ee2-4c8f-41df-bb7c-9aa3f2026bd0/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/72b21fdf-eea4-4983-8650-f28e83868fbf/image.png)


```swift
struct BezierPath: Drawable {
  private var _path = UIBezierPath()

  var pathForReading: UIBezierPath {
    return _path
  }

  var pathForWriting: UIBezierPath {
    mutating get {
      _path = _path.copy() as! UIBezierPath
      return _path
    }
  }

  var isEmpty: Bool {
    return pathForReading.empty
  }

  mutating func addLineToPoint(point: CGPoint) {
    pathForWriting.addLineToPoint(point)
  }
}

var path = BezierPath()
var path2 = path
if path.empty { print("Path is empty") }
path.addLintToPoint(CGPoint(x: 10, y: 20))
```

### 다각형에서의 경로 형성 
- 다각형에 path라는 변수를 넣어 확장하려고 합니다.  
- 개선되지 않은 코드는 result가 struct로 BezierPath 입니다. 그래서 루프의 모든 반복에서 BezierPath 를 복사합니다. 
- 개선된 코드 1은 UIBezierPath()로 참조타입을 하나 만들고 변경할 것들 다 하고나서 하나의 구조체인 BezierPath(path: result)를 생성해서 반환합니다. 
- 개선된 코드 2을 보시죠. Swift에는 유일하게 참조되었는지 아는 훌륭한 기능(isUniquelyReferencedNonObjc)이 있습니다. 그래서 우리는 참조 타입이 유일하게 참조되었다는 것을 안다면 사본 만드는 것을 피할 수 있습니다. 표준 라이브러리는 이 특징을 곳곳에 사용하고 이를 이용하여 뛰어난 성능 최적화를 많이 합니다.

```swift
//개선하지 않은 코드 
extension Polygon {
  var path: BezierPath {
    var result = BezierPath()
    result.moveToPoint(corners.last!)
    for point in corners {
      // Copies `BezierPath` in every iteration of the loop!
      result.addLineToPoint(point)
    }
    return result
  }
}
//개선된 코드 1
extension Polygon {
  var path: BezierPath {
    var result = UIBezierPath()  // Reference type
    result.moveToPoint(corners.last!)
    for point in corners {
      result.addLineToPoint(point)  // Mutate in every iteration
    }
    return BezierPath(path: result) // Create value type at end
  }
}
//개선된 코드 2
struct MyWrapper {
  var _object: SomeSwiftObject
  var objectForWriting: SomeSwiftObject {
    mutating get {
      if !isUniquelyReferencedNonObjc(&_object) {
        _object = _object.copy()
      }
      return _object
    }
  }
}
```

### 값 타입으로 실행 취소 구현하기 
- 값 타입을 사용하면 실행 취소 기능을 쉽게 구현할 수 있습니다. Diagram을 만들고 Diagram 배열인 undoStack을 만듭니다. undoStack에 doc를 추가하고 다각형을 넣고 원도 넣습니다.
- 이제 undoStack에 3개의 다른 Diagram 인스턴스가 있습니다. 이들은 같은 것을 참조하지 않습니다. 이들은 3개의 다른 값입니다.
- 이것으로 실행 취소 기능을 구현할 수 있습니다. 앱에 이를 그리고, History 버튼이 있습니다. History 버튼을 탭하고 undoStack을 통해 이전 Diagram의 모든 상태 리스트를 얻습니다. 사용자에게 탭을 허용하고 이전으로 돌아가도록 할 수 있습니다.
- 이는 매우 강력한 기능으로, 사실 Photoshop은 모든 히스토리를 구현하기 위해 이 기능을 광범위하게 사용합니다. 
- Photoshop에서 이미지를 열 때, 뒤에선 무슨 일이 일어날까요? Photoshop은 사진이 얼마나 큰지 상관치 않고, 작은 타일 묶음으로 나눕니다. 각각의 타일은 값이고, document는 값인 타일을 포함합니다.
- 그러고 나서 만약 이 사람의 셔츠를 자주색에서 녹색처럼 바꾼다면, 셔츠가 포함된 타일인 다이어그램의 두 인스턴스에서 복사됩니다. 두 개의 다른 document가 있다고 하더라도, 오래된 상태와 새로운 상태, 이 사람의 셔츠 안에 포함된 타일인 결과로 새로운 데이터로만 소비해야 했습니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/a9bd9303-3646-4ee7-94d0-a760d93d73da/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/7b8c4500-1f01-44b3-94af-1fe7600dfd84/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/5e2daa42-7cd7-4afc-97be-e712c358d8e9/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/7b5cd2f3-11de-4a56-be25-3dab7306d648/image.png)


## Reference

[https://www.youtube.com/watch?v=A_b2oCBmm2Y&t=1815s](https://www.youtube.com/watch?v=A_b2oCBmm2Y&t=1815s)
