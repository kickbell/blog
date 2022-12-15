
# Singleton Pattern(싱글턴 패턴)
> - 클래스 인스턴스를 하나만 만들고, 그 인스턴스로의 전역 접근을 제공한다.

## 1. 싱글턴과 전역변수 
- 싱글턴 패턴대신 그냥 전역변수를 사용하면 왜 안될까요 ? 전역변수에 객체를 대입하면 애플리케이션이 시작될 때 객체가 생성됩니다. 만약 그 객체가 자원을 많이 차지하고, 그 객체를 애플리케이션이 끝날 때까지 한번도 사용하지 않는다면? 자원낭비죠.
- 싱글턴 패턴을 사용하면 lazy하게(필요할 때만 생성되도록) 사용할 수 있습니다. 
- 아래의 코드처럼 생성자를 private 으로 만들고, 타입 메소드를 통해 이미 1번 생성했다면 그대로 리턴하고 생성되지 않았다면 생성해서 리턴하는 것으로 유일한 객체를 사용할 수 있습니다.
- 하지만 아래의 코드는 문제가 있다. 

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
/*
위아래 같은 코드지만 swift의 lazy 키워드 사용여부가 다름
*/
class Singleton {
    private init() { }

    private static var uniqueInstance: Singleton?

    static func getInstance() -> Singleton {
	//lazy 키워드를 쓰지 않고 lazy하게 사용
	//Singleton 객체를 한번도 사용하지 않는다면 생성되지 않으므로 그만큼 메모리 절약
	if uniqueInstance == nil {
	    uniqueInstance = Singleton()
	}
	return uniqueInstance!
    }
}

class Singleton {
    private init() { }

    //lazy는 static 키워드로 사용이 불가
    //'lazy' cannot be used on an already-lazy global
    private lazy var uniqueInstance: Singleton = Singleton()

    //uniqueInstance가 타입 인스턴스가 아니기 때문에 여기서도 static 삭제
    //Instance member 'uniqueInstance' cannot be used on type 'Singleton'
    func getInstance() -> Singleton {
	return uniqueInstance
    }
}
```
  </p>
</details>

## 2. Thread-Safety 
> If a property marked with the lazy modifier is accessed by multiple threads simultaneously and the property hasn’t yet been initialized, there’s no guarantee that the property will be initialized only once.

- 공식 문서에서도 lazy한 속성에 대해서는 한번만 초기화되어야 된다는 보장이 없다는 글이 있다. 
- 즉, 아래의 그림과 같은 상태이다. 아직 객체가 초기화되기 전에, 여러 곳에서 접근해서 원래 의도와는 다르게 여러개의 객체가 생기는 경우이다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/e0e51cbb-2eb4-4b0b-a2ba-7c300c6c35d5/image.png)


## 3. 해결하기 
1. Synchronization(동기화) 
	- getInstance()를 동기화시켜버리는 것이다. 즉, 초기화 될 때까지 강제로 기다리게 해버려서 다음에 접근할 때는 안전하게 하는 방법이다. objc 같은 경우는 @synchronized 키워드가 있지만 swift에는 그런 키워드가 없다. 따라서 DispatchQueue.global().sync {} 같은 것을 사용해야 할 것이다. 
    - 다만, 메소드를 동기화하면 성능이 100배쯤 저하된다고 한다. 그건 어떻게 하지? 가장 간단한 해결책은 getInstance()의 속도가 그리 중요하지 않다면(큰 파일이 아니라면) 그냥 둔다. 

2. lazy하게 사용하지 않고 그냥 처음부터 만들어버린다. 
	- 어찌보면 가장 간결한 해결책일 수 있다. 예전과는 다르게 하드웨어의 성능이 좋아져서 상대적으로 메모리가 넉넉하기 때문에, 아주 특정한 상황이 아니라면 괜찮을 것이다.

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class Singleton {
    private init() { }
    
    static var shared = Singleton()
}
```
  </p>
</details>

## 4. 실제로 구현해보기 1
- 예시코드로 초콜릿을 만드는 초콜릿 보일러 코드가 있다. 
- 초콜릿 보일러는 순서대로 채우고, 끓이고, 다음단계로 보낸다. 
- 아래의 코드를 그대로 사용했더니, 아직 초콜릿이 끓고 있는 와중에 새로운 초콜릿이 들어가버리는 오류가 발생했습니다. 이 코드를 싱글턴 패턴으로 바꿔보자.  

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>	
  <p>

```swift
class ChocolateBoiler {
    private var empty: Bool
    private var boiled: Bool
    
    private static var uniqueInstance: ChocolateBoiler?

    static func getInstance() -> ChocolateBoiler {
        if uniqueInstance == nil {
            uniqueInstance = ChocolateBoiler()
        }
        return uniqueInstance!
    }
    
    private init() {
        self.empty = true
        self.boiled = false
    }
    
    //채우기
    //보일러에 우유와 초콜릿을 혼합한 재료를 넣음
    func fill() {
        if isEmpty() {
            empty = false
            boiled = false
        }
    }
    
    //끓이기
    //비어있고, 끓이기전이라면 끓이기
    func boil() {
        if !isEmpty() && !isBoiled() {
            boiled = true
        }
    }
    
    //물빼기
    //끓인 재료의 물을 빼서 다음단계로 넘기기
    func drain() {
        if !isEmpty() && isBoiled() {
            empty = true
        }
    }
    
    func isEmpty() -> Bool {
        return empty
    }
    
    func isBoiled() -> Bool {
        return boiled
    }
}
```
  </p>
</details>


## 5. 실제로 구현해보기 2
- 2번째 방법으로 문제를 해결했다.

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class ChocolateBoiler {
    private var empty: Bool
    private var boiled: Bool
    
    static var shared = ChocolateBoiler()
    
    private init() {
        self.empty = true
        self.boiled = false
    }
    
    //...위와 같음 
```
  </p>
</details>


## 6. 문제점
- 그럼에도 불구하고 싱글턴 패턴은 느슨한 결합 원칙을 따르지는 않습니다. 싱글턴을 사용하다 수정하게되면 그에 따르는 클래스들도 고쳐야 하는 경우가 많기 때문이죠. 
- 하나는 클래스는 하나의 책임을 따른다(SRP) 원칙 또한 따르지 않죠. 싱글턴은 자신의 인스턴스를 관리하는 일 외에도 원래 그 싱글턴을 사용하고자 하는 목적의 코드가 들어갈 거에요. 최소 2가지의 일을 하게 되는 것이죠. 그러나, 많은 개발자가 이미 싱글턴에 익숙하고 특정한 목적에 있어서 싱글턴을 사용하면 앱을 더욱 간단하게 만들 수 있기 때문에 사용합니다. 


## Reference 
[https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223](https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223)


