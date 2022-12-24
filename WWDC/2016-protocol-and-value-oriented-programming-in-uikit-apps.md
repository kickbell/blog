# Protocol and Value Oriented Programming in UIKit Apps


> _이 글은 [WWDC 2016 - Protocol and Value Oriented Programming in UIKit Apps](https://developer.apple.com/videos/play/wwdc2016/419)의 내용을 공부하고, 개인적으로 정리해놓은 글입니다._


## 1. 로컬 추론(Local Reasoning)
- 이 세션은 값 타입과 로컬 추론을 사용해서 앱을 개선하는 방법에 관한 것입니다. 
- ¹로컬 추론이란, 일부 코드를 볼 때 나머지 코드가 해당 함수/클래스 등과 상호 작용하는 방식에 대해 생각할 필요가 없는 것을 말합니다. 
- 음, 처음에 저도 로컬 추론에 대해서 이해가 잘 안갔는데요. 예를 들면, 아래의 코드와 같은 것들입니다. ( 미주에 예시 코드를 추가로 작성해두었습니다. )
- 즉, 어떻게 보면 코드 응집도?같은 느낌으로 볼 수 있을 것 같습니다. 최대한 그 부분만 보면 코드를 파악할 수 있게 해놓아서 다른 부분은 보지 않아도 되는 그런 것 말이죠. 
- Swift에서도 MVC 패턴을 통해 큰 개념으로 앱의 맥락에서 로컬 추론에 중점을 둡니다.
    - Model은 데이터를 저장합니다.
    - View는 우리의 데이터를 보여줍니다
    - Controller는 모델과 뷰 사이를 조정합니다.
```swift
//로컬추론 적용 전
let myView = UIView()

func setupUI() {
    myView.backgroundColor = UIColor.blue
    myView.layer.cornerRadius = myCustomView.bounds.width / 2
}

override func viewDidLoad() {
    super.viewDidLoad()
    setupUI()
}

//로컬추론 적용 후
lazy var myView: UIView = {
    let view = UIView()
    view.backgroundColor = UIColor.blue
    view.layer.cornerRadius = myCustomView.bounds.width / 2
    return view
}()
```

## 2. 자각몽(Lucid Dreams)
- 세션의 예제 앱은 자각몽(Lucid Dreams)이라는 앱으로 꾸었던 꿈을 기록할 수 있습니다. 꿈을 탭하면 편집할 수 있고, 그 외 이것저것 기능들이 있지요. 샘플 코드는 [여기](https://developer.apple.com/library/archive/LucidDreams/Introduction/Intro.html)에 있습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/1495426a-d73f-457b-964b-aac64fee1fc0/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/2085bea7-7e72-43ff-8d18-2d75d8140d53/image.png)

## 3. Model
- 클래스는 참조 타입이죠. 즉, 동일한 객체에 대한 참조를 공유하기 때문에 copy를 할 경우에 문제가 생길 수 있습니다. 
- 값 타입은 인스턴스 객체를 복사하기 때문에 의도치 않은 공유에 대한 걱정이 없습니다. 그래서 보통은 Model을 struct로 작성하곤 하지요. 
- 그런데, Model에만 값 타입을 사용하고 나머지는 전부 class를 사용해야 하나요? 값 타입도 프로토콜과 결합하면 class가 할 수 있는 것들을 다 할 수 있는데 말이죠. 그렇지 않나요? 

```swift
// Reference semantics
class Dream {
  var description: String
  var creature: Creature
  var effects: Set<Effect>
  // ...
}

var dream1 = Dream(...)
var dream2 = dream1
dream2.description = "Unicorns all over"  // Changed for dream1 AND dream2!
```

![](https://velog.velcdn.com/images/dev_kickbell/post/00521a79-5deb-434c-8111-004437727414/image.png)


## 3. View(Cell Layout) 

### 다이어그램 비교 
- 기존 코드에는 여러 셀이 있었습니다. 일단 DecoratingLayoutCell은 UITableViewCell을 상속했죠. 레이아웃을 그리는 역할을 담당하고 있었습니다.
- 그리고 DreamCell이라는 DecoratingLayoutCell의 하위 클래스를 만들었습니다. 레이아웃과는 별개로 꿈을 보여주는 것과 같은 특정 로직을 추가한 클래스였죠. 
- 이렇게 셀을 세부적으로 나누었던 이유는 이런 클래스들을 재사용하고 싶어서였습니다. 하지만 개발이 진행될수록 그게 쉽지 않았어요. 우리가 만든 셀은 테이블 뷰에서는 사용하기 좋았지만, 다른 유형의 뷰에서는 사용하기 어려웠죠.
- 예를 들어 유사한 기능을 공유하는 컬렉션뷰의 셀이라던지, 아니면 유사한 타입을 가지고 있는 디테일 뷰 같은 곳에서 말이죠. 
- 그래서 두번째 사진처럼 일반뷰와 테이블뷰셀과 SpriteKit에 있는 기본 클래스인 SKNode에도 재사용 할 수 있는 그런 구조가 필요했습니다. SKNode는 UIView도 상속하지 않죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/92edd23f-c422-4a7d-ae4f-ae9f0628c5f6/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/b3ab8bf2-eca6-4bf5-80d2-03b6db7e07ea/image.png)

### 셀 리팩토링 
- 기존 DecoratingLayoutCell 에는 레이아웃 로직이 UITableViewCell을 상속하는 클래스 내부에 갖혀 있죠. 그런데 이럴 필요가 있을까요? DecoratingLayoutCell를 제거하고 저 부분을 레이아웃을 수행할 때 구조체로 변경해 보겠습니다.
- 이렇게 작게만 변경해도 UITableViewCell을 상속해서 테이블 뷰에 관한 온갖 내용들을 알고 있고, 필요 없는 기능들을 가진 클래스가 사라지고 오직 레이아웃을 그리는 방법만 알고 있는 코드 조각을 얻었습니다. 
- 이것의 이점은 DreamCell 뿐만 아니라 테이블뷰 셀이 아닌 다른 뷰 타입인 DreamDetailView에서도 사용할 수 있다는 것이겠죠. 

```swift
//리팩토링 전 
class DecoratingLayoutCell: UITableViewCell {
  var content: UIView
  var decoration: UIView
  // Perform layout
}
```
```swift
//리팩토링 후 
struct DecoratingLayout {
  var content: UIView
  var decoration: UIView

  mutating func layout(in rect: CGRect) {
    // Perform layout
  }
}

class DreamCell: UITableViewCell {
  // ...
  override func layoutSubviews() {
    var decoratingLayout = DecoratingLayout(content: content, decoration: decoration)
    decoratingLayout.layout(in: bounds)
  }
}

class DreamDetailView: UIView {
  // ...
  override func layoutSubviews() {
    var decoratingLayout = DecoratingLayout(content: content, decoration: decoration)
    decoratingLayout.layout(in: bounds)
  }
}
```

### 테스트
- 이런 방식은 테스트에도 이점이 있습니다. 테스트에서는 테이블 뷰를 만들거나 올바른 뷰 레이아웃 콜백이 발생할 때까지 기다릴 필요가 없죠. 
- 그냥 해당 뷰의 레이아웃 값이 기대한 결과와 맞는지만 확인하면 됩니다. 아주 작고 집중되어 있지요. 

```swift
func testLayout() {
  let child1 = UIView()
  let child2 = UIView()

  var decoratingLayout = DecoratingLayout(content: content, decoration: decoration)
  decoratingLayout.layout(in: CGRect(x: 0, y: 0, width: 120, height: 40))

  XCTAssertEqual(child1.frame, CGRect(x: 0, y: 5, width: 35, height: 30))
  XCTAssertEqual(child2.frame, CGRect(x: 35, y: 5, width: 70, height: 30))
}
```

### SpriteKit도 지원하기 
- 지금 만든 DecoratingLayout의 content, decoration은 전부 UIView이죠. 그리고 지금은 SpriteKit를 지원하려고 합니다. 
- 하지만, SpriteKit의 기본이 되는 클래스인 SKNode는 UIView의 하위클래스가 아니죠. 공통적으로 사용할 수 있는 상위클래스가 없는 상황인거죠. 어떻게 하죠? 아래의 코드처럼 코드를 복제해서 따로 만들어주어야 할까요? 
- 이럴 땐 프로토콜을 사용하면 됩니다. 프로토콜을 사용하면 상속과는 달리 서로 관련 없는 유형도 묶어줄 수 있습니다. 
- 먼저 Layout이라는 프로토콜을 만듭니다. 그리고 프로퍼티를 넣을 건데 우리가 content, decoration으로 하는 유일한 일이 프레임을 설정하는 것이었죠. 그래서 프로토콜에 frame이라는 속성을 넣어줍니다. 
- 그리고 DecoratingLayout의 content, decoration을 각각 UIView, SKNode에서 Layout으로 바꿔줍니다. 
- 마지막으로 UIView와 SKNode가 Layout 프로토콜을 준수하게 합니다. 이미 두 클래스에는 frame이라는 속성이 있으므로 따로 무언가 구현해주지 않아도 됩니다. 
- 또 다른 이점은 DecoratingLayout이 더 이상 UIKit 또는 SpriteKit에 대한 종속성을 필요로 하지 않으므로 동일한 시스템을 AppKit에 쉽게 가져와서 NSView의 레이아웃을 지원할 수 있다는 것입니다.

```swift
// 공통 클래스가 없으므로 어쩔 수 없이 코드 복제 

struct ViewDecoratingLayout {
  var content: UIView
  var decoration: UIView

  mutating func layout(in rect: CGRect) {
    content.frame = ...
    decoration.frame = ...
  }
}

struct NodeDecoratingLayout {
  var content: SKNode
  var decoration: SKNode

  mutating func layout(in rect: CGRect) {
    content.frame = ...
    decoration.frame = ...
  }
}
```
```swift
// 프로토콜을 사용해서 코드 개선하기 

protocol Layout {
  var frame: CGRect { get set }
}

struct DecoratingLayout {
  var content: Layout
  var decoration: Layout

  mutating func layout(in rect: CGRect) {
    content.frame = ...
    decoration.frame = ...
  }
}

extension UIView: Layout {}
extension SKNode: Layout {}
```

### Generic Type으로 정적 다형성, 최적화하기  
- 지금도 꽤 많이 코드를 개선했지만, 더 좋아질 수 있습니다. 잘보면 문제점이 하나 있어요. 뭐냐면 Layout를 준수하는 타입이라면 content와 decoration에 뭐든 다 올 수 있다는 겁니다. 
- 이것이 무슨 문제가 되는거냐면, content -> UIView, decoration -> SkNode 타입이 될 수 있다는 거에요. Swift에서는 이것을 UIView, UIView 와 SkNode, SkNode 처럼 쌍으로 들어오도록 제약을 주는 방법이 있습니다. 바로 제네릭이죠. 
- 아래의 코드처럼 <> 에 Child라고 하고 Layout을 준수하는 타입이라고 해주면 그것이 가능합니다. 물론 Child의 이름도 변경할 수 있죠. 이런 것을 정적 다형성이라고 합니다. 
- 제네릭을 사용하면 컴파일러는 저 두 타입이 같다는 것을 알 수 있기 때문에 컴파일 시점에 최적화가 가능합니다. 자세한 내용은 [Understanding Swift Performance](https://developer.apple.com/videos/play/wwdc2016/416/)를 참고하세요. 

```swift
struct DecoratingLayout<Child: Layout> {
  var content: Child
  var decoration: Child
  mutating func layout(in rect: CGRect) { ... }
}
```


### 코드 재사용하기 
- DecoratingLayout을 훌륭하게 구현했습니다. 하지만 앱에는 DecoratingLayout 말고도 유사하게 생긴 셀들이 있습니다. 예를 들면 CascadingLayout 이죠. 
- CascadingLayout은 왼쪽에 장식 부분이 여러 개로 되어있는 점만 다릅니다. 그래서 그 부분만 제외하고 남은 코드를 재사용 하고 싶죠.   
![](https://velog.velcdn.com/images/dev_kickbell/post/f1582c0b-ea66-45f1-bf2d-e420c8f9e9e9/image.png)

### 상속(Inheritance)
- 이럴 때 사용가능한 방법 중에 하나는 상속이죠. 그러나 상속은 슈퍼클래스가 수행할 수 있는 작업과 서브클래스가 변경하거나 재정의할 수 있는 작업까지 고려해야 합니다. 
- 따라서 작업 중인 코드에 대해서만 생각할 수가 없죠. 그리고 대부분의 경우 UIView 또는 UIViewCotroller와 같은 프레임워크 클래스에서 상속하기 때문에 엄청나게 많은 코드가 있습니다. 그래서 상속은 로컬 추론을 할 수가 없죠.

### 구성(Composition)
- 다른 방법으로는 구성이 있습니다. 구성은 더 작은 조각을 함께 결합하여 더 큰 조각을 만드는 간단한 아이디어입니다. 마치 작곡할 때 처럼, 독립적으로 되어있는 멜로디인 도입부/후렴구처럼 부분들을 따로따로 이해할 수 있습니다. 서브클래스나 슈퍼클래스에 대해 걱정하지 않고 캡슐화를 시행할 수도 있습니다.
- 구성은 완전히 새로운 것은 아닙니다. 과거에 Objective-C 또는 다른 언어도 사용했지요. 
이전에 이 레이아웃을 만들 수 있었던 한 가지 방법은 뷰를 함께 구성하는 것이었습니다.
- 여기서는 왼쪽에 장식부분 따로, 오른쪽 텍스트부분 따로, 그리고 전체 셀 따로 같은 식으로 만들 수 있었겠네요. 
- 그러나, 기존에는 큰 문제가 있었습니다. 클래스 인스턴스는 비용이 매우 비쌌던거죠. 인스턴스를 만들수록 힙 할당이 발생하고 레퍼런스 카운팅 오버헤드가 증가하죠. 그리고 이벤트 액션같은 작업이 추가되면 더 복잡해지겠죠. 그래서 기존에는 사용하는 뷰의 수를 최소화하려고 매우 노력했던거죠. 낭비니까요. 

### 값 타입의 구성(Composition)
- 하지만 Swift에서는 구성을 사용하는 더 좋은 방법이 있죠. 바로 값 타입입니다. 값 타입은 매우 가볍죠. 그리고 copy할 때마다 복사되므로 클래스의 의도치 않은 공유 문제도 걱정없습니다. 
- 음, 여기 부분은 좀 헷갈렸는데 계속 보니까 이해가 되더군요. 아마도? 맞는 것 같아요. 일단 CascadingLayout를 만듭니다. children이라는 인스턴스를 갖고 [Child]를 배열로 갖고 있어요. 
- 이게 무슨 말이냐면, 얘가 DecoratingLayout하고 다른 게 왼쪽 장식 부분이 여러개잖아요? 그래서 코드를 재사용하기 위해서 DecoratingLayout를 그대로 사용하려고 하는 거에요. 상속대신에 구성으로요. 즉, DecoratingLayout도 Layout을 준수하니까 저 Child 배열에는 DecoratingLayout이 올 수 있는 것이죠. 다형성이죠. 그리고 DecoratingLayout의 내부 인스턴스인 content를 사용해서 왼쪽 장식의 여러개를 표현하는 거구요. 
- 그리고, 코드를 일부 개선해줍니다. Layout 프로토콜의 frame 값을 func layout()으로 변경했어요. 이유는 frame이라는 변수는 SKNode, UIView에만 있으므로 좀 더 범용적으로 수용하기 위해서 frame을 func layout()으로 대체했어요. 같은 메소드가 UIView, SKNode 내부에도 있으므로 여전히 두 클래스는 Layout 프로토콜을 준수하고 있습니다.  

```swift
struct CascadingLayout<Child: Layout> {
  var children: [Child]
  mutating func layout(in rect: CGRect) {
    ...
  }
}

struct DecoratingLayout {
  var content: Layout
  var decoration: Layout

  mutating func layout(in rect: CGRect) {
    content.frame = ...
    decoration.frame = ...
  }
}

protocol Layout {
  mutating func layout(in rect: CGRect)
}

struct DecoratingLayout<Child: Layout>: Layout { ... }
struct CascadingLayout<Child: Layout>: Layout { ... }
```
### Associatedtypes
- 이제 CascadingLayout을 이용해서 2. 자각몽의 Dream 3 셀과 같이 디테일 뷰 화면에서 이미지를 선택하면 Dream 3 셀에서도 선택된 이미지로 중첩된 이미지를 표현해주는 셀을 그려주려고 합니다. 그래서 콘텐츠를 반환할 수 있도록 레이아웃 프로토콜에 contents을 추가합니다. 

```swift
protocol Layout {
  mutating func layout(in rect: CGRect)

  var contents: [Layout] { get }
}
```

- 그런데 이렇게 코드를 변경하면 아까처럼 [Layout]에 같은 종류의 배열이 아닌 서로 다른 종류의 배열이 올 수있죠. 아까는 그 문제를 제네릭 타입으로 해결했구요. 그리고 프로토콜에도 제네릭과 같은 기능이 있습니다. 바로 associatedtype 입니다. 
- associatedtype 는 placeholder로 아까 제네릭도 Child를 다른 이름으로 바꿔줄 수 있다고 했죠? 얘도 마찬가지입니다. 아래 코드와 같이 선언하고 사용은 아래 이미지와 같이 합니다. 
- 프로토콜을 준수하게되면 typealias Content의 값을 지정하라고 하고, 그러면 어떤 타입을 선택하느냐에 따라 같은 종류의 배열 타입을 갖게 됩니다. 제네릭과 유사하죠. 

```swift
protocol Layout {
  mutating func layout(in rect: CGRect)

  associatedtype Content
  var contents: [Layout] { get }
}
```
![](https://velog.velcdn.com/images/dev_kickbell/post/3b0e63e0-6059-42ca-bb65-097d94fa6ad6/image.png)

- 그런데, 이렇게 해버리면 다시 View와 Node에 대한 DecoratingLayout을 2개로 만들어야 하죠? 아까처럼 제네릭을 사용하면 그렇게 하지 않아도 됩니다. 
- 아래의 코드처럼 구현하면, 2개의 종류로 따로 만들지 않고도 코드를 재사용할 수 있습니다. 

```swift
struct DecoratingLayout<Child: Layout>: Layout {
  // ...
  mutating func layout(in rect: CGRect) { ... }

  typealias Content = Child.Content
  var contents: [Content] { get }
}
```
- 정리하면, 프로토콜에 associatedtype을 사용하면 아주 강력한 무기가 될 수 있습니다. 제네릭처럼 프로토콜 배열을 리턴할 때, 같은 종류의 배열로 고정시킬 수 있죠. 또 거기에 제네릭을 더한다면 코드 중복도 피할 수 있습니다. 

### 제네릭에 where절을 추가해서 더 안전하게 하기 
- 프로토콜을 개선했으니 둘다 UIView, UIView 인 경우에는 정상동작을 합니다. 그런데, 아래의 그림처럼 UIView, CacadingLayout과 같은 경우에는 정상동작하지 않죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/af3a9205-3fb6-4b08-b4de-1544a7cc5e37/image.png)

- 우리가 원하는 것은 각 콘텐츠인 content와 decoration이 동일한 타입을 갖는 것이죠.  우리는 각 자식에 대해 하나씩 두 개의 서로 다른 제네릭 형식 매개 변수를 갖도록 구조체를 변경할 수 있습니다.
- 아래의 코드를 보면 제네릭에 제약조건이 걸려있죠. 이거 좀 저도 헷갈렸는데 크게 헷갈릴 필요는 없는 것 같아요. 
- 그냥, 우리가 원하는건 content와 decoration의 타입이 똑같게 하려는 것이고 위의 그림처럼 애초에 저렇게 되지 않도록 제약을 걸어버리는 거에요. .Content == .Content로 타입이 똑같게 말이죠. 그러면 타입이 똑같을 수 밖에 없다. 그것을 말하는 겁니다. 

```swift
struct DecoratingLayout<
  Child: Layout, Decoration: Layout where Child.Content == Decoration.Content
>: Layout {
  var content: Child
  var decoration: Decoration

  mutating func layout(in rect: CGRect) { ... }

  typealias Content = Child.Content
  var contents: [Content] { get }
}
```

### 완성된 프로토콜 
```swift
protocol Layout {
  mutating func layout(in rect: CGRect)

  associatedtype Content
  var contents: [Content] { get }
}
```

### UIView에 종속되지않는 Unit Test 
- 우리가 만든 프로토콜을 활용할 수 있는 또 다른 곳은 단위 테스트입니다.
- 첫번째 그림에서 보듯이 기존의 테스트 코드는 UIView인스턴스를 생성하죠. UIView에 의존적입니다. 하지만, 우리는 Layout 프로토콜을 생성했고, 그것을 준수하는 TestLayout 구조체를 작성할 수 있습니다. 그리고 이것은 UIView처럼 Layout을 준수하므로 UIView를 대체할 수 있죠. 
- 이는 테스트가 UIView와 완전히 분리되어 있고, 자체 레이아웃 및 테스트 구조의 논리에만 의존한다는 것을 의미합니다. 따라서 프로토콜을 사용하면 GUI를 사용하지 않고 레이아웃을 단위 테스트할 수 있게 됩니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/77898939-e3af-4d4c-bdde-ef12d380486b/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/1bb4ee0f-85f7-47ef-aa05-1c6c334e4312/image.png)

### 결론 
- 값 유형을 사용하면 로컬 추론을 개선할 수 있습니다.
- 제네릭 타입과 프로토콜의 associatedtype을 사용하면 더 나은 타입 안전성과 유연한 코드를 얻을 수 있습니다.
- 값 타입의 구성(Composition)은 복잡한 행동을 커스터마이징하는 훌륭한 도구입니다.
- 프로토콜을 사용하면 GUI를 사용하지 않고 단위테스트를 할 수 있습니다.

## 4. Controller 
여기서부터는 컨트롤러에서 값 타입 사용하는 방법에 대해서 초점을 맞추려고 합니다. 그리고 앱의 실행 취소 기능과 관련해서 이야기 할 것입니다. 

### 버그 실행 취소 
- 우리는 꿈 리스트에 대해서 실행 취소를 구현했습니다. 하지만 favoriteCreatrue(가장 좋아하는 생물) 기능을 선택하고 실행 취소를 하면 동작하지 않았습니다. 
- 왜 동작하지 않았을까요 ? 아래의 DreamListViewController 보면 2개의 실행 취소를 위한 모델 속성을 가지고 있습니다. 꿈과 가장 좋아하는 생물이죠. 디버깅을 해봤더니 문제는 간단했습니다. favoriteCreature 이라는 모델을 추가한 뒤에는 실행 취소 코드를 잊어버린거죠. 그래서 이 문제를 해결하기 위해 favoriteCreature에 대해 실행 취소를 구현하는 다른 코드 경로를 추가할 수 있었습니다.
- 여기서 문제는, 다른 모델 속성을 추가할 때마다 실행 취소를 구현하기 위해 실행 취소를 구현하는 다른 코드 경로를 추가해야 된다는 것입니다. 그것은 유지관리에 좋지 않아요. 

```swift
class DreamListViewController: UITableViewController {
  var favoriteCreature: Creature //가장 좋아하는 생물
  var dreams: [Dream]
  // ...
}
```

![](https://velog.velcdn.com/images/dev_kickbell/post/66b9b8a5-b5fb-4f6e-b1a0-7f69dd25b113/image.png)

- 그래서 우리가 생각한 솔루션은 아래의 그림과 같은 모델 속성을 모델 구조체라는 단일 값으로 구성하는 것입니다. 실행 취소 기능은 Model에만 사용할 수 있으며, Model에 개체를 계속 추가해도 정상적으로 동작합니다. 
- 이것은 좋은 점은 우리 모델이 여전히 값 의미 체계를 가지고 있다는 것입니다. 이것은 정말 중요합니다. 무슨 말이냐면, 일단 Dream과 Creature는 struct입니다. 즉 모델은 2개의 값 타입으로 구성되어 있다는 거죠. 
- 참조 타입이 없으니 힙 할당도 없고, 모델에 대한 하나의 코드 경로만 있습니다. 모델에 다른 값 타입을 추가해도 여전히 모델 이라는 하나의 코드 경로만 있죠. (참조 타입을 추가하면 이야기가 다르겠지만) 어찌됐건 그래서 좋습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/b3761291-36f0-48c0-9960-958fbcb0677a/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/257c2c2a-af86-447d-96f5-c204c39865ff/image.png)

### 모델 분리 
- 이제 모델을 분리해보면 기존의 모델 인스턴스는 Model이라는 구조체로 이동합니다. 
- 그리고 DreamListViewController에 Model이라는 인스턴스를 추가합니다. 

```swift
class DreamListViewController: UITableViewController {
  var model: Model
  // ...
}

struct Model: Equatable {
  var favoriteCreature: Creature
  var dreams: [Dream]
}
```

### 잘못된 실행 취소 구현 
- 하지만 여기에는 버그가 있는데, 먼저 실행 취소가 일반적으로 구현되는 방식을 살펴보겠습니다. 
- 왼쪽에는 뷰 컨트롤러의 현재 모델 값이 있습니다.오른쪽에는 작업과 실행 취소 스택이 있습니다. 현재 값은 노랑 유니콘이죠. 배열의 1번째 index에 있구요. 이것을 취소하려 합니다. 
- 먼저 dreams에서 방금 추가한 꿈을 제거합니다. 그리고 테이블뷰에서 deleteRows하죠. 그리고 그 다음에 현재 노랑 유니콘을 색을 핑크색으로 다시 바꿔주는 작업을 수행할 수 있습니다. 다음에 테이블뷰에서 reloadRows를 하죠. 마지막으로 그것을 dreams에 다시 추가하고 테이블뷰에서 insertRows 합니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/0eb6fab3-c0bc-4f20-b60b-a7bc8129cb6b/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/e7564cb3-4390-4156-876d-10ff8962abdb/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/706422cd-a18a-46ad-8a76-2b7a07e6e550/image.png)

- 개별 모델 속성을 변경하고 뷰를 독립적으로 업데이트하는 이 접근 방식은 잘못되기 정말 쉽습니다. 모델의 변경 사항을 뷰의 변경 사항과 정확히 일치시켜야 하기 때문입니다.
- 꼭 모델 먼저 작업해주고 뷰를 업데이트해주는 순서도 중요하고, 둘 중에 하나의 작업을 빼먹으면 크래시가 발생합니다. 아래의 에러를 만나볼 수 있죠. 
- 잘못된 업데이트: 섹션 0의 행 수가 잘못되었습니다. 업데이트(14) 후 기존 섹션에 포함된 행 수는 해당 섹션에 포함된 행 수와 같아야 합니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/6531614e-8c99-415a-a14f-75abee68275f/image.png)

- 이런 것들은 디버깅하기가 정말 어렵습니다. 각각의 실행 취소 가능한 변경 사항은 뷰 컨트롤러에서 발생하며 각 실행 취소 가능한 변경 사항은 순서에 중요합니다. 그리고 앱에 기능을 추가함에 따라 이러한 실수가 발생할 가능성은 계속 커지죠. 
- 문제는 우리 코드에는 모델과 뷰 업데이트 사이의 대응 관계를 추론할 수 있는 곳이 하나도 없다는 것입니다. 실행 취소를 처리하는 더 간단한 방법에 대해 생각해 봅시다.


### 더 간단한 실행 취소 구현

- 일단 모델부터 합니다. 어떻게 할거냐면, 기존에 모델에서 하나씩 꿈을 삭제하고 유니콘 값을 바꾸고 이런걸 안할겁니다. 
- 대신에 아래의 그림처럼 UndoManager Stack에 [Model]처럼 모델 배열을 통쨰로 갖고 있습니다. 그리고 실행 취소를 하게 되면 현재의 모델을 UndoManager Stack의 마지막 녀석으로 통쨰로 갈아끼워버리는 겁니다. 이러면 순서에 대해서 걱정할 필요 자체가 없어지죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/19568618-4656-460b-9b96-d7ed97b65a34/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/4320f1eb-9946-4d15-a5ef-0e41f12b21a0/image.png)

- 다음은 UI 입니다. 일단 DreamListViewController에서 모델이 변경될 때마다 modelDidChange 메서드를 호출합니다. didSet 같은 걸로 호출할 수 있겠죠? 그럼 oldValue와 newValue가 있을 겁니다.
- 그리고 그것들을 활용해서 oldValue와 newValue의 다른 점을 확인해서 업데이트 할 수 있을 거에요. 
- 마지막으로는 위에서 말했던 것처럼 UndoManager Stack에서 모델 값을 이전 값으로 재설정하면 됩니다. ³UndoManager라는 Swift에서 기본적으로 제공되는 기능을 사용합니다. 
- 정리하면, 이제 UI를 업데이트 하기 위한 Model이라는 단일 코드 경로가 있습니다. 또 작업이 순서에 영향을 받지 않습니다. 모델을 통쨰로 갈아끼우고, 모델의 상태변경에 따라서 뷰의 업데이트를 하니까 말이죠. 
- 또, 코드가 한 곳에 모여있기 때문에 로컬 추론에 도움이 됩니다. 


### 자각몽의 공유기능 살펴보기
- 자각몽 앱에서는 우리 꿈을 친구와 공유할 수 있는 기능이 있습니다. 그것은 아래 코드의 3가지 속성을 나타내며, 실행 동작은 아래 이미지와 같습니다. 
- 그리고 왼쪽 상단의 취소 버튼을 탭하면 중간에 공유를 중지할 수 있습니다. 공유가 중지되면 
공유 버튼이 다시 표시되므로 내비게이션 바가 올바르게 보이는 것을 볼 수 있습니다. 하지만 버그가 있습니다. 4번째 사진을 보면 테이블뷰의 왼쪽에 있는 UI는 여전히 표시되고 있다는 것이죠. 
- 디버깅을 해보니 상태 변경 중에 일부 상태 속성이 완전히 지워지지 않은 것을 확인했습니다.
따라서 이 경우 viewing 상태로 이동했지만 선택 상태의 일부 속성을 지우는 것을 잊은것이죠.

```swift
class DreamListViewController: UITableViewController {
  // UI state properties.
  var isInViewingMode: Bool
  var selectedRows: IndexSet?
  var sharingDreams: [Dream]?
}
```

![](https://velog.velcdn.com/images/dev_kickbell/post/f9db70e6-85b4-4b8f-9d43-ac76c5629c66/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/9c49c5a5-48e2-4ba8-871b-449aa7e2aa21/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/af0aa152-d066-4c4b-b1fb-cd7253d3da63/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/807f4cdf-a209-46cb-a1c2-fe00dbd6273c/image.png)


### UI의 상태 속성을 enum으로 정의하여 코드 개선하기 
- isInViewingMode, selectedRows, sharingDreams는 DreamListViewController의 속성이죠. 
- 그리고 이런 상태 속성 수는 앱의 기능 집합이 커짐에 따라 쉽게 증가할 수 있습니다. 문제는 이런 상태 속성 값들이 서로 전혀 관련이 없다는 것이죠. 공유하지 않는다는 것입니다. 
- 그러나 지금 현재 코드에 따르면 viewing을 설정하면 selecting이라는 다른 속성은 지워야하며 이런 것들로 버그가 발생하기 매우 쉽습니다. 
- 그렇다면 이 문제를 어떻게 해결할 수 있을까요? 열거형은 실제로 상호 배타적인 값에 완벽합니다. 이전에 있었던 유효하지 않은 상태 버그가 이제는 가능하지도 않고 유형 시스템에 의해 적용되기 때문에 좋습니다.
- 따라서 이 접근 방식은 중간 상태의 가능성 없이 상태가 한 번에 모두 변경됨을 의미합니다.
- 그리고 보너스로 모든 상태를 한 곳에서 관리하면 사용자가 남긴 상태와 정확히 동일한 상태로 앱을 더 쉽게 시작할 수 있습니다.
- 따라서 애플리케이션에서 상태 복원을 구현한 방법을 확인하려면 [여기](https://developer.apple.com/library/archive/LucidDreams/Introduction/Intro.html)에 있습니다. 

```swift
class DreamListViewController: UITableViewController {
  var state: State
}

enum State {
  case viewing
  case sharing(dreams: [Dream])
  case selecting(selectedRows: IndexSet)
}
```

## 5. 정리하기 
- 목표는 MVC 아키텍쳐 전체에서 앱의 로컬 추론을 개선하는 것이었습니다. 값 타입과 프로토콜을 사용했지요. 
- Model 
    - 모델에서는 struct로 변경해서 값 타입을 갖도록 했습니다. 
    - 의도치 않은 공유 문제를 제거했죠. 
- View
    - 뷰에서는 DecoratingLayout, CascadingLayout 같은 작은 컴포넌트를 만들었습니다. 
    - 그리고 이런 것들은 프로토콜과 제네릭을 사용해서 최대한 재사용할 수 있게 했습니다.
    - 또한 모든 레이아웃 코드가 한 곳에 있어 로컬 추론이 향상되었습니다. 
    - 마지막으로 각 타입은 작고 독립적이어서 테스트하기가 쉬웠습니다. 
- Controller
    - 컨트롤러에서는 각각 나눠져있던 모델 인스턴스를 하나로 묶고, 실행 취소 시에 모델을 통째로 바꿔버리는 방법으로 구현을 변경했습니다. 
    - 이렇게 되면서 단일 코드 경로로 실행 취소를 구현했고, UI 업데이트 또한 didSet을 이용해서 한 곳에서 동작하고 순서에 영향을 받지 않도록 바꾸었습니다. 
    - 또, UI의 상태값을 열거형으로 변경해서 UI가 일관성 없는 상태가 될 가능성을 없앴습니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/f6a309b3-4534-401a-ad36-4c3a0084e878/image.png)

### 6. 집에 가져가야 할 몇가지
- 상속 대신 구성(Composition)을 사용해서 문제를 해결하는 방법을 생각해보세요. 
- 일반적인 재사용 가능한 코드에 프로토콜을 사용해보세요. 로컬에서 쉽게 추론하고 테스트하기 쉬운 재사용 가능한 작은 구성 요소를 만들 수 있습니다.
- 값 의미론(Value semantics)을 활용하는 방법을 생각해보세요. 
- 로컬 추론을 향상시킬 방법을 생각해보세요. 로컬 추론은 실제로 UI 프로그래밍에만 국한되지 않고 모바일 개발에만 국한되지 않으며 Swift에만 국한되지 않는 매우 일반적인 기술입니다. 이제 Swift가 값 유형을 강조하는 것은 우연이 아닙니다. 값 유형은 코드에 대해 로컬로 추론할 수 있는 매우 중요한 측면이기 때문입니다.

## Reference
[https://developer.apple.com/videos/play/wwdc2016/419](https://developer.apple.com/videos/play/wwdc2016/419)


## Endnotes 

### ¹로컬 추론(Local Reasoning)
일부 코드를 볼 때 나머지 코드가 해당 함수/클래스 등과 상호 작용하는 방식에 대해 생각할 필요가 없는 것을 말합니다. 최대한 그 부분만 보면 코드를 파악할 수 있게 해놓아서 다른 부분은 보지 않아도 되는 그런 것 말이죠. 

```swift
//로컬추론 적용 전
let myView = UIView()

func setupUI() {
    myView.backgroundColor = UIColor.blue
    myView.layer.cornerRadius = myCustomView.bounds.width / 2
}

override func viewDidLoad() {
    super.viewDidLoad()
    setupUI()
}

//로컬추론 적용 후
lazy var myView: UIView = {
    let view = UIView()
    view.backgroundColor = UIColor.blue
    view.layer.cornerRadius = myCustomView.bounds.width / 2
    return view
}()
```
```swift
//로컬추론 적용 전
let button = UIButton()

override func viewDidLoad() {
    super.viewDidLoad()
    button.addTarget(self, action: #selector(buttonDidTap), for: .touchUpInside)
    view.addSubview(button)
}

@objc private func buttonDidTap() {
    print("pressed!")
}

//로컬추론 적용 후
let button = MyButton()

override func viewDidLoad() {
    super.viewDidLoad()
    button.action = {
        print("pressed!")
    }
    view.addSubview(button)
}
```
```swift
//로컬추론 적용 전
class MyViewController: UIViewController, UITableViewDelegate, UITableViewDataSource, UITableViewDataSourcePrefetching {
    //MyViewController 관련 코드
    //UITableViewDelegate 관련 코드
    //UITableViewDataSource 관련 코드
    //UITableViewDataSourcePrefetching 관련 코드
    //...
}

//로컬추론 적용 후
class MyViewController: UIViewController {
    //MyViewController 관련 코드
}

extension MyViewController: UITableViewDataSource {
    func tableView(
        _ tableView: UITableView,
        numberOfRowsInSection section: Int
    ) -> Int {...}
    
    func tableView(
        _ tableView: UITableView,
        cellForRowAt indexPath: IndexPath
    ) -> UITableViewCell {...}
}

extension MyViewController: UITableViewDelegate {
    func tableView(
        _ tableView: UITableView,
        willDisplay cell: UITableViewCell,
        forRowAt indexPath: IndexPath
    ) {...}
    func tableView(
        _ tableView: UITableView,
        didSelectRowAt indexPath: IndexPath
    ) {...}
}

extension MyViewController: UITableViewDataSourcePrefetching {
    func tableView(
        _ tableView: UITableView,
        prefetchRowsAt indexPaths: [IndexPath]
    ) {...}
}
```


### ²소급 모델링(Retroactive Modeling) 
- 원래는 소급, 소급하다라는 말이 그런 뜻이죠. "과거에까지 거슬러 올라가서 미치게 하다." 예를 들어, 물가 상승률로 인해 월급이 올랐어요. 그런데 월급 인상에 대한 말은 6월달에 나왔는데 결정은 12월에 난겁니다. 
- 그러면 6개월은 월급 인상된 금액이 아니라 기존 월급대로 받았잖아요? 그래서 월급이 200에서 210으로 올랐다면, 6월에 처음 이야기가 나왔으니 6월부터 오른것으로 해서 이번달 월급에 60만원을 더주겠다는 겁니다. 
- Swift에서는 소급 모델링이 그러면 무슨 뜻일까요 ? Swift에서는 기존 소스 코드에 접근 권한이 없는 타입이 확장해서 접근이 가능해지는 것을 말합니다. 
- 위의 코드를 예로 들면, UIView와 SKNode는 extension을 통해 Layout이라는 프로토콜을 준수하죠. 그러면 Layout의 frame이라는 속성에 접근할 수 있는 권한이 생기는 겁니다. 

```swift
protocol Layout {
  var frame: CGRect { get set }
}

extension UIView: Layout {}
extension SKNode: Layout {}
```


### ³UndoManager
- 실행 취소 및 재실행 기능을 지원해주는 Swift의 클래스입니다. 예시 코드는 아래와 같습니다. 
- 공식 홈페이지는 [여기](https://developer.apple.com/documentation/foundation/undomanager
)이고, 아래 GIF처럼 재밌는 기능도 구현할 수 있습니다. 자료 출처는 [여기](https://github.com/adarshvcdev/swiftundomanager/blob/master/SwiftUndoManager/SwiftUndoManager/ViewController.swift)입니다.

```swift
var manager = UndoManager()
var bouquetSelection: NSMutableArray = ["lilac", "lavender"]
func pull(flower: String) {
    bouquetSelection.remove(flower)
    manager.registerUndo(withTarget: bouquetSelection) { $0.add(flower) }
}
pull(flower: "lilac")
// bouquetSelection == ["lavender"]
manager.undo()
// bouquetSelection == ["lavender", "lilac"]
```

![](https://velog.velcdn.com/images/dev_kickbell/post/607a04f3-af3e-4903-afe8-6fa3899e81a8/image.gif)





