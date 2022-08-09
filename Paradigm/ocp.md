이 글은 [베어코드](https://www.youtube.com/channel/UCEuyt4RB4mlMfADQMz-d2DQ) 님의 [Swift Object Oriented Progoramming](https://youtube.com/playlist?list=PLoPKxuu4_dG3jCiMcbskRuNsgZsdZ4zz6) 을 보고 정리한 글 입니다. 


> ## 요약 
- 버트런드 마이어가 1988년에 제안
- 소프트웨어 개체는 확장에 대해서는 열려있고(새로운 기능을 넣기 위해 잘 동작하는 기존 코드는 손대지 않고, 새로운 코드를 추가하는 것만으로 가능한 것), 수정에 대해서는 닫혀있어야(새로 추가되는/수정되는 코드는 기존 코드의 변경을 초래하지 않아야)한다. 
- enum case 를 사용하는 코딩이 있고 그 enum이 수십번 쓰인다면 수정이 생기면 그걸 일일히 다 바꿔줘야만 한다. 심지어 default를 사용하고 있으면 컴파일 에러가 발생하지 않아 노답이 될 수 있다. 
- case를 추가하면 꼭 수정을해야하니 이건 확장에 닫혀있다고 볼 수 있다.  
- 그래서 프로토콜을 사용해서 OCP를 써라는 말이다. 밑에 예제코드가 있다. 
- 그러면 OCP는 만능인가? 아니다. enum case가 몇개 되지 않고, 수정될 가능성도 없다면? 오히려 복잡해 보일 수 있다. 그러니 상황에 맞게 잘 써야 한다. 
- 언제 사용할지, 생각해볼 부분, OCP를 사용한 패턴들은 다시보자. 
- 시작은 if/switch 를 극도로 제한하는 코딩부터 !! 



개발자들의 희망사항 
- 새로운 기능은 쉽게 추가 
- 기존 기능에 대한 수정은 쉽게 

버트런드 마이어가 1988년에 제안 

"소프트웨어 개체(클래스, 함수 등 소프트웨어를 다루는 모든 단위)는 확장에 대해서는 열려있고 수정에 대해서는 닫혀있어야 한다."


- 확장에 대해서 열려있다. 
	- 새로운 기능을 확장하기 위해서 잘 동작하는 기존 코드를 손대지 않고 새로운 코드를 추가하는 것만으로 가능해야 한다.
- 수정에 대해 닫혀있다. 
	- 새로 추가되는 코드 혹은 수정되는 코드는 기존 코드의 변경을 초래하지 않아야 한다. (여러군데에서 수정해야 되고 그런것을 말함) 
    
    
### OCP를 준수하면... 
- 코드의 `경직성`이 줄어든다.
- 빠르고 안정적인 수정이 가능하다.
- 사이드이펙트가 최소화된다. 

`경직성`: 코드를 수정하기 어렵게 되어있거나 수정하더라도 사이드이펙트를 일으키기 쉬운 상태로 되어있는 것. 스파게티 코드 같은 느낌? 


### 어떻게 하면 되는가? 
- OCP는 추상화에 크게 의존한다. 
- 추상화된 인터페이스를 선언한다. 그리고 그 인터페이스를 상속받아 구체적인 행위를 구현한다. 
- 해당 모듈을 사용하는 코드는 구체화된 클래스에 의존하지 않고 추상화된 인터페이스만 사용해서 동작한다. 따라서 새로운 클래스가 추가되었다고 해도 기존 코드를 손댈 이유가 없다. 

### OCP를 사용한 디자인 패턴 
- Abstract Factory : 새로운 팩토리를 추가하기 위해 추상클래스를 상속받은 구현을 하나 추가
- Command : 새로운 동작을 abstract command를 상속받은 구현을 하나 추가
- State : 새로운 state를 상속받아서 하나 추가
- Strategy : 새로운 구현체를 하나 만들어서 알고리즘을 작성 

⭕️ 기본적인 원리는 모두 동일하나 용도에 따라 다른 이름을 붙임. 


### OCP를 따르지 않았을 때의 결과 

- 어떤 타입에 대한 반복적인 분기문
    - 캐시워크의 앱퀵메뉴처럼 Enum타입으로 구현되어있고 이것이 switch/if문으로 반복적으로 사용되고 있다고 하면 OCP를 따르지 않았을 확률이 매우 높다. 
   	
    - 만약에 enum에 케이스가 하나 추가됐다고 하자. 근데 앱퀵메뉴를 수십군데에서 사용하고 있다. 그러면? 코드 수십군데를 다 찾아다니면서 확인해야 한다. swift같은 경우에 enum으로 정의했을 때 default를 사용하고 있지 않다면, 다행?히 컴파일러가 잡아주지만 만약에 default를 사용하고 있다면 새로운 case가 누락될 가능성이 매우 크고 그것은 버그를 발생시킬 확률이 매우 높다. 
    - 이런 케이스가 바로 enum의 변경으로 여러군데에서 수정해야하기 때문에 수정에 닫혀있지 않다고 말할 수 있다. 
    
    
### enum을 사용한 예 

1. 일반적인 enum의 사용 
![](https://images.velog.io/images/dev_kickbell/post/89a44fd3-336c-43dd-9f2c-db52e795b991/image.png)

2. swift는 조금 더 세련된 표현을 할 수 있다. swift에서는 이런식으로 스위치문들이 enum안에 구현되어 있을 때 더 효과적인 코딩을 할 수 있다. case가 1번에 비해 하나 더 추가되었음에도 불구하고 가독성이 더 좋다.

![](https://images.velog.io/images/dev_kickbell/post/55e100ab-fb6a-43a3-a342-5499b73c6668/image.png)

3. 이런경우라면 ? 
- enum안에 run이라는 메소드가 있다. 이 메소드는 enum별로 어떤 코드를 실행해야 한다. 어떤 처리를 해주는데 스위치문 별로 꽤나 복잡한 동작을 해줘야해서 몇십줄 또는 백줄 이상의 코드를 구현해야 한다면? 
- 이런 것 때문에 OCP를 사용하면 enum을 굉장히 제한적으로 사용할 수 밖에 없게 된다. 
![](https://images.velog.io/images/dev_kickbell/post/12da6f88-9f98-45f6-a6b3-fc245d624b4e/image.png)
    

### OCP를 사용한 예 
- OCP를 사용하면 Vehicle을 Enum이아니라 Protocol로 만들게 된다. 
- 추상화된 어떤 녀석을 인터페이스로 정의하고 각자 타입에 따라 클래스를 생성해준다. 그리고 메소드에는 자기가 표현해야 될 내용만 정의해준다. 이렇게 하게되면 하나의 enum안에서 복잡한 동작을 하는 것이 아니라 각각의 클래스 안에서 각자의 동작을 할 수 있게 된다. 결정적으로 아예 분기문이 사라져버렸다. 
- 이 방법은 장점은 기존대로라면 1)타입을 enum에 추가 2)switch문 일일히 다고침 3)enum안에 스위치문이 없으면 그걸 일일히 찾아가서 수정해야하는 반면에, 지금과 같은 경우는 새로운 기능이 추가된다면 그냥 새로운 클래스를 하나 만든다. 그리고 프로토콜을 준수해서 새로운 코드를 구현하기만 하면 기존 코드에 전혀 영향없이 기능이 추가되는 것이다.
- 지금은 OOP를 공부하는 중이므로 class를 많이 사용한다. 
![](https://images.velog.io/images/dev_kickbell/post/1892200f-92b4-419b-a2b8-1512b073780e/image.png)


### OCP는 silver bullet인가 ? 

- 그렇지않다. 
- OCP가 남용되면 `불필요한 복잡성`을 띄게 된다. 
- 경우에 따라 다른것이다. 아까의 코드처럼 enum이 되게 많고, 각각의 케이스별로 코드가 실행될 부분이 많은 경우에는 OCP가 코드를 단순화 시키는데 도움이 될것이다. 
- 하지만, 새로운 타입이 추가될 가능성이 없고 enum 의 종류가 몇 개 되지 않는 경우에는 오히려 enum만 쓸때보다 더 복잡한 상황이 될 수가 있다.  

> **silver bullet**
"No Silver Bullet". 유명한 Software Engineering업계의 고전이다. 
silver bullet은 말그대로 은총알을 말한다.은총알은 유일하게 늑대인간을 죽일수 있는 무기로 알려져 있다.그래서 silver bullet이란은 비장의 무기, 만반의 대책, 묘책 등의 의미로 쓰이기시작한 것으로 알고 있다.
소프트웨어 개발에 있어서 Silver Bullet은 없다. 21세기 들어오기 전부터 그것도 수십년 전부터있던 이야기다. 어떤 툴을, 어떤 활동을 적용 도입하더라도 그 하나만으로 모든 것이 해결되지 않는다.

### 그렇다면 언제 사용할 것인가? 
- 타입에 새로운 멤버들이 계속 추가될 가능성이 클 때 
- 타입의 멤버로 분기처리 되는 곳이 매우 많을 때(스위치문이 겁나많고, 그걸 여러군데에서 사용할때)

### 더 생각해봐야 할 부분 
- 어떤 부분을 open시키고 어떤 부분을 close 시킬 것인가? 
	- 어떤 부분을 추상화시키고 어떤 부분을 구체화 시킬 것인가? 
    - 명확하게 판단되지 않을 때는 먼저 변경이 없다고 가정하고 개발을 시작한다. (유연성 보다는 단순성이 우선) 
    - OCP같은 경우 유연성을 높지만 단순성이 낮은 특징이 있다. 그러니 일단은 고 
    
- 그러다 변경이 발생할 때 
	- 이 변경이 왜일어났는지? 어떤곳에 영향을 미치는지? 생각한다. 
    - 그리고 변경이 일어난 부분이 저위에 `그렇다면 언제...`처럼 앞으로도 변경을 유발할 것 같다면 리팩토링을 진행한다. 
    
- 종류의 추가보다 인터페이스의 변화가 더 자주 일어난다면 ? 
	- 이게 무슨말이냐면 위에서 사용한 아래 사진을 다시 보자. 그니까 자동차의 종류인 세단/해치백/SUV는 고정인데 인터페이스인 readableName, chargeText, run()이 계속 늘어나는 상황을 말하는 것이다. 
    - 이렇게 되버리면 이게 설계를 잘못한게 되버리는거다. 무슨말이냐면 지금은 자동차종류(세단/해치백/SUV)가 close되어있는쪽으로 ocp를하려고했는데 실제로는 인터페이스가 늘어나버리니까. 그러면 애초에 인터페이스(readableName, chargeText, run())를 추상화시켜서 OCP를 적용하는 쪽으로 설계를 했어야 했다는 그런말이다. 맞나? 아리까리하다 나중에 다시보자. 
![](https://images.velog.io/images/dev_kickbell/post/12da6f88-9f98-45f6-a6b3-fc245d624b4e/image.png)
```swift 


```



### OCP의 연습
- 처음에는 의도적으로 사용해봐야함 
- 그게 아니라면 if/swiftch를 극도로 제한하는 코딩을 진행 


### 기타 
![](https://images.velog.io/images/dev_kickbell/post/75cf33a5-3210-4091-831d-ec334c322da2/image.png)
