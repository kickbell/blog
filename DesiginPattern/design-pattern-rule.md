## 객체지향 디자인 원칙 
> 1. [바뀌는 부분은 캡슐화한다.](strategy-pattern.md)
> 2. [상속보다는 구성을 활용한다.](strategy-pattern.md)
> 3. [구현보다는 인터페이스에 맞춰서 프로그래밍한다.](strategy-pattern.md)
> 4. [상호작용하는 객체 사이에는 가능하면 느슨한 결합을 사용해야 한다.](observer-pattern.md)
> 5. [클래스는 확장에는 열려 있어야 하지만 변경에는 닫혀 있어야 한다.(OCP)](decorator-pattern.md)
> 6. [추상화된 것에 의존하게 만들고 구상 클래스에 의존하지 않게 만든다.(DIP)](factory-pattern.md)
> 7. [진짜 절친에게만 이야기해야 한다.(PoLK, LoD)](adapter-facade-pattern.md)
> 8. [먼저 연락하지 마세요. 저희가 연락 드리겠습니다.(Hollywood Principle)](template-method-pattern.md)
> 9. [어떤 클래스가 바뀌는 이유는 하나 뿐이어야만 한다.(SRP)](iterator-composite-pattern.md)

## 디자인 패턴 한 줄 요약 

|패턴|설명|
|:---:|:---:|
|**Strategy Pattern ( 전략 패턴 )*|알고리즘군을 정의하고 캡슐화해서 각각의 알고리즘군을 수정해서 쓸 수 있게 해준다.|
|**Observer Pattern ( 옵저버 패턴 )*|한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체에게 연락이 가고 자동으로 내용이 갱신되는 방식으로 일대다(one-to-many)의존성을 정의합니다.|
|**Decorator Pattern ( 데코레이터 패턴 )*|객체에 추가 요소를 동적으로 더할 수 있습니다.|
|**Factory Method Pattern ( 팩토리 메소드 패턴 )*|객체를 생성할 때 필요한 인터페이스를 만듭니다. 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정합니다. 팩토리 메소드 패턴을 사용하면 클래스 인스턴스 만드는 일을 서브클래스에게 맡기게 됩니다.|
|**Abstract Factory Pattern ( 추상 팩토리 패턴 )*|구상 클래스에 의존하지 않고도 서로 연관되거나 의존적인 객체로 이루어진 제품군을 생산하는 인터페이스를 제공합니다. 구상 클래스는 서브 클래스에서 만듭니다.|
|**Singleton Pattern ( 싱글턴 패턴 )*|클래스 인스턴스를 하나만 만들고, 그 인스턴스로의 전역 접근을 제공합니다.|
|**Command Pattern ( 커맨드 패턴 )*|요청 내역을 객체로 캡슐화해서 객체를 서로 다른 요청 내역에 따라 매개변수화 할 수 있습니다. 이러면 요청을 큐에 저장하거나 로그로 기록하거나 작업 취소 기능을 사용할 수 있습니다.|
|**Adapter Pattern ( 어댑터 패턴 )*|특정 클래스 인터페이스를 클라이언트에서 요구하는 다른 인터페이스로 변환합니다. 인터페이스가 호환되지 않아 같이 쓸 수 없었던 클래스를 사용할 수 있게 도와줍니다.|
|**Facade Pattern ( 퍼사드 패턴 )*|서브시스템에 있는 일련의 인터페이스를 모아서 사용하기 쉽게 통합 인터페이스로 묶어줍니다.|
|**Template Method Pattern ( 템플릿 메소드 패턴 )*|알고리즘의 골격을 정의합니다. 알고리즘의 일부 단계를 서브클래스에서 구현할 수 있고, 알고리즘의 구조는 그대로 유지하면서 알고리즘의 특정 단계를 서브클래스에서 재정의 할 수도 있습니다.|
|**Iterator Pattern ( 반복자 패턴 )*|컬렉션의 구현 방법을 노출하지 않으면서 집합체 내의 모든 항목에 접근하는 방법을 제공합니다.|
|**Composite Pattern ( 컴포지트 패턴 )*|객체를 트리구조로 구성해서 부분-전체 계층구조를 구현합니다. 클라이언트에서 개별 객체와 복합 객체를 똑같은 방법으로 다룰 수 있습니다.|
|**State Pattern ( 상태 패턴 )*|객체의 내부 상태가 바뀜에 따라서 객체의 행동을 바꿀 수 있습니다. 마치 객체의 클래스가 바뀌는 것과 같은 결과를 얻을 수 있습니다.|
|**Proxy Pattern ( 프록시 패턴 )*|특정 객체로의 접근을 제어하는 대리인(특정 객체를 대변하는 객체)을 제공합니다.|
| Bridge Pattern ( 브리지 패턴 )|구현과 더불어 추상화 부분까지 변경합니다.|
| Builder Pattern ( 빌더 패턴 )|제품을 여러 단계로 나눠서 만들도록 제품 생산 단계를 캡슐화 합니다.|
| Chain of Responsibility Pattern ( 책임 연쇄 패턴 )|1개의 요청을 2개 이상의 객체에서 처리합니다.|
| Flyweight Pattern ( 플라이웨이트 패턴 )|어떤 클래스의 인스턴스 하나로 여러 개의 '가상 인스턴스'를 제공합니다.|
| Interpreter Pattern ( 인터프리터 패턴 )|어떤 언어의 인터프리터를 만듭니다.|
| Mediator Pattern ( 중재자 패턴 )|서로 관련된 객체 사이의 복잡한 통신과 제어를 한곳으로 집중합니다.|
| Memento Pattern ( 메멘토 패턴 )|객체를 이전의 상태로 복구합니다.|
| Prototype Pattern ( 프로토타입 패턴 )|어떤 클래스의 인스턴스를 만들 때 자원과 시간이 많이 들거나 복잡할 때 사용합니다. 기존 인스턴스를 복사하기만 해도 새로운 인스턴스를 만들 수 있습니다.|
| Visitor Pattern ( 비지터 패턴 )|다양한 객체에 새로운 기능을 추가해야 하는데 캡슐화가 별로 중요하지 않다면 사용합니다.|


## 디자인 패턴 분류하기
- 생성,행동, 구조라는 3가지 범주로 분류			
			
![](https://velog.velcdn.com/images/dev_kickbell/post/dccbfdc6-be83-4fac-9afc-2faf984b11b7/image.png)

- 클래스 또는 객체를 다루는 지에 따른 분류			
				
![](https://velog.velcdn.com/images/dev_kickbell/post/2aeda93e-3647-4300-88f1-1506319f3035/image.png)


## Reference 
[https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223](https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223)
