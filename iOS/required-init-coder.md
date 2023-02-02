# required init?(coder: NSCoder)



## 1. required init

개발을 하다보면 꼭 한번 봤던 에러가 있을 거에요. 바로 아래 코드의 에러인데요. 커스텀셀, 뷰와 같은 것들을 구현하려고 할 때 주로 발생하죠. `init(coder:)` 를 꼭 제공해야 한다고 하는데 왜 그런 걸까요 ? 간단히 알아보도록 하겠습니다. 
    
```swift
import UIKit

class MyCell: UICollectionViewCell {
    override init(frame: CGRect) {
        super.init(frame: frame)
    }
    
    //'required' initializer 'init(coder:)' must be provided by subclass of 'UICollectionViewCell'
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
```

일단 `required init` 키워드부터 보고 가야합니다. `required init` 은 이름 그대로 필수 생성자죠. 그런데 `swift` 에서는 부모 클래스에서 `required init` 을 구현한 경우에 자식 클래스에서 부모 클래스의 생성자를 상속받지 않는 한 자식 클래스에서 해당 필수 생성자를 반드시 구현해주어야 합니다. 

코드로 예시를 들어본다면, 아래처럼 `Human` 이라는 부모 클래스가 있고 `required init` 를 구현해놓았죠. 그리고 `Student` 라는 자식 클래스가 있어요. 여기서 `required init` 을 구현하지 않으면 에러가 발생합니다. 

반면에 자식 클래스인 `Student` 에서 `init(지정 이니셜라이저)` 을 구현하지 않고, `grade` 라는 속성만 선언하고 부모 클래스인 `Human` 을 상속받아 부모의 생성자를 모두 상속받는 경우에는 에러가 발생하지 않습니다. 즉, `required init(필수 이니셜라이저)` 는 자식 클래스에서 `init(지정 이니셜라이저)` 를 구현 했을 때에만 에러가 발생하는 것이지요. `init(지정 이니셜라이저)` 를 구현한다는 것은 부모 클래스의 생성자를 그대로 상속받지 않는 것과 같은 말이기 때문이니까요. 

```swift
class Human {
    var name: String?
    
    required init(name: String) {
        self.name = name
    }
}

//에러 발생 
class Student: Human {
    var grade: Int
    
    //지정 이니셜라이저
    init(grade: Int, name: String) {
        self.grade = grade
        super.init(name: name)
    }
    
    //'required' initializer 'init(name:)' must be provided by subclass of 'Human'
    required init(name: String) {
        fatalError("init(name:) has not been implemented")
    }
}

//에러 발생하지 않음
class Student: Human {
    var grade: Int?
}
```

자, 그러면 우리가 커스텀 뷰를 만들 때 부모 클래스에서 `required init(필수 이니셜라이저)` 가 선언되어 있겠네요. 그렇죠? 어디일까요? 네, 모든 커스텀 뷰들은 대부분 코코아프레임워크의 `UIView` 를 상속받고 있죠. 그리고 그 `UIView` 가 `NSCoding` 이라는 프로토콜을 준수하고 있어요. 그리고 그곳에 `init?(coder: NSCoder)` 가 있지요. 

```swift
@available(iOS 2.0, *)
open class UIView : UIResponder, NSCoding, ... { 
    public init(frame: CGRect)
    public init?(coder: NSCoder)

	//...
}

public protocol NSCoding {
    func encode(with coder: NSCoder)
    init?(coder: NSCoder) // NS_DESIGNATED_INITIALIZER
}
```

음? 근데요? `UIView` 클래스에는 `required` 키워드가 없는데요? 라고 알아채셨다면 눈썰미가 좋으신 겁니다😄. 보통 그냥 복잡해서 포기하거나 못찾기도 하니까요. `UIView` 클래스에 `required` 키워드가 없는 이유는 `UIView` 가 클래스라 그런겁니다. 

무슨 말이냐면, 아래 코드를 예시로 들어볼게요. 프로토콜을 사용하면 우리는 해당 프로토콜의 내용을 꼭 구현해야 한다고 강제할 수 있잖아요. 그런데, 클래스는 아시다시피 상속이라는게 있습니다. 

그래서 `swift` 에서는 프로토콜에 생성자에 관한 요구사항이 있을 경우 `required` 키워드를 꼭 붙이게 해서 해당 클래스를 상속받는 모든 자식 클래스들이 필수 이니셜라이저를 구현한다는 것을 보장하는 것입니다. 

실제로 아래의 `Dog` 를 상속받는 `AnotherDog` 처럼 해당 클래스에 `required init` 을 또 구현하는 것은 아닌 것 같고, 컴파일러가 알아서 구현해주거나 하는 것 같아요. 이런 이유로 `Dog` 라는 클래스에 `final` 이라는 키워드를 붙여서 이 클래스는 더이상 상속하지 않는다는 것을 알려주는 경우에는 `init` 앞에 `required` 를 없애도 에러가 발생하지 않습니다.

같은 맥락으로 매번 값을 복사해서 사용하는 구조체의 경우에는 마찬가지로 `required` 키워드를 붙이지 않죠. 정리하면, 내가 만드는 커스텀 뷰가 `UIView` 를 상속 받고 있었고 또 `UIView` 에서 `NSCoding` 이라는 프로토콜을 준수했기 때문에 해당 에러가 발생했던 것이다 라고 일단은 볼 수 있겠지요. 

```swift
protocol NameProtocol {
    init(name: String)
}

class Dog: NameProtocol {
    //Initializer requirement 'init(name:)' can only be satisfied by a 'required' initializer in non-final class 'Dog'
    required init(name: String) {

    }
}

class AnotherDog: Dog  {

}

final class Dog: NameProtocol {
    init(name: String) {

    }
}

struct Cat: NameProtocol {
    init(name: String) {
        
    }
}
```

## 2. init?(coder: NSCoder)

자, 끝난 게 아닙니다. 일단 `required` 키워드 까지는 어찌저찌 정리가 됐죠. 다음은 `init?(coder: NSCoder)` 차례인데요. 얘는 도대체 뭐고 어디에 사용하는 녀석인가에 대해서 이야기 해볼게요. 

우리가 뷰를 만들 때, `UI` 를 그리는 방법이 2가지가 있죠. 하나는 코드를 통한 방법이고 또 하나는 `storyboard, xib` 와 같은 `interface builder` 를 통한 방법입니다. 그런데, 여기서 스토리보드를 통한 방법은 내부적으로 `xml` 파일 형식으로 저장이 되요. 실제로 `Xcode` 에서 아래의 그림처럼 스토리보드에 소스 코드 메뉴를 선택하면 내부는 `xml` 파일 형식으로 되어있는 것을 직접 확인할 수가 있지요. 즉, 우리가 스토리보드에 레이블을 하나 가져다놓고, 텍스트를 적고, 색상을 변경하고 하는 것들이 즉각적으로 `xml` 파일로 변환이 되고 있는 것이죠.  

![](https://velog.velcdn.com/images/dev_kickbell/post/5f844106-8038-45b1-8f47-cdbce463a12b/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/75d6f7de-a91b-4787-bc2c-a127c261bb97/image.png)

하지만 이렇게 스토리보드에서 `xml` 파일로 되어있는 코드를 실행하려면 일련의 작업이 필요합니다. 여기서 `archiving/unarchiving` 라는 개념이 나오는데요. 간략하게 설명을 하자면 아카이빙은 `iOS` 에서 모델 객체를 저장하는 방법 중의 하나에요. 그리고 언아카이빙은 이렇게 아카이빙된 데이터로부터 우리가 사용할 수 있게 객체를 다시 만드는 것을 말합니다. 

그러니까 `init?(coder:NSCoder)` 이 생성자잖아요? 즉, 외부에서 `NSCoder` 타입을 주입받겠다는 거겠죠. 그러면 외부에서 `NSCoder` 타입의 어떤 데이터가 들어올 것이고, 얘를 사용해서 객체를 다시 만든 다음에 리턴(초기화)되는 것이죠. 

정리하면 `storyboard, xib` 같은 `interface builder` 들은 코드가 아니기 때문에 컴파일 시점에 컴파일러가 인식을 할 수가 없어요. 그래서 컴파일러가 인식할 수 있게 `unarchiving` 과정이 필요합니다. 그래서 `xml` 데이터를 통해 `init?(coder:NSCoder)` 으로 데이터를 주입받고 컴파일러가 인식할 수 있도록 `unarchiving` 과정을 거친다는 것이지요. 

조금 헷갈리시죠 ? 다시 간단하게 정리해볼게요. 

자, 우리는 `UIView` 를 그려요. `UIView` 를 그리려면 뭔가 초기화를 해야겠죠. 그리는 방법이 코드를 통한 방법과 `interface builder` 를 통한 방법이 있듯 초기화도 2가지 방법이 있어요. 우리가 맨날 보는 아래의 생성자이지요. 

`init(frame: CGRect)`은 코드로 `UIView` 를 상속받은 커스텀 뷰를 초기화할 때 사용해요. 반면에 `init?(coder: NSCoder)` 는 `interface builder` 를 통한 뷰를 초기화할 때 사용하죠. 그리고 코드가 아닌 `interface builder` 의 데이터를 컴파일러가 인식하게 해주기 위해서 `unarchiving` 이라는 일련의 과정이 필요한 것이구요. 

```swift
init(frame: CGRect)
init?(coder: NSCoder)
```

## 3. 정리하기

그래서. `required init` 도 알고, `init?(coder: NSCoder)` 도 알겠는데 도대체 왜 `init(frame: CGRect)` 를 했을 때, `required init?(coder: NSCoder)` 코드가 필요한 거냐고!! 라고 물으실 수 있어요. 결론을 내려보겠습니다. 

우리가 맨 처음에 적었던 아래의 코드처럼 `MyCell` 클래스를 만들고, `UICollectionViewCell` 를 상속합니다. 그리고 `init(frame: CGRect)` 를 `override` 하지요. 

이건 무엇을 뜻하나요? 이것은 `"UIView를 생성하는 방법을 내가 직접 코드로 하겠다."` 라는 뜻입니다. 즉, `"interface builder 를 사용하지 않고 코드를 통한 방식으로 난 나만의 멋드러진 커스텀 뷰를 만들겠어"` 라고 컴파일러에게 선언하는 거죠. 

그리고 여기서 우리가 `init(frame: CGRect)` 를 `override` 하거나 또는 새롭게 생성자를 구현한다는 것은 결국에 부모의 생성자를 상속받지 않겠다는 뜻이 됩니다. 

그러면 `swift` 에서는 부모 클래스에서 `required init` 을 구현한 경우에 자식 클래스에서 부모 클래스의 생성자를 상속받지 않는 한 자식 클래스에서 해당 필수 생성자를 반드시 구현해주어야 한다는 규칙이 있었죠. 그래서 `required init` 를 구현해주어야 하는 겁니다. 

그런데, 내부의 `fatalError` 는 뭘까요? 이것은 어차피 `interface builder` 를 사용하지 않고 코드로 구현될 것이니 이 `required init` 가 불린다면 이것은 완전히 잘못된 거야. 치명적인 에러야! 라는 의미로 자동으로 생성된 것입니다. 

따라서 `fatalError` 가 발생한 경우에 말그대로 앱이 그대로 종료되버리기 때문에 찝찝하시다면 `super.init(coder: coder)` 로 바꿔두셔도 괜찮습니다. 추가적으로 많이 오래됐지만 애플의 `Archives and Serializations Programming Guide` 문서를 링크로 첨부해놓도록 하겠습니다.

```swift
import UIKit

class MyCell: UICollectionViewCell {
    override init(frame: CGRect) {
        super.init(frame: frame)
    }
    
    //'required' initializer 'init(coder:)' must be provided by subclass of 'UICollectionViewCell'
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
```

## Reference

[https://developer.apple.com/documentation/foundation/nscoding/1416145-init](https://developer.apple.com/documentation/foundation/nscoding/1416145-init)

[https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Archiving/Archiving.html#//apple_ref/doc/uid/10000047i](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Archiving/Archiving.html#//apple_ref/doc/uid/10000047i)			  




