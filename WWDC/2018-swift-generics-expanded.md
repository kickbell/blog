# Swift Generics (Expanded)


> _이 글은 [WWDC 2018 - Swift Generics (Expanded)](https://developer.apple.com/videos/play/wwdc2018/406)의 내용을 공부하고, 개인적으로 정리해놓은 글입니다._


![](https://velog.velcdn.com/images/dev_kickbell/post/1b6a7a36-9dbb-4cdf-b57b-e410e7a58f90/image.png)


## 1. 제네릭이 중요한 이유(What are generics)

### 
- 제네릭이 중요한 이유를 설명하는 하나의 방법은 아래의 Buffer와 같은 간단한 컬렉션을 디자인 하는 것입니다. 
- 버퍼에는 count가 있고, 각 요소의 index를 지정된 위치로 가져오는 subscript도 있습니다. 표준 라이브러리의 배열과 비슷하죠. 
- 하지만 그 return 타입을 어떻게 만들까요? 제네릭이 없다면 Any를 리턴해야 합니다. 하지만 그것을 실제로 사용하기 위해서는 타입을 꺼내고 캐스팅까지 해줘야 합니다. 매우 불쾌한 사용자 경험으로 이어지죠. 오류도 발생하기 쉽구요. 
- 사용 편의성만이 문제는 아닙니다. Any가 CGRect같은 너무 커서 자체 내부 저장소에 들어갈 수 없는 타입을 가지고 있을 수도 있습니다. 이럴 땐 간접 참조를 사용해야 하죠. 값에 대한 포인터를 보유해야 하기 때문에 사용 편의성과 정확성 뿐만 아니라 성능상의 이유로 이런 문제를 해결해야 합니다. 

```swift
struct Buffer {
    var count: Int
    
    subscript(at: Int) -> Any {
        // get/set from storage
    }
}

var words: Buffer = ["subtyping","ftw"]

// I know this array contains strings
words[0] as! String

// Uh-oh, now it doesn’t!
words[0] = 42
```

![](https://velog.velcdn.com/images/dev_kickbell/post/12f2429c-d9d4-4b3a-ab10-27e0b84f3d59/image.png)

- 그래서 Parametric Polymorphism 이라는 기술을 사용하는데, 이것은 Generic의 또 다른 말입니다. 
- 제네릭을 사용하면 Buffer<String>으로 선언하고 다른 타입을 넣으려고 하면 컴파일 에러가 발생합니다. Buffer<String> 선언 이후에 Buffer<CGRect>처럼 선언해도 에러가 나죠. 이미 String으로 선언이 되어있으니까요. 
- 가끔 Buffer 와 같이 <타입>없이 선언된 경우를 볼 수가 있는데, 이것은 컴파일러가 타입 추론을 해서 그렇습니다. 
- 리터럴값이 있는 경우에 컴파일러는 무슨 타입인지 추론할 수 있기 때문에 더 안전하고 성능이 최적화 될 수 있습니다. 
  
![](https://velog.velcdn.com/images/dev_kickbell/post/5345f5b1-54a0-49b6-9056-14960669b2ff/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/488cb95b-a3fc-479f-b547-1d98c3ff5672/image.png)

- 그러면 이런 경우를 생각해볼게요. Buffer라는 컬렉션의 요소를 전부 다 더해주는 sum()이라는 기능을 추가한다고 해보겠습니다. extension으로 빼구요. 
- 그러면 에러가 발생합니다. 누적 값으로 더해줄건데, Bool 타입이나 내가 만든 MyCar 같은 타입은 더해줄 수 없을테니까요. 
- 그럴 땐 where 절을 이용해서 제네릭에 제한을 걸 수 있습니다. 여기서는 UInt, Int8, Float, Double 등이 준수하는 Numeric 이라는 프로토콜로 제한을 주었습니다. 
  

![](https://velog.velcdn.com/images/dev_kickbell/post/dccdd6fa-c0be-4217-b581-efa46a089fff/image.png)


## 2. 프로토콜 설계(Protocol design)

### 프로토콜 추출 프로세스 
- 다음으로는 다양한 유형의 프로토콜을 추출하는 프로세스에 대해 이야기 해보겠습니다.   아래의 코드처럼 Array, Dictionary, Data, String 이면 어떻게 해야할까요? 
- 유사하지만, 약간약간씩 코드가 다릅니다. 그래서 그래서 공통 기능을 모두 캡처하는 프로토콜을 만들려고 합니다 바로 Collection 이죠. Collection은 Swift의 표준 라이브러리에 있죠. 지금은 Collection의 축소되고 단순화된 버전을 만들려고 합니다.
- 먼저 다양한 수의 타입을 고려하고, 가능한 많은 유연성이 있었으면 좋겠습니다. 또 확장하기도 편하고 사용자가 사용하기도 간단해야 합니다. 
- 접근 방법은 몇 가지 구체적인 타입으로 시작한 다음, 프로토콜로 통합을 시도하는 겁니다. 그리고 이런 식으로 생각하는 것이 매우 중요합니다. 
  
![](https://velog.velcdn.com/images/dev_kickbell/post/ef559d37-68c1-41c5-b9b7-0c7015ebf58e/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/a7059568-10ae-47aa-b30f-5b37866bb4ff/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/5cebd2e1-565a-4087-93ad-aeedf4d6bc35/image.png)

- 이제 Collection 프로토콜을 조금씩 구현해보겠습니다. 먼저 associatedtype을 지정할건데, 프로토콜의 제네릭이죠. 여기서는 Element라는 이름을 사용합니다. Swift 4.2에서 버퍼와 배열은 이미 지정되어있습니다. Dictionary 같은 경우는 Key, Value 값이 필요하기 때문에 typealias로 설정하였습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/d4b747e5-c8da-4388-b9b1-a931f97ff4df/image.png)
  
- 다음으로 subscript 연산을 추가하는 방법에 대해 알아보겠습니다. 컬렉션에는 서브스크립트로 특정 컬렉션의 주어친 위치를 가져올 수 있는 기능을 제공해야 합니다. array[0], array[1] 처럼요.
- 여기에는 Int가 인수로 사용하도록 할 수 있습니다. Array 타입에서는 사용하기 쉽고 유용하니까요. 하지만 Dictionary 라면 어떨까요? 
- 아래의 Dictionary 코드를 보겠습니다. 여기에는 한 요소에서 다음 요소로 이동하기 위한 특정 논리가 있습니다. 일종의 내부 버퍼에 의해 지원될 수 있으며 해당 버퍼에 _offset을 저장하는 인덱스 타입을 사용할 수 있습니다. 그리고 _offset을 _stroage[at._offset] 처럼 subscript의 인수로 사용할 수 있을 겁니다. 
- 그리고 Dictionary의 Index가 Dictionary만 제어할 수 있어야 하기 때문에 _offset을 private 으로 지정합니다. 
- 다음은 Dictionary의 다음 요소로 이동하게 해야 하는데요. index(after:)라는 메소드를 만들어서 주어진 인덱스가 그 뒤의 인덱스를 리턴하도록 합니다. 
- 여기서 몇 가지 작업이 더 필요한데요. 바로 start, end 인덱스 속성이 필요합니다. 지금은 끝에 도달했는지 알 수가 없기 때문이죠. 자, 이제 Dictionary에서는 Int대신에 Index라는 인수를 사용합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/b2d16363-db48-42ae-9c24-6d0963bada10/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/07445ca0-f956-4163-b188-083e60b0667c/image.png)

- 다시 프로토콜로 이 부분을 추가해보겠습니다. 우리는 위치를 나타내는 인덱스 타입을 가지고 있고, subscript도 있습니다. 그리고 우리는 그 위치를 앞으로 이동시키는 방법을 가지고 있습니다.
  
![](https://velog.velcdn.com/images/dev_kickbell/post/95fe5149-07b9-46e9-b7f4-26dea9415b06/image.png)
  
- 그런데, Array에는 여전히 Int를 인수로 사용하는게 더 좋잖아요? 더 이해하기도 쉽구요. 그래서 associatedtype Index 를 추가합니다. 
- 이렇게 하면, Array같이 Int가 더 편한애들은 Int를 쓰고 Dictionary같은 애들은 Index를 사용할 수 있습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/05045846-0cb7-42a8-9dc3-87a8f0631c4b/image.png)
  
- 다음은 count 속성을 보겠습니다. Collection의 전체 갯수를 나타내는 아주 유용한 속성입니다. 전체 갯수를 리턴해야 하니까 i라는 변수를 두고, startIndex와 endIndex가 같아질때까지 컬렉션을 이동하고 있죠. 
- 그런데, 비교를 하려면 start와 end가 같은 타입이어야 하죠? 또 그 타입에 비교를 할 수 있는 Equtable 프로토콜이 구현되어 있어야 합니다. 
  
![](https://velog.velcdn.com/images/dev_kickbell/post/be34d510-b3ba-45af-9867-d7a945654196/image.png)
  
- 그러면 이렇게 하면 어떨까요? 위에서 우리가 한 것처럼 where로 Index: Equatable 제약을 주는거죠. 
- 동작은 하지만 좋지 않습니다. == 연산자는 여기 말고도 다른 곳도 엄청나게 많이 쓰이죠. 그런데 이렇게 해버리면 우리가 extension 을 작성할 때마다 이 제약조건을 붙여줘야 하기 때문입니다. 
  
![](https://velog.velcdn.com/images/dev_kickbell/post/7da64472-86e3-4422-a491-c4ebb4b61f90/image.png)
  
- 대신에 프로토콜의 요구사항으로 표현하는 것이 더 낫습니다. 프로토콜에 이 제약을 가하는 것은 프로토콜을 준수하는 모든 유형이 해당 인덱스에 대해 일치 가능한 유형을 제공해야 함을 의미합니다.
- 이렇게 하면 extension을 작성할 때마다 조건을 지정할 필요가 없어집니다. Dictionary 또한 Dictionary.Index로 제약을 주어서 타입을 동등하게 만드는 것도 쉽습니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/15958b3d-0897-4545-84be-e71d64730b00/image.png)

                                
### 커스터마이징 포인트 
- 다음으로 커스터마이징 포인트를 사용하여 이 count 작업을 최적화하는 방법에 대해 이야기하겠습니다. 지금 같은 경우 count를 호출하면, 무조건 선형시간이 걸립니다. 한바퀴 다돌아야되는거죠. 분명히 더 나아질 수 있겠죠. 
- 예를 들어, Dictionary가 고유한 목적을 위해 보유한 요소의 수를 내부적으로 유지한다고 가정해볼게요. 그러면 while을 돌 필요가 없죠. 그냥 그 값을 리턴하면 되잖아요. count를 리턴하는데 필요한 시간이 O(n)에서 O(1)이 되는거죠. 더 성능 좋은 O(1) 버전의 count이 생기는 겁니다.
- 표준 라이브러리의 고차함수인 map을 예시로 들어보겠습니다. Collection의 extension이고, 빈배열을 하나 만들고 그걸 transfrom해서 빈배열에 추가합니다. 처음부터 끝까지요. 그리고 꽉찬 배열을 리턴하는거죠. 
- 근데 여기서 비용이 생깁니다. 뭐냐면, 빈배열이잖아요? 그럼 그 빈배열이 통상적으로 10칸 짜리로 생긴다고 쳐요. 근데 데이터가 1000개인거죠. 그러면 10개에서 배열의 크기를 늘려줘야 될겁니다. 그리고 크기가 커짐에 따라서 10개였던 내부 저장소를 20개로 할당, 30개로 할당, 40개로 할당... 처럼 계속 반복해줘야 될 겁니다. 이런 메모리 할당 작업은 생각보다 꽤 비용이 비쌀 수 있지요. 
- 여기서 최적화를 할 수 있는 방법이 있습니다. 뭐냐면, Collection의 extension 이잖아요? 그럼 Collection의 크기를 우린 알고있죠. map으로 리턴되는 배열의 크기도 Collection의 크기와 정확히 일치하겠죠. 
- 그러면 아래의 코드처럼 result.reserveCapacity(self.count) 를 해서 얼마만큼의 크기의 배열을 만들건지 미리 예약을 하는겁니다. 그러면 속도가 빨라지겠죠. 하지만 이것의 전제조건은 count가 Collection의 전체 크기를 O(1)로 불러올 수 있어야 한다는 거죠. O(n)으로 불러온다면 성능 향상 효과가 오히려 떨어질겁니다. 
  
![](https://velog.velcdn.com/images/dev_kickbell/post/a4e3bd34-12dd-4604-b94c-f2cbc55cc1d1/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/4a0e0786-1078-4676-8dc0-530b28b3d41d/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/3acc919f-c2c5-4686-83d3-75c38b8df0e6/image.png)

- 자, 그럼 어떻게 해야 할까요 ? 
- 그건 바로 protocol + extension을 이용하는겁니다. 일단 Collection 프로토콜에 var count: Int { get } 을 추가합니다. 그리고 Dictionary에 extension으로 count를 구현해줍니다. 
- 만약에 이걸 안해줬다고 해도 에러는 나지 않아요. 왜냐면 Collection에 이미 선형 시간의 count가 있고, Dictionary는 Collection 프로토콜을 준수하기 때문이죠. 
- 그리고 이렇게 프로토콜에 요구 사항을 추가하고 이와 함께 확장을 통해 기본 구현을 추가하는 것을 customization point(커스터마이징 지점)이라고 합니다.
- 이 커스터마이징 포인트를 통해 컴파일러는 인식합니다. 아, Dictionary에는 count의 다른 구현이 있구나. 라고 말이죠. 그러면 이제 self.count를 호출할 때, 따라서 generic 컨텍스트에서는 프로토콜을 통해 해당 구현에 동적으로 디스패치합니다. 그러면 이제 런타임에서 선형시간이 아니라 O(1)인 기본 구현을 넣은 count를 호출하는 거겠지요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/59d99c76-1112-44ff-a9a2-fa57b1e43789/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/db9292de-3a71-43b8-894f-a2344fde697e/image.png)
  
- 이 기술은 클래스뿐만 아니라 구조체와 열거형에서도 작동합니다. 그러나 모든 타입에 이렇게 할 수 있는 것이 아니고 특정 타입만 가능하겠지요. 
- 그리고 커스터마이징 포인트는 바이너리 크기, 컴파일러 런타임 성능에 작지만 0이 아닌 영향을 미칩니다. 따라서 커스터마이즈할 기회가 확실히 있을 때만 커스터마이징 지점을 추가하는 것이 합리적입니다.
- 예를 들어 방금 작성한 map 에서 다른 종류의 컬렉션이 실제로 더 나은 구현을 제공할 수 있는 합리적인 방법은 없습니다. 따라서 커스터마이징 포인트로 추가하는 것은 의미가 없습니다.

### 정리하기 
- extension 마다 제약를 추가하는 것보다 프로토콜로 만들고 제약을 추가하는 것이 좋다. 
- Collection 프로토콜이 있다. 그리고 extension 으로 count를 구현했다. 
- Collection 프로토콜을 준수하는 Dictionary 라는 녀석이 있다. 
- 그런데 여기서 Dictionary에 extension으로 count를 구현하면, self.count를 할 때 Dictionary에 있는 count를 가져온다. 그래서 그것으로 성능을 최적화 할 수 있다. 
               
## 3. 프로토콜 상속(Protocol inheritance)
  
### lastIndex(where:)
- 프로토콜에서도 상속이 필요합니다. 정확하게는 상속을 사용해서 보다 전문화된 알고리즘을 제공할 수 있습니다. 
- 예를 들면, 현재 우리가 위에서 만든 컬렉션은 index(after:)로 앞에서부터 뒤로 이동이 가능하죠. 잘못된 것이 아닙니다. 컬렉션 중에는 SingleLinkedList 처럼 단방향으로 이동할 수 밖에 없는 컬렉션이 있으니까요. 
- 그러면 여기서 뒤에서부터 올 수 있는 양방향 컬렉션이 필요합니다. 그리고 BidirectionalCollection은 Collection을 상속하지요. Collection의 모든 기능을 사용할 수 있습니다. 
- BidirectionalCollection 프로토콜 내에서 index(before:)를 정의하고, extension에서 기본구현을 통해 lastIndex(where:)를 구현할 수 있습니다. 
  
![](https://velog.velcdn.com/images/dev_kickbell/post/501ad1ac-2205-4039-989f-08f145518bec/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/1438e5a1-99bf-45d7-a04e-c83be2d311c0/image.png)


### shuffle()
- 또 다른 예로 셔플기능을 들어보겠습니다. Swift 4.2에 도입됐어요. 셔플기능은 컬렉션을 임의로 섞는 기능이죠. 여기서는 꽤 오래된 [Fisher-Yates 셔플 알고리즘](https://ko.wikipedia.org/wiki/%ED%94%BC%EC%85%94-%EC%98%88%EC%9D%B4%EC%B8%A0_%EC%85%94%ED%94%8C)이 사용됐습니다. 교체할 다른 요소를 무작위로 선택하고 index를 한칸씩 이동해주는 선형 시간의 알고리즘이죠. 
- 셔플 기능을 구현하려면 2가지 기능이 필요합니다. 일단 요소를 무작위로 선택하는 기능인데 Int.random(in:)이 이미 있죠? 그런데 컬렉션이므로 Int는 안됩니다. 그래서 index(offsetBy:)가 필요합니다. 두 요소를 바꾸는 swapAt(,) 이죠. 새롭게 만들어진 shuffleCollection 프로토콜 입니다. 
  
![](https://velog.velcdn.com/images/dev_kickbell/post/e76881a1-d870-4f4a-b497-7e6289118a14/image.png)
  
- 하지만 이게 맞나요? 이것은 안티 패턴입니다. 이렇게 설계하면 안됩니다. 우리는 그 요구 사항을 찾은 다음 바로 통째로 하나의 프로토콜로 패키지화했습니다. 이것은 단지 그 하나의 알고리즘을 설명합니다.
- 그러면 어떻게 해야 할까요? 고유한 기능을 분리해서 유지해야 합니다. 아래의 그림처럼 RandomAccessCollection 프로토콜과 MutableCollection 프로토콜로 나눌 수 있습니다. 
- RandomAccessCollection 프로토콜은 인덱스를 빠르게 이동하면서 컬렉션을 건너뛸 수 있게 해줍니다. 재밌는 점은 위에서 말했듯이 SingleLinkedList 같은 컬렉션은 중간 인덱스로 바로 이동할 수 없으므로 BidirectionalCollection 을 준수하고 있다는 겁니다. 
- 그리고 또 다른 MutableCollection 프로토콜이 있습니다. swapAt(,) 기능을 가지고 있습니다. 
  
![](https://velog.velcdn.com/images/dev_kickbell/post/4390e1f7-e1c9-44ea-92c3-178c81635b0d/image.png)

- 그러면 이제 shuffle()로 다시 돌아와서 그것을 구현해줄 수 있습니다. randomAccessCollection의 extention 으로 구현해줬고, Self: MutableCollection으로 제약이 걸려있어 RandomAccessCollection 와 MutableCollection 를 둘 다 준수합니다. 
  
![](https://velog.velcdn.com/images/dev_kickbell/post/c1887a45-5d22-4d41-a6be-1a681da19b1f/image.png)

  
### 정리하기 
  
- 정리하자면, 맨처음에는 특정 알고리즘을 구현하기 위해 ShuffleCollection이라는 것을 만들고 하나의 기능만을 설명하는 프로토콜을 구현했습니다. 
- 하지만, 이렇게 너무 세분화 되어서는 안됩니다. 사용하기 쉬우려면 소수의 프로토콜이어야 하기 때문입니다. 그래야 사용하기가 쉽습니다. 무작위로 프로토콜을 생성한다면 나중에는 너무 많아져 알 수 없는 코드가 되어버릴 가능성이 큽니다 .
- 아래의 그림처럼 프로토콜 별로 고유한 기능으로 나누고, 그 기능을 통합해서 새로운 기능을 구현하면 됩니다. 그러면 아래처럼 계층 구조가 생기게 되죠. 공통점이 보입니다. 계층 구조의 맨 아래에서 맨 위로 이동하면 요구사항이 더 적은 프로토콜로 이동하게 됩니다. 반면에 이제 계층 구조 아래로 이동할수록 고급 기능이 필요한 보다 복잡하고 전문화된 알고리즘을 구현하게 됩니다.
  
![](https://velog.velcdn.com/images/dev_kickbell/post/61b34096-c185-4cad-9fc8-a7846b5992e3/image.png)

 
## 4. 조건부 적합성(Conditional conformance)

어려워서 중도포기. 다음에 다시 읽어보자. 

https://developer.apple.com/videos/play/wwdc2018/406/
https://www.hackingwithswift.com/example-code/language/how-to-use-conditional-conformance-in-swift
https://www.swiftbysundell.com/articles/conditional-conformances-in-swift/


  
## 5. 클래스와 제네릭(Classes and generics)


## Reference

[https://developer.apple.com/videos/play/wwdc2018/406](https://developer.apple.com/videos/play/wwdc2018/406)			

## Endnotes 

### ¹스냅샷(Snapshot)
- 사진을 찍듯이 특정 시점에 데이터를 별도의 파일이나 이미지로 저장, 보관하는 기술을 말합니다. 그래서 스냅샷 기능을 이용하여 데이터를 저장하면 유실된 데이터 복원과 일정 시점의 상태로 데이터를 복원할 수 있습니다.


<details>
  <summary><a href=""></a>reorderingHandlers</summary>
  <p>

```swift
  
```
  </p>
</details>





