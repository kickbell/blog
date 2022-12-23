# 프로토콜의 동적 디스패치와 정적 디스패치

프로토콜에는 재밌는 점?이 하나 있습니다. 알고 계실려나 모르겠네요. 정적 디스패치와 동적 디스패치라는 것인데요. 그전에 먼저 메서드/정적/동적 디스패치의 의미부터 알아보고 가도록 하겠습니다. 더 자세히 알고 싶으시면 [Understanding Swift Performance](https://developer.apple.com/videos/play/wwdc2016/416/)를 참고하시기 바라요. 


## 1. Method Dispatch(메서드 디스패치) 
메서드 디스패치는 프로그램이 메서드를 호출할 때 실행할 명령을 선택하는 방법입니다. 더 세분화된 분류로 objc에서는 Dynamic Dispatch가 메시지를 보내는 방법으로 Message Dispatch가 있다고는 하지만, 여기서는 Static과 Dynamic으로만 설명하겠습니다. 

## 2. Static Dispatch(정적 디스패치)
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


## 3. Dynamic Dispatch(동적 디스패치)
- 참조 타입에서만 지원됩니다. 주로 class지요. 
- 런타임 시점에 호출되는 메서드가 결정되는 Method Dispatch 입니다. 컴파일 시점에 무슨 호출이 일어날 지 알 수 없고, 런타임 시점에 무슨 호출이 일어날 지 알 수 있기 때문에 Dynamic(동적) 디스패치라고 불립니다. 
- 왜 컴파일 시점에 무슨 호출이 일어날 지 알 수 없을까요? class가 상속을 통해 다형성(Polymorphism)을 지원하기 때문입니다. 
- 아래의 코드를 보죠. class Drawable이 있습니다. 그리고 Point와 Line은 Drawable을 상속하고 draw()를 오버라이드 하고 있죠. 그리고 drawables 라는 [Drawable] 배열을 가지고 for문 내부에서 draw() 메소드를 호출합니다. 
- 컴파일러는 저 draw()가 Point의 draw()인지 Line의 draw()인지 알 수 있을까요? 네 알 수 없죠. 컴파일 타임에는 알 수가 없습니다. 실제 런타임이 되어서 특정 버튼을 click하던가 해서 어느 메소드를 실행한 것인지 알 수 있기 전까지는 말이죠. 변수의 개수가 다르다고는 하지만 class라 stack에는 주소값만을 갖기 때문에 d[0]과 d[1]의 크기도 똑같죠. 
- 런타임에 어떤 녀석을 눌렀는지 알았다면 그 이후에는 어떻게 호출할까요? 예를 들어 Line을 선택했다고 해보죠. 이후 작업이 어떻게 되냐면, Line이든 Point든 파란색으로 type:이라는 부분이 있죠? 거기에 해당 class에 대한 메타데이터를 갖고 있습니다. 그래서 Line을 탭했다고 하면 type: 에서 정보를 갖고 옵니다. 거기에는 v-table(virtual method table) 이라는 녀석이 있어요. 바로 그곳에서 Line의 draw() 메소드가 어느 주소에 있는지를 찾고 그것을 실행하는 것이죠. 
- 결론적으로는 실제 무슨 타입을 호출하는지는 컴파일 시점에 알 수가 없다. 런타임 시점에 알 수 있다. 그래서 static 디스패치처럼 컴파일 최적화를 할 수가 없다는 겁니다.(참고로, 힙에 여러 쓰레드가 접근하는 thread safety 문제는 없다고 합니다.) 
- 그렇지만, 항상 class는 dynamic dispatch만 사용해야 되는 것은 아닙니다. 위에서도 static dispatch는 값, 참조 타입 모두를 지원한다고 적어두었는데요. class 앞에 얘는 상속을 사용하지 않을 것이다는 final 키워드를 쓴다거나 private, fileprivate같은 접근 제어자를 사용해서 상속을 사용하지 않을 것임을 표시해둔다면 컴파일러는 해당 클래스가 static dispatch를 사용한다는 것을 알고 static dispatch로 전환하게 됩니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/383b90ef-409e-4c0e-b67e-1b676bb9a97d/image.png)

## 4. 프로토콜의 동적 디스패치와 정적 디스패치

- struct는 static dispatch를 사용한다. 메소드 인라이닝을 사용해서 컴파일러가 최적화 할 수 있다.
- class는 기본적으로 dynaimic dispatch를 사용한다. 하지만 final, private를 사용한다면 static dispatch로 전환된다.
- class에서 다형성을 사용한다면 어떤 특정 타입을 호출하는지는 컴파일 시점에 알 수 없으므로, 런타임에서 v-table의 정보를 조회해서 실행하는 방식이다. 



- 
- 
- 


- 드디어 본론입니다. 프로토콜에서는 사용방법에 따라 정적/동적 디스패치가 나뉩니다. 무슨 말이냐면, 아래의 코드 예시처럼 우리가 일반적으로 프로토콜을 명시한 상태의 메서드는 동적 디스패치로 동작합니다. 반면에 extension으로 프로토콜을 확장한 상태의 메서드는 정적 메서드로 동작하지요. 


### 코드를 통한 예시 1 
- Movable이라는 프로토콜을 하나 만들었습니다. 요구사항으로 slow()라는 메서드를 가지고 있네요. 그리고 extension으로 fast()라는 메서드를 기본 구현해주고 있습니다. 
- Person에서 Movable을 준수하고, slow()를 구현해주었습니다. 이제 사용해보죠. 
- person1, person2를 만들었습니다. 둘 다 fast()를 사용할 수 있죠? 기본 구현이 되어있으니까요. 또 둘 다 같은 결과를 출력합니다. 차이점은 person2는 Movable로 캐스팅을 한다는 점이겠네요. 

```swift
protocol Movable {
    func slow()
}

extension Movable {
    func fast() { print("extension fast") }
}

struct Person: Movable {
    func slow() { print("Requirement slow") }
}

let person1 = Person()
person1.slow() //Requirement slow
person1.fast() //extension fast

let person2: Movable = Person()
person2.slow() //Requirement slow
person2.fast() //extension fast
```

### 코드를 통한 예시 2
- 약간 코드를 바꿔보죠. fast()를 재정의 해볼게요. 그러면 어떻게 될까요 ? 
- 출력 결과가 약간 달라집니다. person1에서 Override fast가 출력이 되죠. 이런 것을 Swift에서는 커스터마이징 포인트라고 부릅니다. 

```swift
protocol Movable {
    func slow()
}

extension Movable {
    func fast() { print("extension fast") }
}

struct Person: Movable {
    func slow() { print("Requirement slow") }
    func fast() { print("Override fast") }
}

let person1 = Person()
person1.slow() //Requirement slow
person1.fast() //Override fast

let person2: Movable = Person()
person2.slow() //Requirement slow
person2.fast() //extension fast
```

### 코드를 통한 예시 3
- 자, 이번에도 코드가 약간 바뀌었습니다. 뭐가 바뀌었을까요? 달라진 부분은 protocol 명시 부분에 fast() 메소드가 들어간 것 뿐입니다. 
- 그런데 출력이 이상하네요? 왜 person2.fast()가 Override fast이죠? Movable로 캐스팅 했잖아요? 버그일까요? 
- 일단 정리하면 버그는 아닙니다😀. 아까 위에서 그랬었죠. protocol을 명시할 때의 메서드는 동적 디스패치고, extension에서는 정적 디스패치라고요. 
- 그런데 지금 우리는 protocol을 명시하는 부분에 fast()를 추가했죠. 이렇게 해버리면 저 fast() 메소드는 동적 디스패치가 되어버립니다. 그래서 Movable로 캐스팅 했음에도 Override fast가 출력되는 것이죠. 

```swift
protocol Movable {
    func slow()
    func fast() //달라진 곳은 이 부분! 
}

extension Movable {
    func fast() { print("extension fast") }
}

struct Person: Movable {
    func slow() { print("Requirement slow") }
    func fast() { print("Override fast") }
}

let person1 = Person()
person1.slow() //Requirement slow
person1.fast() //Override fast

let person2: Movable = Person()
person2.slow() //Requirement slow
person2.fast() //Override fast
```

## 4. 정리하기 
- protocol을 명시하는 부분의 메서드는 동적 디스패치로 동작한다.
- protocol을 확장한다면 그 메서드는 정적 디스패치로 동작한다.
- protocol에 extension으로 기본구현을 하고, 상위 타입으로 캐스팅을 했더라도 명시부분에 해당 메서드가 있다면 그것은 동적으로 동작한다. 

## Reference

[https://developer.apple.com/videos/play/wwdc2016/416/](https://developer.apple.com/videos/play/wwdc2016/416/)
