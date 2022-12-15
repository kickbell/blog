# Observer Pattern(옵저버 패턴)
> - 한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체에게 연락이 가고 자동으로 내용이 갱신되는 방식으로 일대다(one-to-many)의존성을 정의합니다.

## 1. WeatherData에 따라서 현재기상조건/기상통계/기상예보 디스플레이를 만들어야 합니다. 
				
![](https://velog.velcdn.com/images/dev_kickbell/post/4887f093-ccee-47c9-901a-754892e76024/image.png)				

## 2. 대략적으로 생각나는대로 구현한 코드의 문제점
1. 인터페이스가 아닌 구체적인 구현을 바탕으로 코딩하고 있음 
2. 새로운 디스플레이 항목이 추가될 때마다 코드를 변경해야 함
3. 실행 중에 디스플레이 항목을 추가하거나 제거할 수 없음 
4. 바뀌는 부분을 캡슐화 하지 않았음         
				
![](https://velog.velcdn.com/images/dev_kickbell/post/464b9864-2ade-4801-81ca-de4416e42402/image.png)           		


## 3. 옵저버 패턴의 구조 
- 인터페이스는 Subject, Observer 2개가 있다.
- 이벤트를 송출할 방송국은 Suject(1개)이고, 이벤트를 받을 수신자는 Observer(여러개)이다. 
- 옵저버 등록, 옵저버 탈퇴, 이벤트 알림은 Subject의 메소드를 사용한다. 
- 데이터를 송출하는 주체는 Subject이고, 옵저버는 Subject의 연락을 기다리는 입장이기에 의존성을 가진다고 볼 수 있다. 
- 옵저버가 될 객체는 Observer 인터페이스를 준수해야 하고, 오직 update() 메소드만을 가진다.
			
![](https://velog.velcdn.com/images/dev_kickbell/post/bd4585f0-56a0-45af-a0ad-b875d1b1c2d3/image.png)				

## 4. 느슨한 결합(Loose Coupling)
- 느슨한 결합은 객체들이 서로 상호작용할 수는 있지만, 서로를 잘 모르는 관계를 의미한다. 
- Subject는 Observer가 특정 인터페이스(Observer 인터페이스)를 구현한다는 사실만 안다. 해당 인터페이스를 준수하는 클래스가 무엇인지, 옵저버가 무엇을 하는지 알 필요가 없다. 
- Subject는 Observer 인터페이스를 준수하는 목록만 필요하므로 언제든지 새로운 Observer를 추가, 제거해도 된다. 
- Subject와 Observer는 서로 독립적으로 재사용이 가능하다. 전혀 다른 앱에 활용한다고 해도 단단하게 결합되어 있지 않으므로 재사용할 수 있다. 
- Subject와 Observer둘 다 각자의 인터페이스를 구현한다는 조건만 만족한다면 어떻게 바뀌어도 문제가 생기지 않는다. 
- 즉, 옵저버 패턴을 사용하면 느슨하게 결합되어 있어 유연한 설계를 할 수 있다. 

## 5. 설계하기 
			
![](https://velog.velcdn.com/images/dev_kickbell/post/e3fabcc7-f379-4e85-9825-054808227237/image.png)			

## 6. 구현하기 
- Subject 인터페이스를 구현한다. 옵저버등록/삭제/이벤트알림 기능이 있다. 
- Observer 인터페이스를 구현한다. Subject에서 데이터가 변하면 업데이트 하는 기능이 있다. 
- 데이터가 변하면 화면에 표시해주기 위한 DisplayElement 인터페이스도 있다. 
- WeatherData는 Subject 프로토콜을 준수한다. observers 배열을 가지고 있어, 옵저버를 구독하고 해지하고 있다. 
- 또 WeatherData는 measurementsChanged() 에서 데이터가 변경되면 notifyObservers()를 호출한다. 그러면 가지고 있는 observers 배열을 통해 for 문으로 update() 메소드를 호출한다. 
- 각 CurrentConditionsDisplay, PressureDisplay는 Observer, DisplayElement 인터페이스를 준수한다. 이로써 옵저버가 될 자격을 갖는다. 
- 중요한 점은 상속이 아니라 Composition으로 WeatherData를 변수로 갖고 있다는 점이다. 이 변수를 통해 옵저버로 등록한다. 			

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
protocol Subject {
    func registerObserver(ob: Observer)
    func removeObserver(ob: Observer)
    func notifyObservers()
}

protocol Observer {
    func update(_ temp: Float,_ humidity: Float,_ pressure: Float)
}

protocol DisplayElement {
    func display()
}
```
```swift
class WeatherData: Subject {
    
    // MARK: - Properties

    private var temp: Float? //기온
    private var humidity: Float? //습도
    private var pressure: Float? //압력
    private var observers: [String: Observer]
    
    init() {
        self.observers = [:]
    }
    
    // MARK: - Subject

    func registerObserver(ob: Observer) {
        observers[String(describing: ob.self)] = ob
        print("\(String(describing: ob.self))의 구독 등록되었습니다.")
    }
    
    func removeObserver(ob: Observer) {
        if let result = observers.removeValue(forKey: String(describing: ob.self)) {
            print("성공적으로 \(result)의 구독이 해지되었습니다.")
        } else {
            print("\(String(describing: ob.self))를 찾을 수 없습니다. 구독해지 요청이 실패했습니다.")
        }
    }
    
    func notifyObservers() {
        guard let temp = self.temp,
              let humidity = self.humidity,
              let pressure = self.pressure else { return }
        for observer in observers.values {
            observer.update(temp, humidity, pressure)
        }
    }
    
    
    // MARK: - 기온, 습도, 압력 데이터가 변경되면 호출되는 메소드

    func measurementsChanged() {
        notifyObservers()
    }
    
    // MARK: - 감지 센서로부터 데이터를 받아오는 부분
    
    func setMeasurements(_ temp: Float,_ humidity: Float,_ pressure: Float) {
        self.temp = temp
        self.humidity = humidity
        self.pressure = pressure
        measurementsChanged()
    }
}
```
```swift
class CurrentConditionsDisplay: Observer, DisplayElement {
    
    private var weatherData: WeatherData
    private var temp: Float?
    private var humidity: Float?
    
    init(_ weatherData: WeatherData) {
        self.weatherData = weatherData
        weatherData.registerObserver(ob: self)
    }
    
    func update(_ temp: Float, _ humidity: Float, _ pressure: Float) {
        self.temp = temp
        self.humidity = humidity
        display()
    }
    
    func display() {
        print("현재 상태: 온도 \(self.temp ?? 0.0)F, 습도 \(self.humidity ?? 0.0)%")
    }
}


class PressureDisplay: Observer, DisplayElement {
    
    let weatherData: WeatherData
    var pressure: Float?
    
    init(_ weatherData: WeatherData) {
        self.weatherData = weatherData
        weatherData.registerObserver(ob: self)
    }
    
    func update(_ temp: Float, _ humidity: Float, _ pressure: Float) {
        self.pressure = pressure
        display()
    }
    
    func display() {
        print("현재 기압: \(self.pressure ?? 0.0)hPa")
    }
}
```
```swift
let weatherData = WeatherData()

//구독 등록
let c = CurrentConditionsDisplay(weatherData)
let p = PressureDisplay(weatherData)

weatherData.setMeasurements(80, 65, 30)
weatherData.setMeasurements(82, 70, 29)
weatherData.setMeasurements(78, 90, 30)

//구독 해지
weatherData.removeObserver(ob: c)

weatherData.setMeasurements(88, 70, 25)
    
/*
ObserverPattern.CurrentConditionsDisplay의 구독 등록되었습니다.
ObserverPattern.PressureDisplay의 구독 등록되었습니다.
현재 상태: 온도 80.0F, 습도 65.0%
현재 기압: 30.0hPa
현재 상태: 온도 82.0F, 습도 70.0%
현재 기압: 29.0hPa
현재 상태: 온도 78.0F, 습도 90.0%
현재 기압: 30.0hPa
성공적으로 ObserverPattern.CurrentConditionsDisplay의 구독이 해지되었습니다.
현재 기압: 25.0hPa
Program ended with exit code: 0  
*/
```
  </p>
</details>


## 7. 리팩토링 
- 기존 코드의 옵저버 패턴은 Subject가 Observer로 데이터를 보내는 Push 방식이었다. 이 방법은 여러 데이터 중 하나의 데이터만 갱신되도 update() 메소드에 모든 데이터를 보내도록 되어있다. 
- 반대의 방법으로 Observer가 Subject로부터 데이터를 당겨오는 Pull 방식이 있다. 
- 둘 중 하나의 방법으로 구현하는 것은 방법의 차이라고 볼 수 있지만, 대체로 애플리케이션은 계속 바뀌고 점점 더 확장되기 때문에 Pull 방식이 더 좋다고 볼 수 있다. 
- 리팩토링은 먼저 Observer 인터페이스의 매개변수를 없앤다. 그러면 notifyObservers()도 일부 수정해야 한다. 
- 그리고 WeatherData에 getTemp(), getHumidity(), getPressure()의 게터 메소드를 만들어서 각 Display에서 weatherData 변수를 통해 weatherData.getTemp()와 같이 직접 데이터를 Pull 해올 수 있게 수정한다. 

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
protocol Subject {
    func registerObserver(ob: Observer)
    func removeObserver(ob: Observer)
    func notifyObservers()
}

protocol Observer {
    func update()
}
    
protocol DisplayElement {
    func display()
}
```
```swift
class WeatherData: Subject {
    
    // MARK: - Properties

    private var temp: Float? //기온
    private var humidity: Float? //습도
    private var pressure: Float? //압력
    private var observers: [String: Observer]
    
    init() {
        self.observers = [:]
    }
    
    // MARK: - Subject

    func registerObserver(ob: Observer) {
        observers[String(describing: ob.self)] = ob
        print("\(String(describing: ob.self))의 구독 등록되었습니다.")
    }
    
    func removeObserver(ob: Observer) {
        if let result = observers.removeValue(forKey: String(describing: ob.self)) {
            print("성공적으로 \(result)의 구독이 해지되었습니다.")
        } else {
            print("\(String(describing: ob.self))를 찾을 수 없습니다. 구독해지 요청이 실패했습니다.")
        }
    }
    
    func notifyObservers() {
        for observer in observers.values {
            observer.update()
        }
    }
    
    
    // MARK: - 기온, 습도, 압력 데이터가 변경되면 호출되는 메소드

    func measurementsChanged() {
        notifyObservers()
    }
    
    // MARK: - 감지 센서로부터 데이터를 받아오는 부분
    
    func setMeasurements(_ temp: Float,_ humidity: Float,_ pressure: Float) {
        self.temp = temp
        self.humidity = humidity
        self.pressure = pressure
        measurementsChanged()
    }
    
    func getTemp() -> Float {
        return self.temp ?? 0.0
    }
    
    func getHumidity() -> Float {
        return self.humidity ?? 0.0
    }
    
    func getPressure() -> Float {
        return self.pressure ?? 0.0
    }
}
```
```swift
class CurrentConditionsDisplay: Observer, DisplayElement {
    
    private var weatherData: WeatherData
    private var temp: Float?
    private var humidity: Float?
    
    init(_ weatherData: WeatherData) {
        self.weatherData = weatherData
        weatherData.registerObserver(ob: self)
    }
    
    func update() {
        self.temp = weatherData.getTemp()
        self.humidity = weatherData.getHumidity()
        display()
    }
    
    func display() {
        print("현재 상태: 온도 \(self.temp ?? 0.0)F, 습도 \(self.humidity ?? 0.0)%")
    }
}

class PressureDisplay: Observer, DisplayElement {
    
    let weatherData: WeatherData
    var pressure: Float?
    
    init(_ weatherData: WeatherData) {
        self.weatherData = weatherData
        weatherData.registerObserver(ob: self)
    }
    
    func update() {
        self.pressure = weatherData.getPressure()
        display()
    }
    
    func display() {
        print("현재 기압: \(self.pressure ?? 0.0)hPa")
    }
}
```
```swift
let weatherData = WeatherData()

//구독 등록
let c = CurrentConditionsDisplay(weatherData)
let p = PressureDisplay(weatherData)

weatherData.setMeasurements(80, 65, 30)
weatherData.setMeasurements(82, 70, 29)
weatherData.setMeasurements(78, 90, 30)

//구독 해지
weatherData.removeObserver(ob: c)

weatherData.setMeasurements(88, 70, 25)
    
/*
ObserverPattern.CurrentConditionsDisplay의 구독 등록되었습니다.
ObserverPattern.PressureDisplay의 구독 등록되었습니다.
현재 상태: 온도 80.0F, 습도 65.0%
현재 기압: 30.0hPa
현재 상태: 온도 82.0F, 습도 70.0%
현재 기압: 29.0hPa
현재 상태: 온도 78.0F, 습도 90.0%
현재 기압: 30.0hPa
성공적으로 ObserverPattern.CurrentConditionsDisplay의 구독이 해지되었습니다.
현재 기압: 25.0hPa
Program ended with exit code: 0  
*/
```
  </p>
</details>


## 8. 디자인 원칙에 근거한 정리 
1. 애플리케이션에서 달라지는 부분을 찾아내고, 달라지지 않는 부분과 분리한다.
	- 옵저버 패턴에서 변하는 것은 Subject의 상태, Observer의 개수이다. 
    - 새로운 Observer를 추가할 때, Subject는 변하지 않는다. 
2. 구현보다는 인터페이스에 맞춰서 프로그래밍한다.
	- Subject와 Observer에서 모두 protocol을 사용했다. 
3. 상속보다는 구성을 활용한다.
	- 중요한 부분이다. Subject를 준수하는 Display 클래스에서 상속이 아니라 구성으로 WeatherData 객체를 가지고 있다. 
4. 상호작용하는 객체 사이에는 가능하면 느슨한 결합을 사용해야 한다.
	- 인터페이스를 사용하기 때문에 해당 메소드를 구현한다는 것만 알지, 내부의 실제 구현 클래스가 무슨 클래스인지 무엇을 하는지는 모른다. 
    - 그래서 해당 Subject는 다른 곳에서도 재사용이 가능하다. 

## 9. Swift? NotificationCenter? 
- Swift에서 제공하는 [NotificationCenter](https://developer.apple.com/documentation/foundation/nsnotificationcenter)와는 무엇이 다른지? 대체 가능한지? 
## Reference 
[https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223](https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223)
