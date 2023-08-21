# ISP, 인터페이스 분리 원칙

이 글은 [베어코드](https://www.youtube.com/channel/UCEuyt4RB4mlMfADQMz-d2DQ) 님의 [Swift Object Oriented Progoramming](https://youtube.com/playlist?list=PLoPKxuu4\_dG3jCiMcbskRuNsgZsdZ4zz6) 을 보고 정리한 글 입니다.

> ## 요약 ISP 1

* "클라이언트가 자신이 이용하지 않는 메서드에 의존하지 않아야 한다." 를 다시 말하면,
* 클라이언트(코드를 사용하는쪽)가 자신이 이용하지 않는 인터페이스(프로토콜, 객체)에 정의되어 있는 모든 프로퍼티, function을 사용하는 것이 좋다.
* SRP 같은거 아니야? 응 묘하게 다르다.
* 클라이언트(어떤 객체를 사용하는쪽), 서버(서비스라고도 많이 부름, 사용되는 쪽)이다. 근데 위에서 의존하지 않아야 된다고 했다. 여기서 의존은 "사용"하고 있다는 것과 같은 말이다. 즉, 서버(서비스, 사용되는쪽)은 클라이언트가 필요로 하는 최소한의 인터페이스만 제공해서 둘 간의 의존도를 낮추어야 한다는 말이다. (우리가 private, internal, public처럼 설계하는 것도 같은 맥락이다.)
* 2가지 경우가 있는데, 상속(슈퍼클래스의 메소드나 객체)해서 사용하는 경우와 내 클래스 내부에서 해당 서버(서비스)의 인스턴스를 생성해서 사용(의존)하는 경우가 있다.
* [상속(채택)을 통해 사용](https://github.com/kickbell/OOP/blob/main/SOLID/SOLID/ISP.swift) : 어떤 프로토콜을 채택한다고 했을 때, 3개중에 1개만 사용한다고 하면 상속받은 메소드를 퇴화시켜야 한다.(LSP 위반), @objc 문법으로 호환하면 optional이라는 키워드를 통해 해당 메소드를 선택적으로 처리할 수 있지만, 그것은 순수 swift의 문법이 아니므로 지양하는 것이 좋다. 그러느니 프로토콜을 여러개로 나누어야 한다.
* 인스턴스를 사용하는 경우 : 직접적으로 인스턴스를 생성해서 메소드를 사용하지 않으면 큰 커플링이 발생하진않는다. 다만 컴파일시 의존관계(사용에따른)에 의해 불필요한 컴파일이 필요할 수 있고, 해당 모듈(라이브러리)가 업데이트 됨에 따라서 불필요한 모듈 업데이트를 유발할 수 있는 부분이 있다.
* 정리하자면, 큰 덩어리의 인터페이스들을 구체적으로 작은 단위들로 분리시켜 클라이언트들이 꼭 필요한 메소드만 사용할 수 있게 해야 한다는 것이 ISP를 뜻한다.
* **objc같은 경우는 프로토콜 덩치가 굉장히 크고, 델리게이션에 치우쳐져 있었다.** 그런데 swift 같은 경우에는 프로토콜이 굉장히 작은 단위들로 구성되고, 그 프로토콜들을 다시 조합해서 사용하는 그런 상황들을 많이 볼 수 있다.
* 예를 들면, [String과 Hashable](https://github.com/kickbell/OOP/blob/main/SOLID/SOLID/ISP.swift)이 있겠다. String > Hashable 이니까 더 작은 인터페이스를 써서 클라이언트와 String의 의존관계를 끊어줄 수 있겠다.
* 같은 예시로 Equatable, Comprable도 굉장히 잘게 쪼개져서 언제든지 조합할 수 있게 되어 있다.
* 또 다른 예시, iOS의 `UITableViewDataSource, UITableViewDelegate`. objc에서 보다 swift에서 더욱 잘게 분해되어 있다.
* 클라이언트와 서버측의 작업에서, 통상적으로 클라이언트 쪽이 서버와 강하게 커플링된다. 별도로 분리된 인터페이스를 사용하지 않고 직접 서버클래스의 인스턴스를 생성?해와서 바로 사용하기 때문이다. 이렇게 되면 서버측의 코드 변경이 클라이언트에 강하게 영향을 미친다.(메소드 명이 변경됐다던지)
* 그럼 어떻게 해야하나 ? 서버클래스를 SRP를 준수하게 잘게 분해한다. 예를들어 알라모파이어면 이미지를 업로드하는 멀티파트매니저, 일반 리퀘스트의 CRUD를 담당하는 매니저를 각각 따로 만든다던지해서 그걸 사용하는 것이다. 물론 너무 나누면 코드 복잡도가 증가할 수 있다. swift는 프로토콜로 간결하게 명세화?처럼 한눈에 볼 수 있기 때문에 괜찮다.
* POP. swift는 POP를 지향한다고 말했다. 근데 결국은 POP는 OOP의 ISP를 기반으로 작게 분해된 인터페이스(Protocol)을 이용해서 잘게잘게 쪼개서 의존성을 낮추게 코딩하는 것을 말한다고 볼 수 있겠다.

> ### 요약 ISP 2

* asdf
* asdf
* 클라이언트(어떤 코드를 사용하는 쪽)가 자신이 이용하지 않는 메서드에 의존하지 않아야 한다.
* 무슨말?.. A라는 클래스가 B라는 프로토콜을 사용하는데, B라는 프로토콜에 정의되어있는 펑션, 프로퍼티 들을 모두 사용하는 것이 좋지않다. 그니까 이용할것만 이용해라? 뭐 그런거같은데?..
* 그냥 인터페이스를 작게 만들면 되는거 아니야 ? 아니다.

`더 자세히 보기`

* 클라이언트가 자신이 이용하지않는 메서드에 의존하지 않아야 한다.
  * 클라이언트 : 객체를 사용하는 쪽
  * 서버 : 사용되는 쪽
* 의존한다는 것은 사용한다는 것
  1. 상속을 해서 사용한다.
  2. 내부에서 해당 서버의 인스턴스를 사용한다.
* 서버는 클라이언트가 필요로하는 최소한의 인터페이스만 제공해서 둘 간의 의존도를 낮추어야 한다.

> 어떤 파일을 만들때 private, public 처럼 외부에 노출되지 않을 것들을 접근제어자로 없애주잖아. 이런것도 마찬가지로 그 파일을 사용할때 최소한의 인터페이스만 제공해서 의존도를 낮춰주려는 것이라고 볼 수 있겠다.

#### ISP: 상속을 통해 사용

* 예를들어 a프로토콜이 있고 메소드가 3개가있다. 그리고 b라는 클래스가 a를 준수한다. 근데 얘는 기능이 1개만 필요하다. 그러면 2개는 필요없는 게 되잖아?
* 그러면 상속받은 메소드를 퇴화시켜야 한다.(LSP위반)
* 즉, 덩치가 큰 프로토콜을 준수하게 되면 불필요한 메소드를 선언해야 할 가능성이 높다.
* objc같은 경우에는 프로토콜에 옵셔널이 있는데, 순수한 swift는 없다.
* 쉽게 한다면 아래의 코드처럼 1.objc를 사용하는경우, 2. 비어있는 함수로 extension구현 (swift)의 방법들이 있겠지만, 이런 경우는 swift 고유 문법이 지향하는 바와는 결이 다르다.
* 이런 경우라면 차라리 옵셔널로 선언해야 될 부분을을 `별도의 프로토콜`로 만들고 `잘게 나누어진 프로토콜`을 한꺼번에 상속받은 `하나의 프로토콜`을 만들던지 해서 모든 기능을 다쓰면 `하나의 프로토콜`을 쓰고 그렇지 않은 경우에는 `잘게 나누어진 프로토콜`을 따로따로 준수해서 쓰는 것이 훨씬 더 좋은 방법이다.

```swift
//1. 
@objc protocol CounterDataSource {
    @objc optional func increment(forCount count: Int) -> Int
    @objc optional var fixedIncrement: Int { get }
}

//2. 비어있는 함수로 extension
protocl MyProtocol: class {
    func myFunc()
}
extension MyProtocol {
    func myFunc() {
    	...
    }
}
```

#### ISP: 인스턴스를 사용하는 경우

* 직접적으로 메소드를 사용하지 않으면 크게 영향을 주지는 않음
* 컴파일시 의존관계에 의해 불필요한 컴파일 필요
* 불필요한 모듈의 업데이트 유발

> 무슨말이냐면, 프로토콜에 사용하지 않는 인스턴스가 있는 상황인거야. 그래서 음 그걸 직접적으로 사용하지 않으면 커플링이 발생한다던지는 않지만, 불필요한 컴파일이 발생할수있고 불필요한 모듈의 업데이트 유발할 수 있다.

#### ISP: 요약

* 큰 덩어리의 인터페이스들을 구체적으로 작은 단위들로 분리시켜 클라이언트를이 꼭 필요한 메소드만 사용할 수 있게 해야 하는 것.

#### ISP: String - Hashable의 예

* Swift같은 경우에는 objc보다 훨씬 더 ISP를 고려해서 설계가 되어있다. objc는 기존 프로토콜들의 덩치가 매우 컸다. 또 프로토콜 용법도 델리게이션에 치우쳐 있었다. 그런데 Swift같은 경우에는 그런 프로토콜들이 굉장히 작은 단위로 이루어지고, 그 작은 단위의 프로토콜들을 상속을 통해 조합해서 사용하는 그런 설계를 볼 수 있다.
* 대표적인 예시 중에 하나가 Hashable이다.
* 아래 스위프트 코드를 보면 String도 Hashable이다. 그런데 hash값이 만약에 필요하다면 String을 쓰기보다 Hashable을 받아서 가져오는 것이 좋다. 즉, String보다 Hashable이 더 작은 인터페이스 라는 말이다.
* 그래서 클라이언트와 String의 의존관계를 끊음.

```swift
/// String보다 Hashable이 더 작은 인터페이스

func myFunction(key: String) {
let hashValue = key.hashValue
...
}

func myFunction(key: Hashable) {
let hashValue = key.hashValue
...
}
```

```swift
public protocol Hashable : Equatable {
    var hashValue: Int { get }
    func hash(into hasher: inout Hasher)
}

extension String : Hashable {
    public func hash(into hasher: inout Hasher)
    public var hashValue: Int { get }
}
```

#### ISP: iOS의 예

* UITableViewDataSource, UITableViewDelegate가 분리되어 있다.
* Swift에서 메서드 한 두개만 제공하는 프로토콜들 (Equatable, Comprable, Hashable 등..)
* objc에서 보다 Swift에서 더욱 잘게 분해되어 있어서, 딱딱 어디 필요한데만 갖다 쓸 수 있게 자유도를 높여놨다. 그리고 ISP를 더 신경써서 설계한 것을 볼 수 있다. 쓸데없이 안쓰는 애들을 가져오지 않기 때문에.

#### 클라이언트 / 서버 양측에서의 작업

* 통상적으로 클라이언트 쪽이 서버와 강하게 커플링
  * 별도로 분리된 인터페이스를 사용하지 않고 직접 서버의 클래스를 사용하기 때문이다.
  * 서버측의 변경이 클라이언트에 강하게 영향을 미침.
* 그러면 어떻게 해야 할까?
* 작업
  1. 서버클래스를 SRP를 준수하게 잘게 분해한다.
  2. 인터페이스를 그룹지어 제공하고 그것을 클라이언트가 이용한다.
  3. 서버클래스 한덩어리로 쓰는 것과 만들어서 나눠서 쓰는것이 어떤게 더 나은지 고민해본 다음에 사용해야 하는데 통상적으로는 나누는것이 복잡해보일 수 있지만, 오히려 의존도를 낮추는 코드이다.
  4. 더군다나 스위프트에서는 protocol로 이런작업들을 간결하게 해줄 수 있기 때문에 더 좋다.

#### Protocol Oriented Programming (POP)

* POP를 제대로 하기 위해서는 ISP를 제대로 이해하고 습관화해야한다.
* 고전적인 ISP는 abstract class를 주로 이용하지만, Swift에서는 주로 protocol을 이용한다.
* ISP에 따라 작게 분해된 인터페이스(protocol)을 이용해 코딩하는 것이 POP이다.
* POP도 결국엔 OOP의 ISP에서 나온것이라고 볼 수 있겠다. 이름만 약간 바꾼거라고 해야하나.

#### 연습

* 10개의 문이있고, 터치하면 문이 열린다.
* 5초 이상 열리면 경보가 울리는 시스템 ![](https://images.velog.io/images/dev\_kickbell/post/9a65bf22-75b4-425c-8416-5997b3b3d4c3/image.png)
* 출처 : 로버트 마틴, 클린 소프트웨어의 코드를 Swift로 바꾸기

1.

> 일단 5초 제한이 있어서 그 제한이 걸리면 동작해야하는 프로토콜을 하나 만듬. ![](https://images.velog.io/images/dev\_kickbell/post/1ce964be-b5ac-4541-aae8-b987c2ad6073/image.png)

1.

> 그리고 타이머를 만들었다. import Timer는 해야겠지. start/stop이 있고, register/deregister도 있다. ![](https://images.velog.io/images/dev\_kickbell/post/c36f19d7-3cf8-4d62-bfee-99476da08c5f/image.png)

1.

> 5초를 확인하는 방법이 TimerClient에는 없기 때문에 TimerClientInfo라는 별도의 클래스를 만들었다. TimerClientInfo는 현재시간하고 등록된 시간을 빼서 타임아웃보다 크면 TimerClient에서 타임아웃 메소드를 실행하는 로직으로 되어있다. ![](https://images.velog.io/images/dev\_kickbell/post/cbdc515a-c373-42ba-8a52-4c0090621f8e/image.png)

4-1.

> 타이머와 아무상관없는 도어라는 인터페이스가 있다. ![](https://images.velog.io/images/dev\_kickbell/post/33bcdc8a-f83e-4696-aeb9-1375342cbcf4/image.png)

4-2.

> 만약 타이머 기능이 없는 도어가 없다는 가정하에 생각없이 구현한다고 치고 TimerClient 를 상속하면 timeout이라는 메소드를 구현해줘야 되겠지? 그러면 `인터페이스가 오염`된 상황이 된다. 구현할 필요가 없는 timeout을 구현해야 하기 때문. ![](https://images.velog.io/images/dev\_kickbell/post/2c479cde-6162-48a5-90d4-1966730bf767/image.png)

1.

> 4-2를 했다는 상태에서 이제 TimeDoor라는 클래스를 만들어주었다. Door를 준수하는. delegate같은 경우에는 문상태가 바뀔때마다 (오픈/닫힘/알람온) 델리게이션 해주는 애일것이고, Door를 준수하기 때문에 timeout메소드 까지 구현해줬고 timeout이 되면 알람을 켜주는 그런 동작까지 하게 된다. ![](https://images.velog.io/images/dev\_kickbell/post/314623a4-2f07-4d3c-92f2-34d7426f6d80/image.png)

#### 문제점

* Timer를 사용하지 않는 Door를 만들려고해도 TimerClient 인터페이스에 종속( Door 자체가 TimerClient를 상속받아서 만들어졌기 때문에)
* timeout 메소드를 퇴화 (LSP위반), 즉 좋은 설계가 아니다.
* TimerClient - Door - TimedDoor: 상속관계로 강한 커플링
* TimedDoor가 TimerClient의 변경에 영향을 끼칠 가능성이 높음
  * TimerClient를 사용하는 다른 클래스들도 영향
  * 인터페이스의 변화는 서버보다 클라이언트에서 유발될 확률이 높음. (중요한 포인트이다.)
  * 결국은 서버는 클라이언트가 사용하는 기능을 제공해주는 것이고, 무슨 기능이 필요하면 새로운 인터페이스를 서버에 요청하게 된다. 서버는 수동적으로 클라이언트의 요청에 따라 인터페이스를 추가해야 하는 상황이된다.
  * TimedDoor or TimerClient의 필요에 따라서 그 자신도 수정해야만 하는 상황을 가지게 될 수 있다.
  * 뭔말인지 모르겠지만 대충알겠기도하다.

#### 개선

1. TimedDoor와 일반 Door의 커플링 제거

> 이렇게 되면 이 도어는 TimerClient와는 아무 상관없이 순수하게 도어의 역할만 하게 된다. ![](https://images.velog.io/images/dev\_kickbell/post/0d8aedba-d3e4-45fa-bae8-efd557848e0e/image.png)

2-1, 2-2중에 택1

2-1. Adapter Pattern 을 사용하는 방법

> 차이점이 타임아웃에 대한 어떤 정보를 TimerdDoor가 직접받지 않는다. 그걸 DoorTimeAdapter를 통해서 받는다. DoorTimeAdapter가 하나 끼기는 했지만 의존성이 떨어지게 된다. 그리고 TimerdDoor에는 익스텐션을 하나 생성해서 TimerdDoor를 접근할때 생성시키게 해주고, 타임아웃이 불렸을 때의 동작을 doortimertimeout을 통해 해주게 된다.

![](https://images.velog.io/images/dev\_kickbell/post/55a278d4-a144-4b18-a510-d542aed6292c/image.png) ![](https://images.velog.io/images/dev\_kickbell/post/9c84d891-42af-406e-b02e-53b281239838/image.png) ![](https://images.velog.io/images/dev\_kickbell/post/c7e27671-9139-47fa-a734-c927b93e052e/image.png)

2-2. TimerDoor가 Door, TimerClient를 같이 상속 (이경우에는 코드가 몇없기때문에 2-2가 더쉽다.)

> 두번째 방법. 더 많이 사용되긴 한다고 한다. Door가 TimerClient를 상속받는게 아니라 TimerDoor가 Door, TimerClient를 같이 받는다. 그리고 TimerDoor가 timeout을 직접 구현한다. 어댑터 보다 더 단순해진다.

![](https://images.velog.io/images/dev\_kickbell/post/1df4a334-6120-4105-ae8b-190a589af08d/image.png)

#### 정리

* 인터페이스를 명확한 목적별로 잘게 나누고 인터페이스 기준으로 코딩
* POP와 같은 이야기
* 구현과 인터페이스를 항상 분리하는 것이 최선은 아님
* 목적별로 인터페이스를 분리해서 사용하면 좋은 점
  * 클래스의 인터페이스가 비대한 경우
  * 클래스간의 직접적인 의존성 제거
