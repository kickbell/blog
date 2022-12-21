# Understanding Swift Performance

> _이 글은 [WWDC 2016 - Understanding Swift Performance](https://developer.apple.com/videos/play/wwdc2016/416/
)의 내용을 공부하고, 개인적으로 정리해놓은 글입니다._


## 1. Stack 할당과 Heap 할당 

### Stack 할당
- 처음에는 스택 할당과 힙 할당에 차이에 대해서 설명합니다. 익히 아는대로 값타입은 스택에 저장되고 스택은 단순한 자료구조이지요. 컴파일 타임에 이미 스택 메모리에 공간이 할당되고, 런타임에 인스턴스를 생성할 때는 이미 할당된 공간에 값을 초기화만 해주면 되지요. 
- 그리고 point1, point2는 독립적이기 때문에 point2가 변경되어도 point1에는 영향이 없고, 둘 다 사용이 끝나면 메모리에서 해제되죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/f5b22af7-16ec-46a1-860c-01c1363d67a4/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/8bf64e69-d034-4494-b3c3-7f9f619b9b3f/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/ee44fd5f-41d0-48a6-a3b4-409afc4e581d/image.png)


### Heap 할당
- 힙 할당은 조금 더 복잡합니다. 컴파일 타임에는 point1, point2의 참조를 위해서 스택에 메모리를 할당하죠. 
- 그리고 런타임이 되면, Swift는 힙을 잠그고 적당한 크기의 사용되지 않은 메모리 블록을 검색합니다. 왜냐면, 여러 스레드가 동시에 힙에 메모리를 할당할 수 있기 때문이죠. 그리고나서 빈 공간을 찾는데 성공하면 메모리에 값을 저장합니다. 
- point2 = point1를 하게되면 힙에 있는 동일한 인스턴스를 참조하죠. 그래서 point2 = 5 와 같은 작업을 하면 의도치 않은 공유를 일으킬 수 있습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/a9ec667e-a140-478e-9512-c90ff54107a4/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/360a6592-0b27-4e23-8a80-a8fdbc5b2893/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/f54549ff-78db-4763-b904-5ee4a69ee937/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/836825ef-d800-4f9c-a66a-97032010feb7/image.png)


### 결론 

- 결론적으로는 클래스는 힙 할당이 필요하고, 빈 공간을 찾아야 하고 그동안 힙을 잠궈서 무결성을 유지해야 하는 등의 부가적인 작업이 필요합니다. 의도치 않은 상태 공유를 일으킬 수도 있죠. 
- 반면에 스택은 힙을 사용하지 않으므로 더 빠르지요. 값을 복사하므로 의도치 않은 상태 공유에 취약하지도 않습니다. 앱을 디자인하는데 추상화의 이런 특성이 필요하지 않은 경우라면 값 타입인 구조체를 사용하는 것이 더 좋습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/102d6118-8826-4885-9d80-1c195651acd7/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/19e0cbfd-970b-4dd9-bedb-e6dbbab8ec33/image.png)


## 2. 코드 예시를 통한 Struct의 사용 

### Key 값으로 String을 사용(문자열을 힙에 저장, 힙 할당) 
- 말풍선을 만들겁니다. 대충 이런 모양 💬, 💭 이겠죠? 그런데 당연히 반대쪽 모양도 있을겁니다. 그래서 Orientaion이 있고, 내가 보낸 말풍선의 색은 달랐으면 좋겠으니 Color도 있습니다. 또, 맨 처음 채팅만 말풍선 꼬리가 있어야 해서 none과 말풍선 꼬리 2종류가 추가된 Tail도 있습니다.
- 그리고 makeBalloon 메소드를 생성했어요. 그런데, 이 말풍선은 채팅방에서 스크롤하면서 수없이 많이 호출됩니다. 그래서 매우 빨라야 해요. 그래서 var cache 를 추가했습니다. 한번만 생성하면 그 이후부터는 키값에 따라서 바로 꺼낼 수 있게 말이죠. 
- 그런데 문제가 있습니다. 뭘까요? 바로 key값인 String 입니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/18318bf3-72b4-42b8-ba2c-dabc537bd472/image.png)

### key 값으로 Enum, Struct를 사용(힙 할당 사용하지 않음) 
- 왜 String이 문제일까요 ? Swift에서 String은 구조체니까 값타입¹이죠? 그러면 힙을 사용하지 않고 스택만 사용하겠네요? 그럴 것 같지만 그렇지도 않다고 합니다. 
- 왜냐면, 저 key를 보면 알 수 있지만 key의 값이 가변적이죠. 길이가 1자일지 1000자일지 모르는 겁니다. 컴파일 타입에 길이가 정해져 있지 않죠. 그래서 Swift에서 String은 힙을 간접적으로 이용한다고 합니다. 정확히는 문자열 값을 힙에 저장한다고 해요. 이것은 Swift의 컬렉션 타입인 Array, Dictionary 같은 녀석들도 마찬가지라고 해요. 
- 그러면 이 코드를 어떻게 개선하면 좋을까요 ? 힙을 사용하지 않게 하면 될 것 같아요. 
- 해결책은 아래와 같습니다. Attribute라는 구조체를 만듭니다. 그리고 Hashable을 준수합니다. Swift에서 구조체는 일급 객체이므로 key값으로 사용할 수가 있습니다. 이렇게 하면 힙 할당이 필요 없어지므로 더 효율적인 코드가 됩니다. 
- 간접적으로 힙 할당을 사용했던 String을 제거함으로써 스택에 할당할 수 있는 것이죠. 훨씬 더 안전하고 더 빠릅니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/a12f9ed5-ea1c-404c-a8ee-d3fb2088ccfb/image.png)



## 3. Struct의 Reference Count 

### 참조를 포함하는 Struct은 어떨까요 ? 
- Swift에서는 ARC가 자동으로 refCount를 처리해주죠. 인스턴스를 사용하게 되면 refCount를 1씩 올려주고, 사용이 끝나면 1씩 내려줘서 결국에 0이되면 메모리에서 해제됩니다. 
- 중요한 것은, 이것이 매우 빈번하고 실제로는 정수를 증가/감소 시키는 것보다 더 많은 작업이 있다는 거에요. 첫째로는 실행하는 것과 관련된 몇 가지 간접 참조가 있다는 것이고 둘째로는 힙 할당과 마찬가지로 동시에 여러 스레드의 힙 인스턴스에 참조를 추가/제거 할 수 있기 때문에 thread safety를 고려해야 한다는 겁니다. 
- struct는 refCount가 없을까요? 그렇지는 않습니다. String과 UIFont가 포함된 아래의 코드를 보죠. String은 위에서 말했던 것처럼 실제로 해당 문자의 내용을 힙에 저장한다고 했죠? 그래서 refCount가 필요합니다. UIFont는 클래스니까 당연히 필요하겠죠. 
- 처음에 label1에 2개의 참조가 있습니다. 그리고 let label2라는 사본을 만들 때 실제로 2개의 참조를 더 추가합니다. 하나는 text storage에 하나는 font에 추가하죠. 이런 힙 할당은 refCount의 retain/release에 대한 호출을 추가하는 것입니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/8071e1fe-ba13-4265-bb54-d32ab9db5c90/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/109ecd48-cd5b-4d86-ab7c-db6dfe68666c/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/213e7493-008c-4457-8fb6-72496027d275/image.png)


### 참조를 2개 이상 포함하는 Struct의 경우 Class보다 좋지 않습니다. 
- 결론적으로 클래스였으면 reference counting overhaed가 2개면 됐을건데, struct는 label1과 label2이 독립적인 인스턴스이므로 reference counting overhaed가 4개가 발생되어 버립니다. 
- 실제로 구조체는 포함된 참조 수에 비례하는 reference counting overhaed를 지불하게 되는 거죠. 2개 이상의 참조를 포함하는 구조체의 경우 오히려 클래스보다 효율이 좋지 않습니다. 
- 클래스의 경우 
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/b2eee608-3a8a-4216-a21b-9494379ff48f/image.png)
- 구조체의 경우 
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/9d4588c5-7da9-4a30-ab0b-a39e31a93240/image.png)
- 참조가 포함된 구조체의 경우
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/2a698d38-5bb1-4247-9141-13e014039f55/image.png)
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/e26193c4-1043-4b23-8629-7376f804fc15/image.png)



## 4. 참조가 포함된 Struct의 성능 개선

### uuid, mimeType을 String으로 사용 
- 참조가 포함된 struct의 성능 개선을 진행해볼게요. 일단 아까 채팅방만들던 것을 예시로 이어가 보겠습니다. 채팅창에 텍스트말고 png, jpeg, gif 같은 것들도 보내고 싶습니다. 그러기 위해서 Attachment라는 struct를 생성했어요.
- Attachment에는 첨부파일에 대한 디스크 경로를 저장하는 fileURL이 있고, 무작위로 생성된 고유 식별자를 위해서 uuid라는 String 프로퍼티가 있고, jpeg/png/gif같은 타입을 나타내는 mimeType이 있습니다. 우리 채팅방은 mp3, mov같은 타입은 지원하지 않기 때문에 failable initializer로 작성되었습니다. 타입을 지원하면 초기화, 아니면 실패인거죠. 
- 하지만 문제는 계속 말했던 것처럼 3가지 타입 전부 힙 할당을 하고 있습니다. 정확한 표현으로는 참조 카운팅 오버헤드를 발생시킨다고 할 수 있곘지요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/f3c2bf68-03d4-48a6-9571-7ee9bf685de1/image.png)


### uuid, mimeType의 최적화 
- iOS는 6.0부터 struct UUID라는 타입이 새로 생겼습니다. 보시다시피 구조체죠. String을 UUID로 바꿔주면 힙 할당을 막을 수 있습니다. UUID는 128비트를 구조체에 직접 저장합니다. 
- mimeType을 enum으로 변경합니다. enum 또한 값타입이죠. String extension을 없애고, enum과 고유 기능인 rawValue를 통해 코드를 수정해줍니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/115dd65f-56b2-4577-9088-01110ee9ae62/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/c82514d6-950b-4f86-bffd-b0b5b7e4f1d5/image.png)


### HTTPRequest의 성능 개선
- 성능 개선 전이라면 HTTPRequest을 copy할 때마다 9개의 레퍼런스 카운팅 오버헤드가 발생합니다. 
- 이것을 enum, struct로 개선하면서 2개로 줄일 수 있게 됩니다.
```swift
//성능 개선 전 
struct HTTPRequest {
  var protocol: String //1
  var domain: String //2
  var path: String //3
  var filename: String //4
  var extension: String //5
  var query: [String: String] //6
  var httpMethod: String //7
  var httpVersion: String //8
  var httpHost: String //9
}

//성능 개선 후
enum HTTPMethod {
	case Get, Post, Put, Delete
}
enum HTTPVersion {
	case _1_0, _1_1
}
struct HTTPRequest {
  var url: NSURL //1
  var httpMethod: HTTPMethod
  var httpVersion: HTTPVersion
  var httpHost: String //2
}
```

### 결론
- Attachment는 더 안전해졌습니다. URL은 어쩔 수 없지만 1개이고, 적어도 uuid와 mimeType이 힙 할당될 필요가 없기 때문에 참조 카운트 오버헤드가 발생하지 않습니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/6ab89739-d4c7-4a5a-9a60-995a8a365184/image.png)


## 5. Method Dispatch
메서드 디스패치는 프로그램이 메서드를 호출할 때 실행할 명령을 선택하는 방법입니다. 더 세분화된 분류로 objc에서는 Dynamic Dispatch가 메시지를 보내는 방법으로 Message Dispatch가 있다고는 하지만, 여기서는 Static과 Dynamic으로만 설명하겠습니다. 

### Static Dispatch(정적 디스패치)
- 값 타입과 참조 타입 둘 다 지원됩니다. 
- 컴파일 시점에 호출되는 메서드가 결정되는 Method Dispatch 입니다. 컴파일 시점에 무슨 호출이 일어날 지 알고 있기 때문에 Static(정적) 디스패치라고 불립니다. 
- 정확히는 컴파일 하는 시점에 컴파일러가 어떤 구현이 실행될 지 정확히 알고있다면, 해당 메소드를 실행하지 않고 그 구현을 붙여넣기 해버리는 겁니다. 
- 그런 과정을 `메소드 인라이닝(Inlining)`이라고 하는데, 예시를 들어보면 아래와 같은 느낌입니다. 
- 아래 그림이 3장이 있는데 설명하자면, 우리는 drawPoint(point)를 실행하지요. 그런데 얘는 매개변수로 point 인스턴스를 받고 내부를 보면 param.draw()는 즉 point.draw()죠? 
- 그러면 컴파일러는 알 수 있죠. drawPoint(point) -> point.draw() 이구나. 그리고 point.draw()는 결국에 struct Point안에 있는 draw() 메소드를 호출하는 거죠? 
- 결론적으로 func draw() 안의 //Point.draw implementation으로 drawPoint(point)를 대체할 수 있다는 겁니다. 이게 메소드 인라이닝(Inlining)이에요. 
- 만약 메소드 인라이닝을 하지 않았다면 drawPoint(point) -> param.draw() ->  //Point.draw implementation 으로 총 3단계 stack call이 필요했을거에요. 하지만 컴파일러가 알고 있죠? 어떤 부분에서 어떤 코드가 실행될지. 그래서 //Point.draw implementation를 붙여넣기 해버리니까 stack call을 1번만 하는겁니다. stack call에 대한 overhead 2단계가 줄어들게 된거죠. 
- 그래서 상대적으로 dynamic dispatch에 비해 비용이 덜 들고, 컴파일이 최적화되어 빠릅니다. 반복문안에서 호출된다면 비용차이가 더 커지겠지요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/fe0cff49-e14d-4694-841d-fdd1b0bc0afd/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/c0c5ec20-02c7-46f8-b8ca-c9f0d27a0159/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/dc07cc2b-368e-4da9-bbd0-4a901241b1ec/image.png)


### Dynamic Dispatch(동적 디스패치)
- 참조 타입에서만 지원됩니다. 주로 class지요. 
- 런타임 시점에 호출되는 메서드가 결정되는 Method Dispatch 입니다. 컴파일 시점에 무슨 호출이 일어날 지 알 수 없고, 런타임 시점에 무슨 호출이 일어날 지 알 수 있기 때문에 Dynamic(동적) 디스패치라고 불립니다. 
- 왜 컴파일 시점에 무슨 호출이 일어날 지 알 수 없을까요? class가 상속을 통해 다형성(Polymorphism)을 지원하기 때문입니다. 
- 아래의 코드를 보죠. class Drawable이 있습니다. 그리고 Point와 Line은 Drawable을 상속하고 draw()를 오버라이드 하고 있죠. 그리고 drawables 라는 [Drawable] 배열을 가지고 for문 내부에서 draw() 메소드를 호출합니다. 
- 컴파일러는 저 draw()가 Point의 draw()인지 Line의 draw()인지 알 수 있을까요? 네 알 수 없죠. 컴파일 타임에는 알 수가 없습니다. 실제 런타임이 되어서 특정 버튼을 click하던가 해서 어느 메소드를 실행한 것인지 알 수 있기 전까지는 말이죠. 변수의 개수가 다르다고는 하지만 class라 stack에는 주소값만을 갖기 때문에 d[0]과 d[1]의 크기도 똑같죠. 
- 런타임에 어떤 녀석을 눌렀는지 알았다면 그 이후에는 어떻게 호출할까요? 예를 들어 Line을 선택했다고 해보죠. 이후 작업이 어떻게 되냐면, Line이든 Point든 파란색으로 type:이라는 부분이 있죠? 거기에 해당 class에 대한 메타데이터를 갖고 있습니다. 그래서 Line을 탭했다고 하면 type: 에서 정보를 갖고 옵니다. 거기에는 v-table(virtual method table) 이라는 녀석이 있어요. 바로 그곳에서 Line의 draw() 메소드가 어느 주소에 있는지를 찾고 그것을 실행하는 것이죠. 
- 결론적으로는 실제 무슨 타입을 호출하는지는 컴파일 시점에 알 수가 없다. 런타임 시점에 알 수 있다. 그래서 static 디스패치처럼 컴파일 최적화를 할 수가 없다는 겁니다.(참고로, 힙에 여러 쓰레드가 접근하는 thread safety 문제는 없다고 합니다.) 
- 그렇지만, 항상 class는 dynamic dispatch만 사용해야 되는 것은 아닙니다. 위에서도 static dispatch는 값, 참조 타입 모두를 지원한다고 적어두었는데요. class 앞에 얘는 상속을 사용하지 않을 것이다는 final 키워드를 쓴다거나 private, fileprivate같은 접근 제어자를 사용해서 상속을 사용하지 않을 것임을 표시해둔다면 컴파일러는 해당 클래스가 static dispatch를 사용한다는 것을 알고 static dispatch로 전환하게 됩니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/383b90ef-409e-4c0e-b67e-1b676bb9a97d/image.png)

### 결론 
- struct는 static dispatch를 사용한다. 메소드 인라이닝을 사용해서 컴파일러가 최적화 할 수 있다.
- class는 기본적으로 dynaimic dispatch를 사용한다. 하지만 final, private를 사용한다면 static dispatch로 전환된다.
- class에서 다형성을 사용한다면 어떤 특정 타입을 호출하는지는 컴파일 시점에 알 수 없으므로, 런타임에서 v-table의 정보를 조회해서 실행하는 방식이다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/8da64186-a4f9-4d98-86bd-4916c12b0778/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/6baba9bf-0cc9-41e6-baf1-0805534b3dd5/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/d81f6e19-c586-4f16-ada0-decc4d48fc6f/image.png)


## 6. Protocol의 값 타입의 다형성
- 프로토콜 타입을 보겠습니다. objc에서의 protocol은 선언만하고 구현은 하지않는 java의 interface와 같은 느낌이었지만, swift에서의 가장 큰 특징은 value type인 struct에도 protocol이 적용이 가능하다는 점입니다.
- 아래의 코드를 보면, 위에 예시와는 class에서 protocol, struct인 것만 다른데요. 얘도 마찬가지로 다형성을 지원하죠. 그러면 여기서 의문이 생깁니다. 
- 위의 Dynamic Dispatch의 그림 예시를 보면 쟤는 클래스죠? 그래서 어차피 스택에는 주소값만 가지고 있으니 d[0]과 d[1]의 크기가 같지만, 얘는 구조체잖아요? 그러면 변수의 개수가 2개와 4개로 다른데 힙 할당도 쓰지 않는데 어떻게 메모리를 할당할까요? Drawable이 뭔지 알고? 
- 두번째 의문은 class의 다형성에서는 v-table을 통해서 draw()를 호출했는데 protocol에서는 어떻게 draw()를 호출할까? 입니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/5285dad8-4ee3-4777-b95f-efec46974fc3/image.png)

### Existential Container
- 먼저 `struct에서 변수의 개수가 다른데 어떻게 메모리를 할당하는지`에 대한 대답은 existential container를 사용하는 것입니다. 
- 프로토콜 타입에서만 사용되는 컨테이너이구요 struct입니다. 아래 그림을 보면 알 수 있지만, 3 words 크기의 버퍼를 가지고 있습니다. 1 word는 64bit CPU는 8byte(64bit), 32bit CPU는 4byte(32bit)의 크기를 가지고 있어요. 나머지 공간에는 아래에 나올건데 vwt, pwt같은 메타데이터가 있습니다.
- 그래서 넣으려는 데이터가 3words 이하면 valueBuffer에 그대로 저장합니다. 끝이죠. 반면에 3words 이상이라면 힙 할당을 합니다. 
- 그러면 이제 데이터 크기에 따라서 힙 할당을 하고 안하고는 알겠는데, 얘를 관리를 어떻게 하느냐? 하면 관리하는 애가 따로 있습니다. 바로 Value Witness Table(VWT)입니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/b1eb1da8-730e-4d28-91a9-d5cdf66f7506/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/3533fcb8-a2a6-4f86-8a19-8d19a89427fe/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/10a3d62b-1094-4db3-bb16-0b18cc940def/image.png)

### Value Witness Table(VWT)
- vwt는 existential container의 생성과 해제를 담당하는 인터페이스에요. 4종류의 메소드가 있는데, vwt에 따라서 LineVWT라는 녀석이 구현되어 있겠죠? 
- 그러면 처음에 allocate 합니다. 메모리에 올리는거죠. 근데 3words 이상이네? 그래서 힙을 잡아놓습니다. 그리고 다음에 넣어야 할 데이터를 copy하죠. 그러다가 사용이 끝나고 없애려면 destruct하고, 메모리에서 해제하기 위해서 deallocate 하는거죠.
- 만약에 LineVWT가 아니라 PointVWT라면 힙 할당만 없는 것 빼고는 나머지는 똑같겠지요. 
- 그런데 vwt는 어디 있나요? 네. 그건 위에서도 살짝 말했지만, existential container가 가지고 있습니다. 그래서 복사가 되더라도 계속 vwt 데이터를 가져갈 수 있게 되어 있습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/3afb82d2-6917-4f15-9d69-6c247796a00d/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/9a903053-0cc8-4229-abb9-80a6f8fa3a9a/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/4e2e2f8b-b250-43a7-8d05-93a9772ba4b8/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/a2225c6f-b42f-4c82-b731-48ea2483804d/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/7576ece5-eed1-4c26-895d-11300ae836f7/image.png)


### Protocol Witness Table(PWT)
- 이제 두번째 질문입니다. 두번째 질문은 `class의 다형성에서는 v-table을 통해서 draw()를 호출했는데 protocol에서는 어떻게 draw()를 호출할까?` 였죠? 그 답은 pwt 입니다. 
- 얘도 v-table 비슷한 애라고 생각하시면 됩니다. 그리고 또 이친구도 existential container의 3 words를 제외한 메타데이터 영역에 들어가 있어요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/f5eb41ee-fc93-46e2-a63d-272229735ffa/image.png)


### 3 Words가 넘는 값의 Protocol Type Copy(Indirect Storage)
- 그러면 3words가 넘는 값을 copy 한다면 어떻게 될까요? 얘는 struct니까 reference counting을 하지 않잖아요 ? 
- 이게 또 꽤나 재밌게 됩니다. 아래의 사진을 보면 알 수 있겠지만, pair.second = Line()을 했죠. 근데 그러면 힙 전체가 복사가 되버려요. 큰 비용이 들겠죠. 그런데, 두번째 사진처럼 pair를 카피하면? 총 힙 할당이 4개가 되는겁니다. copy, copy... 에 따라서 비용이 엄청 크게 증가하는 거죠. 
- 이거 해결 방법이 없을까요? 있습니다. 바로 Line()을 struct가 아니라 class로 만드는 겁니다. 그렇게 되면, 클래스는 reference semantic 이므로 3번째 그림처럼 참조만 하고 있고 refCount만 +=1 증가했을 겁니다. 
- 코드로 된 예시는 밑에 copy on write의 2번째, Indirect Storage With Copy-on-Write를 보면 알 수 있는데요. class LineStorage에서 변수 4개를 가지고, 그걸 struct인 Line이 인스턴스로 갖고 있죠. 그래서 이런 것을 Indirect Storage 라고 합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/c1cbe6ec-7a64-4735-bf2d-626d49dc4fc3/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/5856ea5b-ccbe-4db1-935f-13070f80d3a2/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/4b26cbe5-aa0e-4d01-8f7f-913ef815345b/image.png)

### Copy on Write
- 끝난 것 같죠? 하지만 그렇지 않습니다. 생각해보시면 문제가 또 있죠. 만약에 second.x1의 값을 3.0으로 바꾸면 어떻게 될까요? 당연히 같은 곳을 가리키고 있으니 값이 둘 다 바뀌겠죠? 근데 그게 정상 동작인가요? Pair는 struct 잖아요. 이런 문제, 정확하게는 의도하지 않은 상태 공유를 일으키는 문제를 해결하기 위한 방법이 Copy on Write 입니다. 
- 생각보다 어렵지는 않습니다. 뭐냐면, copy를 하기전에 refCount를 확인합니다. 두번째 사진을 보면 알 수 있는데요. isUniquelyReferencedNonObjc의 역할은 refCount를 확인하는 겁니다. 그리고 refCount가 1이상이면 LineStorage를 새로 생성해버리는 거죠.
- 그러면 세번째 사진처럼 우리가 원래 의도했던 대로 retCount가 각각 1인 애가 2개 생기곘죠. x1의 값은 하나만 바뀌구요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/ca5912c3-76a9-41ff-95df-44096ba23a2c/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/85ecb3e0-d5b3-4d52-90a2-7f83aacd1b5f/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/e76224ec-50ae-48fd-aa34-a9840bc874b4/image.png)


### 결론
- 3 words 이하의 protocol type은 메모리 할당에 stack만 사용한다. 힙 할당도 하지 않으니 reference counting도 사용하지 않는다. method dispatch는 pwt를 기반으로 한 dynamic dispatch를 사용한다. 
- 3 words 이상의 큰 값의 protocol type은 힙 할당을 사용한다. struct이므로 reference counting을 사용하진 않지만(class 프로퍼티가 있으면 사용하겠지), copy를 하게 되면 힙도 같이 복사되어버리므로 큰 비용이 발생한다. method dispatch는 pwt를 기반으로 한 dynamic dispatch를 사용한다. 
- 3 words 이상의 큰 값의 protocol type에서 Indirect Storage를 사용해서 코드를 개선하게 되면 두번재 사진처럼 힙 할당, reference counting, dynamic dispatch이 class를 사용할 때의 수준 정도로 맞춰지게 됩니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/fc20d7a7-0fe3-4b00-9505-179f43ccaa40/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/847ca757-30d1-4e1b-93db-acb33fd5757a/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/d9bce7ad-b3ee-4d57-aeb0-9141d42da29a/image.png)



## 7. Generic Type

### Static Polymorphism(정적 다형성) 
- 제네릭에 관해서 마지막으로 얘기해보려고 합니다. 첫번째 사진은 기존에 우리가 구현했던 프로토콜을 사용하는 방법이죠. 그리고 두번째 사진에 T라는 제네릭을 사용하고 Drawable이라는 제약을 줍니다. 그러면 첫번째 사진과 다르지 않죠? 
- 그런데, 약간 다른 부분이 있습니다. 함수의 매개변수는 값이 바뀌지 않잖아요? 세번째 사진을 예시로 하면 local: T 를 뜻하는거죠. 그래서 foo의 매개변수가 Point면 그걸 그대로 체이닝해가면서 bar에서도 Point인 겁니다. 제네릭이니까 다형성이긴 한데, 이게 함수 내에서는 변하지 않으니까 이걸 Static Polymorphism(정적 다형성) 이라고 합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/0a518f23-9f6a-460e-b258-ea585d67c8bc/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/dfbf805d-c282-4f12-8588-3ecfe070d856/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/8af55c04-11c2-47c0-8ba2-77567b1d69b4/image.png)


### Specialization(특수화)
- 정적 다형성을 알았으니 다음으로 넘어갈 수 있는데요. 제네릭이 정적 다형성이라는 기능을 지원하기 때문에 코드를 최적화를 할 수가 있어요. 무슨말이냐면, 위에 사진에서 drawACopy라는 메소드를 호출할 때, 컴파일러는 Point인지 Line인지 알 수 있나요? 네 알수있죠. 매개변수로 Point를 넣었으니까요. 그리고 쭉쭉이어서 위에 bar도 정적 다형성이라 Point가 올걸 아니까 같이 알 수 있겠죠. 그러면 컴파일러가 뭐가 올지 아니까 최적화를 할 수 있겠네요? 네 그래서 static dispatch가 사용이 됩니다. 
- 실제로는 아래의 사진처럼 컴파일러가 메소드를 만들어 준다고 합니다. 저희는 못보지만요. 이런 것을 제네릭의 특수화(Specialization)라고 합니다. 그래서 저런 경우에는 매개변수에 프로토콜 타입을 지정하는 것보다 꼭 제네릭을 사용하는게 퍼포먼스적으로 더 낫겠죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/ae47f02b-20fd-40ff-9833-c33b4ed7c43c/image.png)


### WMO(Whole Module Optimization) 
- 그런데 또 저 특수화의 기본 조건이 타입 추론이 가능해야 한 거겠죠? 그래서 아래 사진을 보면 알 수 있겠지만, Point.swift에는 Point가 구현되어있고 UsePoint.swift에서 Point를 사용하죠. 
- 근데 Swift는 기본적으로 파일별로, 즉 .swift별로 컴파일을 하잖아요? 그래서 저렇게 파일이 나뉘어있으면 특수화가 잘 안된다고 합니다. 그래서 이런 경우에는 아예 한파일에 두시거나 아니면 WMO라는 기능을 사용하시면 됩니다. 
- 그러면 두번째 사진처럼 두 파일을 하나의 단위로 컴파일하게되고 특수화가 발생할 수 있겠습니다.
- 사용하는 방법은 xcode에서 -whole-module-optimization 플래그를 통해 켤 수 있습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/c52f9ede-37f0-4554-9045-11a9e6c599a8/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/8f2e70eb-3331-4d55-8cff-97833bdcd472/image.png)


### 결론 
- 특수화 된 제네릭, struct는 힙 할당 사용안함, 레퍼런스 카운팅 없음, static dispatch 사용으로 제일 성능이 좋다. 
- 특수화 된 제네릭, class는 힙 할당 사용, 레퍼런스 카운팅 있음, v-table을 통한 dynamic dispatch를 사용합니다.
- 특수화 되지 않은 제네릭, 3 words 이하의 작은 값은 힙 할당 사용안함, 레퍼런스 카운팅도 없음, pwt를 통한 dynamic dispatch를 사용한다. 위에서 다 적진 않았습니다만 작은 값 같은 경우에는 existential container를 따로 만들지 않고 valueBuffer 3words를 사용합니다.  
- 특수화 되지 않은 제네릭, 3 words 이상의 큰 값은 힙 할당 많음, pwt를 통한 dynamic dispatch를 사용합니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/c9391fd4-20c3-4f48-b80c-7a2d7549e6a1/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/05820cfc-b291-4cf5-bfe3-83d1f6e65959/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/1898ce73-1753-428e-8d2c-be459dbe9e9d/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/01611bd2-a590-4276-9a2b-93ab90a1e016/image.png)


## 8. 정리하기 
- value, reference, protocol type 의 성격을 잘 고려해서 사용해야 한다. 
- struct에 class 타입의 프로퍼티가 많다면 최대한 enum, struct 등의 value 타입으로 대체해서 reference counting overhead를 줄여야 한다. 
- class 에서 상속을 하지 않을거면 final, private을 꼭 사용해서 static dispatch가 될 수 있도록 하자. 
- 정적 다형성이 가능한 경우는 generic, 동적 다형성이 필요한 경우는 protocol을 사용하자. 
- protocol type을 사용할 때, 대상이 큰 struct이면 indirect storage로 변경하고 mutable 해야 한다면 copy-on-write를 구현하는 것이 좋다. 
- 무조건 성능 최적화를 해야 되는 것은 아니다. 일반 앱에서는 딱히 체감이 되지 않을 수 있고 기존에 구현되어 있는 디자인 패턴이 깨질 수도 있다. 하지만, 렌더링 관련 로직(이미지가 있는 셀에서의 스크롤링)이라던지 서버 환경에서의 대용량 데이터 처리 같은 경우에는 고려할 만 하며, 알고 안하는 것과 모르는 데 안하는 것은 다르므로 옳은 방향으로 가는 것이 중요하다. 



## 9. 코드 성능을 개선하기 위한 질문 3가지 
- 내 인스턴스가 Stack에 할당되는지, Heap에 할당되는지?
- 내가 인스턴스를 전달 할 때, 얼마나 많은 레퍼런스 카운팅 오버헤드가 일어나는지?
- 내가 내 인스턴스의 메소드를 호출 할때, 그게 static dispatch를 통해 일어나는지, dynamic dispatch를 통해 일어나는지?


## Reference

[https://developer.apple.com/videos/play/wwdc2016/416/](https://developer.apple.com/videos/play/wwdc2016/416/)

[https://www.youtube.com/watch?v=z1Gf6EosaUQ](https://www.youtube.com/watch?v=z1Gf6EosaUQ)


## Endnotes 

### ¹swift 종류별 값, 참조타입
- 참조 reference 타입
    - class
    - function
    - closure
- 값 value 타입
   - struct 
   - enum 
   - tuple 
   - public struct Int
   - public struct Bool
   - public struct Float
   - public struct Double
   - public struct Character
   - public struct String
   - public struct Dictionary<Key : Hashable, Value>
   - public struct Array<Element>
   - public struct Set<Element : Hashable>  




