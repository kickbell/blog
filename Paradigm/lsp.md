# LSP, 리스코프 치환 원칙

이 글은 [베어코드](https://www.youtube.com/channel/UCEuyt4RB4mlMfADQMz-d2DQ) 님의 [Swift Object Oriented Progoramming](https://youtube.com/playlist?list=PLoPKxuu4\_dG3jCiMcbskRuNsgZsdZ4zz6) 을 보고 정리한 글 입니다.

> ### 요약

* Barbara Liskov (1988)제안, 미국 최초 여자 컴퓨터박사?
* 상속을 할 때는 이러이렇게 해야한다는 기준을 세워주는 원칙이다. 만약에 상속에서 그 기준을 지키지 않으면 매우 고통스럽다는 것이다.
* T(super)를 상속받은 S(child)가 있을 때, T의 객체를 S로 교체했을때도 잘 동작해야 한다는 말임.
* NSString과 NSMutableString을 실제로 구현해야 한다고 하면? 무슨방법이 리스코프 치환원칙을 준수하느냐.
  *
    1. NSString과 NSMutableString을 각각 별도의 다른 클래스로 만든다. NSString을NSMutableString로 바꿀 수 있나? 없다. (리스코프 치환원칙 위배)
  *
    1. NSString을 상속받은 NSMutableString을 만든다. 상속받았으니 NSString에NSMutableString이 들어가도 괜찮다. NSString에+Mutable 기능이 확장되는 개념(OK)
  *
    1. NSMutableString을 상속받은 NSString을 만든다. 바꿀 수 있나? 불가하다. NSString은 Mutable이 되지 않는다. (리스코프 치환원칙 위배)
* 2번을 보면 NSString에+Mutable 기능이 확장되는 개념인데 마냥 확장하는 것은 좋은게 아니다. 확장할 때의 기준이 필요하다. 즉 상속할때의 기준. 그것이 바로 리스코프 치환원칙
* 직사각형을 상속받은 정사각형 vs 정사각형을 상속받은 직사각형 ?
  *
    1. 정답은 둘 다 아니다. 상속관계가 성립되면 안된다.
  *
    1. 일반적인 관념으로 정사각형은 직사각형의 한종류다. 하지만 setWidth라는 메소드가 있을때, 직사각형의 width를 2배로하면 넓이도 2배가 되지만, 정사각형은 4배가 되어야한다. 그렇지 않으면 그건 정사각형이 아니니까. 즉, 자식이 거부할 수 밖에 없는 기능을 부모가 제공하고 있다.
* 개, 고양이에 "걷기", "뛰기"의 행위를 추가하면 그 클래스는 동물 레벨로 올릴 수 없다. 붕어나 고래는 "걷기", "뛰기"를 하지 못하기 때문이다. 즉 또 부모의 기능을 퇴화시킬수밖에 없는것이다. 이런 경우처럼 "걷기", "뛰기", "헤엄치기" 같은 행위는 상속과 별도로 존재해야 한다. 이렇게 해버리면, 개발하다가 도중에 아차! 해버리고 결국엔 이미 개발이 진행된 상황에서 바꿀수도 안바꿀수도 없는 슬픈상황이 벌어진다.

> ### 상속할 때 해서는 안되는 행위(판단기준)

* 부모의 행동을 자식이 거부 NSMutableString을 상속받은 NSString. 부모가 더 추상화(기능이많은?..)되어있는데 그게 막혀버려서 제대로 동작하지않거나 컴파일조차 안될 수 있음.
* 퇴화함수 슈퍼클래스의 함수를 오버라이드 한다음에 그 기능을 못쓰게 한다던지(기능이 시작되기 전에 에러를 보내버린다던지), 또는 원래 있던 기능을 지워버리고 빈구현을 만든다던지

> ### LSP를 위반하면?

[여기코드](https://github.com/kickbell/OOP/blob/main/SOLID/SOLID/LSP.swift)를 보면 설명이 잘 나와있지만, 정리한다.

* 모든 클래스에서 하위 클래스를 명시적으로 지정해서 코딩해야 한다.(코드복잡도 증가)
* 부모클래스가 자식클래스를 알아야하는 황당한 일이 발생..
* 당연히 사용자는 상속할때 상속의 기능이 잘 동작할 것을 가정하고 사용하지않나? 근데 그게 안되버리는거다. 그래서 그게 꼬이고 꼬이면 그걸 되돌리는 비용이 어마어마하게 들 수 있다.

> ### LSP를 잘지키면?

* 상위 클래스를 기준으로 작성된 코드가 문제없이 동작
* 추상화된 인터페이스 하나로 공통의 코드를 작성할 수 있음
  * 상속된 수많은 다른 클래스를 일일히 재구현해야하는 고민을 하지 않음(코드의 재활용)
  * Swift에서 LSP의 대표적인 예시가 바로 protocol extension 이다.
  * protocol은 추상화된 인터페이스이고, 우리는 클래스를 만들고 프로토콜을 준수해서 코드를 짠다. 밑바탕 개념에 LSP가 있는 것이다.
* 상속에 있어서 가장 중요한 기본 원칙 제시
* OCP를 가능하게 해주는 원칙
* 자료형 S가 자료형 T의 하위형이라면 필요한 프로그램의 속성(정확성, 수행하는 업무등)의 변경 없이 자료형 T의 객체를 자료형 S의 객체로 교체(치환)할 수 있어야 한다.
* 이 말을 자세히 풀면 superclass(부모클래스)를 가지고 작성된 코드에다가 childclass(자식클래스, 서브클래스)를 넣었을 때에도 `superclass(부모클래스)를 가지고 작성된 코드`는 정상적으로 잘 동작해야 된다는 말이다.
* 그리고 그걸 정리하면 "객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다."이라고 할 수 있겠다.
* Barbara Liskov (1988)
* 상속에 있어서 가장 중요한 기본 원칙 제시
* OCP를 가능하게 해주는 원칙

#### 잘못된 상속

* 잘못된 상속은 LSP를 위반하는 경우가 많음
* 혹은 위반 할 수 없게 만듦.

#### 예시 : NSString과 NSMutableString을 구현해야 한다면?

1. NSString과 NSMutableString을 완전 상관없는 별도의 클래스로 설계한다.
   * 애초에 NSString에 NSMutableString을 넣을 수 없을테니 기각
2. NSString(super class)을 상속받은 NSMutableString을 만든다.
   * 된다.
3. NSMutableString(super class)을 상속받은 NSString을 만든다.
   * NSString을 NSMutableString에 넣어도 NSString은 Mutable이 아니므로 안된다.

#### 상속할 때 해서는 안되는 행위

* 부모의 행위를 자식을 거부
  * NSMutableString이 superclass일때 NSMutableString에는 appendstring이라고 스트링을 추가해주는 기능이있었는데 그걸 막아버린거다. 부모의 행위를 자식이 거부한 케이스에 해당.
* 퇴화함수
  * super클래스에 있던 기능을 자식클래스가 가져와서 빈구현을 만든다던지, 아니면 에러를 보낸다던지 하는 식으로 만듬. 즉 원래있던 동작마저도 동작하지 못하게 퇴화시켜버림.

#### 직사각형과 정사각형은 어떤 상속구조를 가지나? 뭐가 맞을까?

1. 직사각형을 상속 받은 정사각형
2. 정사각형을 상속 받은 직사각형

정사각형은 직사각형의 한 종류이지않나? 1번이 맞을거라고 생각한다. 하지만 정답은 둘 사이엔 상속관계가 있으면 안된다.

* 직사각형과 정사각형의 관계
* 일반적인 관념은 정사각형은 직사각형의 한 종류라고 생각한다. 그래서 부모(직사각형)을 상속받은 자식(정사각형은 얼핏 당연해보인다.)
* 하지만, setWidth 함수라는 것을 예로들어볼 때
  * 정사각형의 경우 height도 같이 바뀌어야 한다.
  * 직사각형은 setWidth로 폭을 2배로 하면 면적이 2배가 된다.
  * 정사각형은 setWidth로 폭을 2배로 하면 면적이 4배가 된다.
  * 즉, 부모의 정합성(서로 모순이 없이 일관되게 일치해야 한다는 의미)을 깨버린다.
* 자식(정사각형)이 거부할 수 밖에 없는 기능을 부모(직사각형)가 제공하고 있으므로 이건 상속관계가 있으면 안되는 관계다. 즉, LSP를 위반하는 관계이다.

#### 상속에 관해 더 들여다보기

* 개, 고양이에 `걷기`, `뛰기`를 추가하면 그 행위를 동물레벨로 올릴 수 없다. 붕어나 고래는 `걷기`, `뛰기`를 퇴화시킬 수 밖에 없으므로
* 동물의 상속구조에서 `걷기`, `뛰기`의 행위는 상속구조 자체에 넣을 수 없다.
* `걷기`, `뛰기`, `헤엄치기` 등의 행위는 상속과 별도로 존재해야만 한다.

#### 어떤 문제를 일으키고 있나?

* 우리가 쓰고있는 애들 중에도 LSP를 안지키는 애가 있다.

1. 아울렛 10개가 있고 ![](https://images.velog.io/images/dev\_kickbell/post/426d2888-aff5-45bd-8fa1-f5c1c555faee/image.png)
2. 이런 작업을 해주면 잘동작한다. ![](https://images.velog.io/images/dev\_kickbell/post/5aff7b91-9bb8-4297-98c6-f07a259b5622/image.png)
3. 근데 기대값을 300으로 보고 이런작업을 했더니? 279가 나온다. ![](https://images.velog.io/images/dev\_kickbell/post/01e9e02c-61ec-42dc-ba01-5136b92541fb/image.png)

왜 그럴까? 1번의 아울렛은 모두 UIView의 자식클래스이다. 근데 300을 기대했는데 실제값은 279가 나왔다. 10개의 뷰중에 누군가가 height를 세팅하는 것을 거부한거다. 저중에 크기를 바꿀수없는 애들이 있다는거다. 그말은 저녀석들 중에도 LSP를 안지키는 애들이 있다는 거다.

* LSP가 위반되고 있어도 UIKit을 사용하는데 큰 불편함은 없다.
* View중에 size를 바꿀 수 없는 것이 있다는 것을 경험과 학습을 통해 알고 있기 때문이다.
* 하지만 이런 경우가 많으면 코딩하기 힘들어진다.

#### LSP를 위반하게 되면 ?

* 모든 클래스에서 하위클래스를 명시적으로 지정해서 코딩해야함.

> 이건 뭔말이냐면, 예를들어 아까 위에처럼 views.reduce(0) { $0 + $1.frage.height } 과 같은 식으로 300을 기대하고 코드를 진행한다. 치자. 근데 높이를 바꿀 수없는 애가 뭐가 하나 있잖아? 그러면 그 클래스가 뭔지를 지정해서 그것만 분기처리를 해줘야 300이 나올거아니야? 그말이다. 이게 사이즈조절이 안되는 ㅇㅇ이니까 이거는 따로 지정해서 분기처리해줘야 한다 이런느낌

* OCP를 사용할 수 없게 됨.

> 긍까 결국그거야. 추상화된 인터페이스를 선언하고 그걸 상속받아서 뭘만들어야되는상황이야. 그니까 우리는 아래 UIView를 보면 UIResponder만 상속이고 나머지는 다 protocol이잖아. 그말은 뭐냐면, 나머지 추상화된 인터페이스가 옵셔널이아니면 다 동작할 것을 확신하고 있는거지. 근데? LSP를 위반하면 그게 슈퍼클래스의 그런 것들이 동작한다고 확신할수가 없는 상황이 되는거야.

```swift
    open class UIView : UIResponder, NSCoding, UIAppearance, 
    UIAppearanceContainer, UIDynamicItem, UITraitEnvironment, 
    UICoordinateSpace, UIFocusItem, UIFocusItemContainer, CALayerDelegate {...}
```

* 코드의 복잡도를 높임.

> LSP를 위반해서 잘못된 상속을 해놓으면 그걸 되돌리는대는 매우큰 cost가 발생한다.

* 부모클래스가 자식클래스를 알아야 하는 경우도 발생.

> 부모클래스인 uiview가 앞으로 자식클래스가 뭐가 생길건데 얘는 높이값이 안먹으니까.. 어떻게 처리해줘야되지? 같은 식으로 자식을 알아야하는 황당한 상황이 발생쓰.

* 복잡한 상속관계를 가지는 UIKit같은 경우도 대부분은 다 LSP를 지켜주고 있다. 그래서 믿고 작업하고있음.

#### LSP를 지키게 되면 ? (이부분 뭔말인지 잘모르겠음)

* 상위 클래스를 기준으로 작성된 코드가 문제없이 동작
* 추상화된 인터페이스 하나로 공통의 코드를 작성할 수 있음
  * 상속된 수많은 다른 클래스를 일일히 재구현해야하는 고민을 하지 않음(코드의 재활용)
  * Swift에서 LSP의 대표적인 예시가 바로 protocol extension 이다.
  * protocol은 추상화된 인터페이스이고, 우리는 클래스를 만들고 프로토콜을 준수해서 코드를 짠다. 밑바탕 개념에 LSP가 있는 것이다.

> A라는 클래스에서 기능을 확장하기 위해서 B라는 클래스를 만드는데 그것은 좋은 습관이 아니다. 또 반대로 자식클래스에서 공통된 작업들이 있어서 그걸 super클래스로 끌어올리는 경우도 있다. 근데 그것도 딱히 좋은 방법은 아닌것같다고 함. 그리고 LSP, OSP를 지키다보니 추상화된 인터페이스 하나로 공통의 코드를 작성할 수 있다는 장점이 있는데 이게 `목적`은 아니라고함. 이걸 목적으로하면 엉뚱한 방향으로 갈수있대.

#### 현실적인 이야기

* 모든 코드에서 LSP를 지키기는 어려움
* 적정선에서 trade-off
* LSP를 이해하고 따르면
  * 설계에 도움이 됨
  * 발생할 문제를 사전에 막을 수 있음
  * 복잡하고 이해할 수 없는 상속을 만들지 않게 함
