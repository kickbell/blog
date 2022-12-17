# Understanding Swift Performance

> _이 글은 [WWDC 2016 - Understanding Swift Performance](https://developer.apple.com/videos/play/wwdc2016/416/
)의 내용을 공부하고, 개인적으로 정리해놓은 글입니다._

## 1. stack 할당과 heap 할당 

### stack 할당
- 처음에는 스택 할당과 힙 할당에 차이에 대해서 설명합니다. 익히 아는대로 값타입은 스택에 저장되고 스택은 단순한 자료구조이지요. 컴파일 타임에 이미 스택 메모리에 공간이 할당되고, 런타임에 인스턴스를 생성할 때는 이미 할당된 공간에 값을 초기화만 해주면 되지요. 
- 그리고 point1, point2는 독립적이기 때문에 point2가 변경되어도 point1에는 영향이 없고, 둘 다 사용이 끝나면 메모리에서 해제되죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/f5b22af7-16ec-46a1-860c-01c1363d67a4/image.png)
![](https://velog.velcdn.com/images/dev_kickbell/post/8bf64e69-d034-4494-b3c3-7f9f619b9b3f/image.png)
![](https://velog.velcdn.com/images/dev_kickbell/post/ee44fd5f-41d0-48a6-a3b4-409afc4e581d/image.png)


### heap 할당
- 힙 할당은 조금 더 복잡합니다. 컴파일 타임에는 point1, point2의 참조를 위해서 스택에 메모리를 할당하죠. 
- 그리고 런타임이 되면, Swift는 힙을 잠그고 적당한 크기의 사용되지 않은 메모리 블록을 검색합니다. 왜냐면, 여러 스레드가 동시에 힙에 메모리를 할당할 수 있기 때문이죠. 그리고나서 빈 공간을 찾는데 성공하면 메모리에 값을 저장합니다. 
- point2 = point1를 하게되면 힙에 있는 동일한 인스턴스를 참조하죠. 그래서 point2 = 5 와 같은 작업을 하면 의도치 않은 공유를 일으킬 수 있습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/a9ec667e-a140-478e-9512-c90ff54107a4/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/360a6592-0b27-4e23-8a80-a8fdbc5b2893/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/f54549ff-78db-4763-b904-5ee4a69ee937/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/836825ef-d800-4f9c-a66a-97032010feb7/image.png)


### 결론 

- 결론적으로는 클래스는 힙 할당이 필요하고, 빈 공간을 찾아야 하고 그동안 힙을 잠궈서 무결성을 유지해야 하는 등의 부가적인 작업이 필요합니다. 의도치 않은 상태 공유를 일으킬 수도 있죠. 
- 반면에 스택은 힙을 사용하지 않으므로 더 빠르지요. 값을 복사하므로 의도치 않은 상태 공유에 취약하지도 않습니다. 앱을 디자인하는데 추상화의 이런 특성이 필요하지 않은 경우라면 값 타입인 구조체를 사용하는 것이 더 좋습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/102d6118-8826-4885-9d80-1c195651acd7/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/19e0cbfd-970b-4dd9-bedb-e6dbbab8ec33/image.png)


## 2. 코드 예시를 통한 struct의 사용 

### key 값으로 String을 사용(문자열을 힙에 저장, 힙 할당) 
- 말풍선을 만들겁니다. 대충 이런 모양 💬, 💭 이겠죠? 그런데 당연히 반대쪽 모양도 있을겁니다. 그래서 Orientaion이 있고, 내가 보낸 말풍선의 색은 달랐으면 좋겠으니 Color도 있습니다. 또, 맨 처음 채팅만 말풍선 꼬리가 있어야 해서 none과 말풍선 꼬리 2종류가 추가된 Tail도 있습니다.
- 그리고 makeBalloon 메소드를 생성했어요. 그런데, 이 말풍선은 채팅방에서 스크롤하면서 수없이 많이 호출됩니다. 그래서 매우 빨라야 해요. 그래서 var cache 를 추가했습니다. 한번만 생성하면 그 이후부터는 키값에 따라서 바로 꺼낼 수 있게 말이죠. 
- 그런데 문제가 있습니다. 뭘까요? 바로 key값인 String 입니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/18318bf3-72b4-42b8-ba2c-dabc537bd472/image.png)

### key 값으로 enum, struct를 사용(힙 할당 사용하지 않음) 
- 왜 String이 문제일까요 ? Swift에서 String은 구조체니까 값타입이죠? 그러면 힙을 사용하지 않고 스택만 사용하겠네요? 그럴 것 같지만 그렇지도 않다고 합니다. 
- 왜냐면, 저 key를 보면 알 수 있지만 key의 값이 가변적이죠. 길이가 1자일지 1000자일지 모르는 겁니다. 컴파일 타입에 길이가 정해져 있지 않죠. 그래서 Swift에서 String은 힙을 간접적으로 이용한다고 합니다. 정확히는 문자열 값을 힙에 저장한다고 해요. 이것은 Swift의 컬렉션 타입인 Array, Dictionary 같은 녀석들도 마찬가지라고 해요. 
- 그러면 이 코드를 어떻게 개선하면 좋을까요 ? 힙을 사용하지 않게 하면 될 것 같아요. 
- 해결책은 아래와 같습니다. Attribute라는 구조체를 만듭니다. 그리고 Hashable을 준수합니다. Swift에서 구조체는 일급 객체이므로 key값으로 사용할 수가 있습니다. 이렇게 하면 힙 할당이 필요 없어지므로 더 효율적인 코드가 됩니다. 
- 간접적으로 힙 할당을 사용했던 String을 제거함으로써 스택에 할당할 수 있는 것이죠. 훨씬 더 안전하고 더 빠릅니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/a12f9ed5-ea1c-404c-a8ee-d3fb2088ccfb/image.png)



## 3. Struct의 Reference Count 

### 참조를 포함하는 struct은 어떨까요 ? 
- Swift에서는 ARC가 자동으로 refCount를 처리해주죠. 인스턴스를 사용하게 되면 refCount를 1씩 올려주고, 사용이 끝나면 1씩 내려줘서 결국에 0이되면 메모리에서 해제됩니다. 
- 중요한 것은, 이것이 매우 빈번하고 실제로는 정수를 증가/감소 시키는 것보다 더 많은 작업이 있다는 거에요. 첫째로는 실행하는 것과 관련된 몇 가지 간접 참조가 있다는 것이고 둘째로는 힙 할당과 마찬가지로 동시에 여러 스레드의 힙 인스턴스에 참조를 추가/제거 할 수 있기 때문에 thread safety를 고려해야 한다는 겁니다. 
- struct는 refCount가 없을까요? 그렇지는 않습니다. String과 UIFont가 포함된 아래의 코드를 보죠. String은 위에서 말했던 것처럼 실제로 해당 문자의 내용을 힙에 저장한다고 했죠? 그래서 refCount가 필요합니다. UIFont는 클래스니까 당연히 필요하겠죠. 
- 처음에 label1에 2개의 참조가 있습니다. 그리고 let label2라는 사본을 만들 때 실제로 2개의 참조를 더 추가합니다. 하나는 text storage에 하나는 font에 추가하죠. 이런 힙 할당은 refCount의 retain/release에 대한 호출을 추가하는 것입니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/8071e1fe-ba13-4265-bb54-d32ab9db5c90/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/109ecd48-cd5b-4d86-ab7c-db6dfe68666c/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/213e7493-008c-4457-8fb6-72496027d275/image.png)


### 참조를 2개 이상 포함하는 struct의 경우 class보다 좋지 않습니다. 
- 결론적으로 클래스였으면 reference counting overhaed가 2개면 됐을건데, struct는 label1과 label2이 독립적인 인스턴스이므로 reference counting overhaed가 4개가 발생되어 버립니다. 
- 실제로 구조체는 포함된 참조 수에 비례하는 reference counting overhaed를 지불하게 되는 거죠. 2개 이상의 참조를 포함하는 구조체의 경우 오히려 클래스보다 효율이 좋지 않습니다. 
- 클래스의 경우 
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/b2eee608-3a8a-4216-a21b-9494379ff48f/image.png)
- 구조체의 경우 
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/9d4588c5-7da9-4a30-ab0b-a39e31a93240/image.png)
- 참조가 포함된 구조체의 경우
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/2a698d38-5bb1-4247-9141-13e014039f55/image.png)
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/e26193c4-1043-4b23-8629-7376f804fc15/image.png)



## 4. 참조가 포함된 struct의 성능 개선






## 추가로 들어보면 좋은 세션들
![](https://velog.velcdn.com/images/dev_kickbell/post/84825fdb-c03e-4035-aa68-54c2608dd089/image.png)










static 은 컴파일에 저기한다.
dynamic 은 실행중에 저기된다.
인라인.
구조체면 가능하다.
점프점프안하고 그대로 코드를 복사해서 실행하는것? 이다.
클래스로 하면 drawble을 상속받은 모두가 다들어올수있어서. d.draw()를 해도
컴파일타입에는 알수가 없다.
v-table dispatch. 점프점프해서 찾아가야된다. 즉, 실행중에 찾아가야된다.
그래서 이런걸 다이내믹 디스패치라고 한다.



## 힙, 스택할당

레퍼런스 타입 -> 힙할당
밸류 타입 -> 스택할당

## swift 종류별 값, 참조타입

1. 참조 reference 타입
- class
- function
- closure

2. 값 value 타입 
- struct 
- enum 
public struct Int
public struct Bool
public struct Float
public struct Double
public struct Character
public struct String
public struct Dictionary<Key : Hashable, Value>
public struct Array<Element>
public struct Set<Element : Hashable>  
Tuple도. 

 


