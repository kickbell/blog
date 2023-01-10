# Testing Tips & Tricks


> _이 글은 [WWDC 2018 - Testing Tips & Tricks](https://developer.apple.com/videos/play/wwdc2018/417)의 내용을 공부하고, 개인적으로 정리해놓은 글입니다._

## 1. 테스트 피라미드
- 테스트가 가장 이상적이려면 각 테스트의 비중이 아래의 피라미드와 같아야 합니다. 
- 테스트에는 3단계가 있죠. 유닛(Unit), 통합(Integration), 종단간(End-to-end) 입니다. 

### 유닛 테스트(Unit Test)
- Unit 테스트는 앱의 수많은 로직 Unit들이 정상적으로 동작하는지 테스트하는 거에요. 계산기는 잘동작하는지, 텍스트필드에 유효하지 않은 글자가 입력되진 않았는지처럼 각 기능들에 대한 정상동작 여부 테스트지요. 
- 그래서 이상적인 피라미드에서 Unit 테스트는 가장 기초가 되고 가장 많은 양을 가지고 있어요. 
		
![](https://velog.velcdn.com/images/dev_kickbell/post/b04c03dc-d6ff-4788-9dc3-7f694d71e72a/image.png)
		
### 통합 테스트(Integration Test)
- 통합테스트는 계산기, 텍스트필드 같은 것들이 Unit 단위로 테스트가 잘 되었으니 그걸 합쳐서 통합으로 테스트를 해보는 겁니다. 좀 더 큰 개념이죠. 
- Unit 테스트에서는 찾지 못했던 버그들을 찾을 수 있어요. 예를 들어, 아래의 그림과 같은 것이죠. 창문(Unit)에는 이상이 없는데 다른 창문에 걸려서 결국에 그 창문을 열 수가 없는 그런 이상한 상황말이죠. 
- 정리하면, 단위 테스트가 끝난 모듈을 통합하는 과정에서 발생할 수 있는 오류를 찾는 것이라고 할 수 있겠습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/cf9e707c-4d38-4a0a-b9ba-e51e9d9f6078/image.png)

### 종단간 테스트(End-to-end Test)
- 마지막으로 종단간 테스트란 사용자의 입장에서 처음부터 끝까지 어플리케이션의 흐름을 테스트하는 겁니다. 실제로 사용하듯이 버튼을 눌러서 화면을 이동하고, 값을 입력하고 하는 것이죠. 통상적으로 iOS에서는 UI Test라고 보시면 될 것 같아요. 
- 실행하는시간이 E2E > 통합 > 유닛 순으로 가장 오래걸리지만 실제 시뮬레이션이 되면서 테스트하기 때문에 모든 것이 올바르게 동작한다는 정확성은 더 높겠죠. 
- 하지만, 실무에서는 실제로 앱에서 UI가 자주바뀌기도하고 디버깅하기가 어려운 부분이 있기 때문에 유지보수하기가 더 어렵습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/16ab79da-a88b-4164-8daa-dbfe20836065/image.png)


## 2. 네트워킹 코드 테스트하기
- 아래의 그림은 네트워킹을 요청하는 대략적인 흐름입니다. 앱에서 요청을 만들고 서버에 요청하고, 응답을 받아서 JSON같은 데이터를 파싱하고 그것을 앱에 보여주는 것이죠. 
- 개선 전의 코드와 개선 후의 코드를 보여주면서 할건데 아래의 코드는 이런 모든 작업이 한 곳에서 수행하는 예시 입니다.
- 테스트 피라미드를 기준으로 살펴보면 우리가 하고싶은 것은 저 4단계의 각 부분에 대한 단위 테스트를 작성하는 겁니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/3ffbbbd8-9b01-455f-8bce-bc679f968522/image.png)

```swift
func loadData(near coord: CLLocationCoordinate2D) {
    let url = URL(string: "/locations?lat=\(coord.latitude)&long=\(coord.longitude)")!
    URLSession.shared.dataTask(with: url) { data, response, error in
        guard let data = data else { self.handleError(error); return }
        do {
            let values = try JSONDecoder().decode([PointOfInterest].self, from: data)
            DispatchQueue.main.async {
                self.tableValues = values
                self.tableView.reloadData()
            }
        } catch {
            self.handleError(error)
        }
    }.resume()
}
```

### 코드 개선하기 1(요청준비, 파싱부분 -> Unit Test)
- 흐름의 4단계 중에 일단 요청준비와 파싱 부분에 먼저 초점을 맞춰보겠습니다. 
- 여기서 할 일은 요청준비와 파싱부분을 유닛 테스트로 바꿔주는 겁니다. 
- 일단 코드를 테스트하기 쉽게 바꿔볼게요. 테스트하기 쉬우려면 return 값이 있어야 합니다. 그래야 얻어온 리턴값과 예상값을 비교할 수 있기 때문이죠. 
- 뷰컨트롤러에서 코드를 꺼내서 요청을 만드는 부분과 파싱하는 부분을 2개의 메소드로 나눠줍니다. 
- 이렇게 하면 테스트코드에서 PointsOfInterestRequest의 인스턴스를 만들어서 유닛 테스트를 하는 것이 엄청 간단해지죠. 
- 이 테스트에 대해 주목해야 할 또 다른 점은 throws로 표시된 테스트 메서드에 대한 XCTest를 활용하여 do catch 블록 없이 테스트 코드에서 try를 사용할 수 있다는 겁니다.


![](https://velog.velcdn.com/images/dev_kickbell/post/944dc71b-d5f5-4a5f-973e-32c758b93c9a/image.png)


```swift
// 테스트 하기 쉽게 뷰컨트롤러에서 리턴값을 반환하도록 메소드로 나누기 
struct PointsOfInterestRequest {
    func makeRequest(from coordinate: CLLocationCoordinate2D) throws -> URLRequest {
        guard CLLocationCoordinate2DIsValid(coordinate) else {
            throw RequestError.invalidCoordinate
        }
        
        var components = URLComponents(string: "https://example.com/locations")!
        components.queryItems = [
            URLQueryItem(name: "lat", value: "\(coordinate.latitude)"),
            URLQueryItem(name: "long", value: "\(coordinate.longitude)")
            }
        ]
        return URLRequest(url: components.url!)
    }
    
    func parseResponse(data: Data) throws -> [PointOfInterest] {
        return try JSONDecoder().decode([PointOfInterest].self, from: data)
    }
}

// 코드 테스트 
class PointOfInterestRequestTests: XCTestCase {
    let request = PointsOfInterestRequest()
    
    func testMakingURLRequest() throws {
        let coordinate = CLLocationCoordinate2D(latitude: 37.3293, longitude: -121.8893)
        let urlRequest = try request.makeRequest(from: coordinate)
        //¹Assertion(어썰션)
        XCTAssertEqual(urlRequest.url?.scheme, "https")
        XCTAssertEqual(urlRequest.url?.host, "example.com")
        XCTAssertEqual(urlRequest.url?.query, "lat=37.3293&long=-121.8893")
    }
    
    func testParsingResponse() throws {
        let jsonData = "[{\"name\":\"My Location\"}]".data(using: .utf8)!
        let response = try request.parseResponse(data: jsonData)
        XCTAssertEqual(response, [PointOfInterest(name: "My Location")])
    }    
}
```

### 코드 개선하기 2(URL 세션 생성부분 -> Integration Test)
- 아래 그림을 보면 좀 헷갈릴 수 있습니다. 사실 저도 명확히는 잘 몰라요. 하지만 핵심만 설명을 해볼게요. 필요없는 것은 뺄게요. 
- 자, 우리가 많이 쓰는 URLSession이 있죠. URLSession은 앱이 네트워크 요청을 수행하는 데 사용할 API를 제공하죠. 그리고 그 하위에 URLProtocol 클래스의 하위 클래스들이 있지요. 
- 그리고 URLProtocol 클래스는 URLProtocolClient 프로토콜을 통해 네트워킹의 진행 상황을 시스템에 다시 전달할 수 있어요.
- 자 그러면 여기서 트릭이 뭐냐면, URLProtocol을 상속하는 mock 클래스를 만듭니다. 그리고 실제 앱에서 사용하는 URLSession이랑 같은 애를 주입받는 거에요. 
- 그리고 나서 MockURLProtocol에서 네트워킹 관련 메소드를 오버라이드 하는 겁니다. 그러면, 우리는 테스트 번들에서도 네트워킹 요청을 보내볼 수 있게 되는 겁니다. 
- 초간단하게 정리하자면, MockURLProtocol 클래스에서 URLProtocol를 재정의해서 테스트 번들에서도 네트워크 요청을 할 수가 있다는 겁니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/1106ad2b-e310-4ffd-bdf2-72693d553e46/image.png)

- 코드를 변경합니다. 이전 코드에서 메소드로 빼냈던 makeRequest()와 parseResponse()를 APIRequest라는 프로토콜로 만들어줍니다. 
- 그리고 APIRequestLoader 클래스를 만들어주고 프로토콜을 준수합니다.
- 중요한 부분은 urlSession을 생성자 주입으로 해줬다는 거에요. 

```swift
protocol APIRequest {
    associatedtype RequestDataType
    associatedtype ResponseDataType
    
    func makeRequest(from data: RequestDataType) throws -> URLRequest
    func parseResponse(data: Data) throws -> ResponseDataType
}

class APIRequestLoader<T: APIRequest> {
    let apiRequest: T
    let urlSession: URLSession
    
    init(apiRequest: T, urlSession: URLSession = .shared) {
        self.apiRequest = apiRequest
        self.urlSession = urlSession
    }
    
    func loadAPIRequest(requestData: T.RequestDataType,
                        completionHandler: @escaping (T.ResponseDataType?, Error?) -> Void) {
        do {
            let urlRequest = try apiRequest.makeRequest(from: requestData)
            urlSession.dataTask(with: urlRequest) { data, response, error in
                guard let data = data else { return completionHandler(nil, error) }
                do {
                    let parsedResponse = try self.apiRequest.parseResponse(data: data)
                    completionHandler(parsedResponse, nil)
                } catch {
                    completionHandler(nil, error)
                }
            }.resume()
        } catch { return completionHandler(nil, error) }
    }
}
```

- 그리고 위에서 말했던 것처럼 URLProtocol을 준수하는 MockURLProtocol을 만들겁니다.
- 먼저 canInit을 해서 테스트 번들에서도 시스템에서 요청하는 네트워킹에 관심이 있다고 true를 리턴해줍니다. 
- 그리고 테스트가 이 URLProtocol에 연결하는 방법을 제공하기 위해 테스트가 설정할 클로저 속성 requestHandler를 만들어줍니다.
- 다음은 이제 startLoading 메소드를 오버라이드 해줄건데, 네트워킹 요청할 때는 startLoading, 요청 취소를 수행할 때는 stopLoading 메소드를 구현하면 됩니다.
- 아까 뭐랬죠? 네트워킹이 시작되면 URLProtocol은 URLProtocolClient 프로토콜을 통해 진행 상황을 시스템에 다시 전달한다고 했어요. 여기가 그 부분입니다. 
- 그래서 아래 코드를 보면 client라고 있는데 얘가 URLProtocolClient 타입이에요. 그리고 메소드를 4개 호출하죠. 각자 주석에 설명이 적혀있는데 얘네들의 역할은 위에서 requestHandler가 (HTTPURLResponse, Data)를 리턴하잖아요? 그것을 받아서 urlProtocol 메소드로 응답 또는 오류를 시스템에 전달하는 겁니다.
- 만약에 테스트 요청 취소를 하려면 경우 stopLoading 메서드를 구현하면 됩니다. 

```swift
class MockURLProtocol: URLProtocol {
    override class func canInit(with request: URLRequest) -> Bool {
        return true
    }
    
    override class func canonicalRequest(for request: URLRequest) -> URLRequest {
        return request
    }
    
    static var requestHandler: ((URLRequest) throws -> (HTTPURLResponse, Data))?
    
    override func startLoading() {
        guard let handler = MockURLProtocol.requestHandler else {
            XCTFail("Received unexpected request with no handler set")
            return
        }
        do {
            let (response, data) = try handler(request)
            //프로토콜 구현이 요청에 대한 응답 개체를 생성했음을 클라이언트에 알립니다.            
            client?.urlProtocol(self, didReceive: response, cacheStoragePolicy: .notAllowed)
            //프로토콜 구현이 일부 데이터를 로드했음을 클라이언트에 알립니다.
            client?.urlProtocol(self, didLoad: data)
            //프로토콜 구현이 로드를 완료했음을 클라이언트에 알립니다.
            client?.urlProtocolDidFinishLoading(self)
        } catch {
	        //오류로 인해 로드 요청이 실패했음을 클라이언트에 알립니다.
            client?.urlProtocol(self, didFailWithError: error)
        }
    }
    
    override func stopLoading() {
        
    }
}
```

- 자, 이제 만들거 만들었으니 테스트 해줘야 겠죠? 테스트 클래스를 위한 클래스를 만듭니다. XCTestCase를 상속하고 setUp() 메소드를 오버라이드 합니다. 얘는 테스트 시작 전에 초기화 해줄 것이 있거나 하면 작성하는 메소드지요. 
- 여기서 URLSession을 만들건데, 중요한 점은 URLSessionConfiguration에 MockURLProtocol.self가 들어간다는 거겠죠. 
- 그리고 만든 URLSession으로 APIRequestLoader 클래스의 인스턴스를 생성하고 loader 변수에 할당합니다. 
- 자, 그리고 여기서부터 잘보세요. 중요! 실행순서를 잘봐야됩니다. 일단 첫번째는 일단 테스트를 위해 값 두개를 만들죠. 좌표값하고 JSONData 입니다. 
- 그리고 MockURLProtocol.requestHandler를 지나가죠? 얘의 리턴값은 (HTTPURLResponse, Data))? 입니다. 근데 클로저니까 일단 나중에 실행되는거니 쭉 지나가요. 
- 그리고 loader.loadAPIRequest를 실행합니다. loader에는 위에 setUp에서 초기화를 다 해뒀죠. 그러면 어떻게 되느냐. 리퀘스트를 했으니 네트워킹을 내보내야 되잖아요. 그럼 inputCoordinate를 바탕으로 리퀘스트 요청이 시작되고, URLSession에 MockURLProtocol이 있으니까 MockURLProtocol 내부의 startLoading() 메소드가 호출되겠죠. 얘는 startLoading() 이니까 나가는 요청이에요. 그래서 나가는 쿼리에 lat=37.3293이 포함됐는지 테스트를 하는겁니다. 
- 그리고 다음에 클로저의 리턴값이 (HTTPURLResponse, Data))?니까 거기의 Data 타입이 넣어질 곳에 우리가 생성한 mockJSONData을 넣어줍니다. Data타입으로요. 
- 마지막으로 loadAPIRequest에서 성공했다면 Data타입인 mockJSONData가 파싱이 되서 돌아왔겠죠? 실패했으면 에러가 왔겠구요. 그 결과값이랑 mockJSONData에 넣어준 데이터인 [PointOfInterest(name:"MyPointOfInterest")]이랑 같다면 요청이 성공한거겠죠. 
- 참고로 fulfill()은 비동기메소드를 테스트할 때 사용되는 메소드죠.

```swift
class APILoaderTests: XCTestCase {
    var loader: APIRequestLoader<PointsOfInterestRequest>!
    
    //테스트 전에 초기화되는 메소드 
    override func setUp() {
        let request = PointsOfInterestRequest()
        let configuration = URLSessionConfiguration.ephemeral
        configuration.protocolClasses = [MockURLProtocol.self]
        let urlSession = URLSession(configuration: configuration)
    }

    func testLoaderSuccess() {
        //실행순서 1
        //테스트를 위해 생성한 값
        let inputCoordinate = CLLocationCoordinate2D(latitude: 37.3293, longitude: -121.8893)
        let mockJSONData = "[{\"name\":\"MyPointOfInterest\"}]".data(using: .utf8)!
        
        //실행순서 2
        //MockURLProtocol에 requestHandler를 설정하여 나가는 요청에 대한 테스트
        MockURLProtocol.requestHandler = { request in
            //실행순서 4
            XCTAssertEqual(request.url?.query?.contains("lat=37.3293"), true)
            return (HTTPURLResponse(), mockJSONData)
        }
        
        //loadAPIRequest를 호출하고 완료 블록이 호출되기를 기다려서 온 응답에 대한 테스트
        let expectation = XCTestExpectation(description: "response")
        //실행순서 3
        loader.loadAPIRequest(requestData: inputCoordinate) { pointsOfInterest, error in
            //실행순서 5
            XCTAssertEqual(pointsOfInterest, [PointOfInterest(name: "MyPointOfInterest")])
            expectation.fulfill()
        }
        wait(for: [expectation], timeout: 1)
    }
}
```
- 정리를 하자면, 우리는 makeRequest()와 parseResponse()로 유닛테스트를 만들었죠. 얘네 둘은 잘 동작했어요. 그런데 이게 통합테스트하고는 무슨 상괸일까요? 
- 이런 의미입니다. 내가 만든 이 유닛 테스트가 훨씬 더 큰 모듈인 URLSession API에서 상호 작용을 잘하고 있는가? 에 대한 테스트를 원했던 거죠. 
- 위에서 말했던 것처럼 통합 테스트는 단위 테스트가 끝난 모듈을 통합하는 과정에서 발생할 수 있는 오류를 찾는 것이라고 했었죠. 
- 그런데 만약에 여기서 task.resume()을 호출하지 않았으면 유닛 테스트가 통과했음에도 통합테스트는 실패했겠죠. 애초에 네트워크 요청이 안갔을테니까요. 그런겁니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/19b3910c-e897-482d-96c6-d82a3bd23af2/image.png)

### 종단간 테스트(End-to-end Test)
- E2E 테스트를 포함하는 것도 유용합니다. 실질적으로는 UI 테스트지요. 
- 하지만 UI 테스트를 시작할 때의 문제는 테스트 실패가 발생했을 때, 문제의 원인을 찾기 시작하는 위치를 알기가 정말 어렵다는 것입니다.
- 이를 해결하기 위한 방법 한 가지는 MockServer의 로컬 인스턴스를 설정하여 UI 테스트를 중단하여 실제 서버 대신 해당 인스턴스에 대한 요청을 만드는 겁니다.
- 이렇게 하면 앱으로 피드백되는 데이터를 제어할 수 있기 때문에 UI 테스트를 훨씬 더 안정적으로 만들 수 있습니다. 자세한 내용은 아래의 세션에서 확인하시기 바랍니다.
- [UI Testing in Xcode](https://developer.apple.com/videos/play/wwdc2015/406)

![](https://velog.velcdn.com/images/dev_kickbell/post/9b35bed5-a293-4546-8692-747a2fbdd228/image.png)




## 2. 노티피케이션을 테스트하기 위한 팁

### 문제를 인식하기
- 노티피케이션은 잘 등록했는지도 테스트해야 하지만, 반대로 잘 송출되는지도 테스트해야 합니다.
- 노티피케이션은 1:N 통신 매커니즘이므로 여러 수신자에게 알림이 전송될 수 있습니다. 따라서 의도하지 않은 부작용을 피하기 위해 노티피케이션을 항상 독립된 방식으로 테스트하는 것이 중요합니다. 
- 아래는 문제가 있는 코드의 예시입니다. 가볼만한 곳을 테이블뷰로 표시하는 컨트롤러입니다. 앱의 위치 인증이 변경될 때마다 데이터를 다시 로딩할 수 있고, 생성자에서 노티피케이션을 등록하고 변경되면 handleAuthChanged()를 호출합니다. 노티가 실제로 수신되었는지를 확인하기위해 flag값으로 didHandleNotification 변수를 두고 있습니다. 
- 테스트 코드는 잘 동작하지만 문제는 테스트 코드에서도 뷰컨트롤러에서 사용하는 것과 동일한 NotificationCenter.default 를 사용한다는 것입니다. 이러면 알 수 없는 사이드이펙트가 발생할 확률이 높습니다.

```swift
//애플리케이션 코드 
class PointsOfInterestTableViewController {
    var observer: AnyObject?
    init() {
        let name = CurrentLocationProvider.authChangedNotification
        observer = NotificationCenter.default.addObserver(forName: name, object: nil,
                                                          queue: .main) { [weak self] _ in
            self?.handleAuthChanged()
        }
    }
    
    var didHandleNotification = false
    
    func handleAuthChanged() {
        didHandleNotification = true
    }
}

//테스트 코드 
class PointsOfInterestTableViewControllerTests: XCTestCase {
    func testNotification() {
        let observer = PointsOfInterestTableViewController()
        XCTAssertFalse(observer.didHandleNotification)
        // This notification will be received by all objects in process,
        // could have unintended consequences
        let name = CurrentLocationProvider.authChangedNotification
        NotificationCenter.default.post(name: name, object: nil)
        XCTAssertTrue(observer.didHandleNotification)
    }
}
```

- 예를 들어, 우리가 자주사용하는 didFinishLaunchingNotification 와 같은 경우입니다. App의 라이프싸이클에서 앱이 켜지면 어떤 이벤트를 하기 위해 우리는 func application(_ application:, didFinishLaunchingWithOptions:) 메소드를 자주 사용하죠. 그리고 이 이벤트가 발생하면 실행되는 것이 didFinishLaunchingNotification 입니다. 
- 그런데, 이 녀석을 온갖 뷰컨트롤러에서 여러 곳에 등록하고 테스트 코드에서도 등록하면 알 수 없는 사이드이펙트가 있거나 테스트 속도가 느려지거나 할 수 있습니다. 그래서 이것을 테스트하기 위해 분리해야 합니다. 

```swift
@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Override point for customization after application launch.
        return true
    }
}

NotificationCenter.default.addObserver(forName: UIApplication.didFinishLaunchingNotification,
                                       object: self, queue: nil) { _ in ... }

```


### 옵저버가 노티피케이션 센터에 구독을 잘했는지 확인하는 테스트 
- 테스트를 잘 격리하는데 사용할 수 있는 기술이 있습니다.
- 이를 사용하려면 먼저 NotificationCenter가 여러 인스턴스를 가질 수 있음을 인식해야 합니다. 노티피케이션은 클래스 인스턴스로 Notification.default라는 기본 인스턴스가 있지만 필요할 때마다 추가 인스턴스 생성을 지원하고 이것은 테스트를 격리하는데 핵심입니다.
- 따라서 이 기술을 적용하려면 먼저 새 NotificationCenter를 생성하고 이를 subject에 전달한 다음 Notification.default 인스턴스 대신 사용해야 합니다. 이것을 의존성 주입이라고 합니다.
- 기존 코드를 개선해보겠습니다. NotificationCenter.default로 생성했던 부분을 의존성 주입으로 외부에서 주입받도록 바꿔줍니다. 그리고 기본값을 .default를 줄겁니다. 이것은 애플리케이션과 테스트 코드에서의 인스턴스를 분리하기 위함입니다. 기존 앱의 코드에서 PointsOfInterestTableViewController를 호출할 때, 기존과 똑같이 notificationCenter 매개변수를 입력하지 않게 해서 다른 에러가 발생하지 않도록 하는 의도도 있구요. 
- 테스트 코드를 보겠습니다. 여기서도 마찬가지로 별도의 NotificationCenter() 인스턴스를 만듭니다. 그리고 그것을 뷰컨트롤러를 생성할 때 주입해줍니다. 그러면 뷰컨트롤러의 노티와 테스트 코드의 노티는 다른애인거죠. 참조값이 다를테니까요. 성공적으로 분리되어 테스트 할 수 있습니다.


```swift
//노티피케이션에는 클래스 타입 인스턴스로 `default`가 이미 존재한다.
//하지만 싱글톤 같은 것이 아니므로 추가 인스턴스 생성을 지원한다. 
open class NotificationCenter : NSObject {
    open class var `default`: NotificationCenter { get }
}
```
```swift
//애플리케이션 코드 
class PointsOfInterestTableViewController {
    let notificationCenter: NotificationCenter
    var observer: AnyObject?
    init(notificationCenter: NotificationCenter = .default) {
        self.notificationCenter = notificationCenter
        let name = CurrentLocationProvider.authChangedNotification
        observer = notificationCenter.addObserver(forName: name, object: nil,
                                                          queue: .main) { [weak self] _ in
            self?.handleAuthChanged()
        }
    }
    
    var didHandleNotification = false
    
    func handleAuthChanged() {
        didHandleNotification = true
    }
}

//옵저버가 노티피케이션 센터에 구독을 잘했는지 확인하는 테스트
class PointsOfInterestTableViewControllerTests: XCTestCase {
    func testNotification() {
        let notificationCenter = NotificationCenter()
        let observer = PointsOfInterestTableViewController(notificationCenter: notificationCenter)
        
        //현재 뷰컨의 flag가 노티가 송출되기전인 false인지 테스트
        XCTAssertFalse(observer.didHandleNotification)
        
        //Notification posted to just this center, isolating the test
        let name = CurrentLocationProvider.authChangedNotification
        notificationCenter.post(name: name, object: nil)
        
        //노티를 위에서 post했으므로 flag값이 true로 잘바뀌었는지 테스트 
        //구독이 잘됐다면 flag값이 바뀌었을 것임. 
        XCTAssertTrue(observer.didHandleNotification)
    }
}
```

### 노피티케이션 센터에서 이벤트를 잘 post했는지를 확인하는 테스트 
- CurrentLocationProvider 클래스가 있습니다. 그리고 내부에는 notifyAuthChanged() 라는 노티피케이션을 post하여 앱의 위치 인증이 변경되었음을 내 앱의 다른 클래스에 알리는 이 메서드가 있습니다. 
- 그리고 테스트 코드가 있습니다. notifyAuthChanged() 메서드가 호출될 때마다 알림을 post하는지 확인하고 addObserver 메서드를 사용하여 observer를 할당하고 클로저 내부에서 해당 observer를 제거하고 있습니다. 

```swift
//애플리케이션 코드
class CurrentLocationProvider {
    static let authChangedNotification = Notification.Name("AuthChanged")
    
    func notifyAuthChanged() {
        let name = CurrentLocationProvider.authChangedNotification
        NotificationCenter.default.post(name: name, object: self)
    }
}

//테스트 코드
class CurrentLocationProviderTests: XCTestCase {
    func testNotifyAuthChanged() {
        let poster = CurrentLocationProvider()
        var observer: AnyObject?
        let expectation = self.expectation(description: "auth changed notification")
        
        //   Uses default NotificationCenter, not properly isolating test
        let name = CurrentLocationProvider.authChangedNotification
        observer = NotificationCenter.default.addObserver(forName: name, object: poster,
                                                          queue: .main) { _ in
            NotificationCenter.default.removeObserver(observer!)
            expectation.fulfill()
        }
        
        poster.notifyAuthChanged()
        wait(for: [expectation], timeout: 0)
    }
}  
```

- 코드를 개선해보겠습니다. 일단 방금 말한 observer를 할당하고 제거하는 코드는 내부 API가 지원됩니다. XCTNSNotificationExpectation를 사용하면 기존 observer 인스턴스를 삭제하고 한 줄로 요약할 수 있어요. 
- 다음으로 이전 테스트와 유사하게 의존성 주입을 사용해서 코드를 변경해줍니다. 좋은 점은 테스트 코드의 notificationCenter의 인스턴스를 XCTNSNotificationExpectation 생성자에 초기화할 때 전달할 수 있게 지원해줍니다. 
- 또 wait의 timeout이 0입니다. 이것은 왜냐면 바로 위에서 post.notifyAuthChanged() 가 호출 될 때, 해당 메소드 안에서 이미 post를 하므로 이 메소드가 반환되기전에 이미 노티피케이션이 post되어야 한다고 기대하기 때문입니다. 그래서 timeout을 0으로 한겁니다. 
- 여기까지 노티피케이션 테스트가 완전히 격리된 상태로 유지되도록 하는 부분을 살펴봤습니다.

```swift
class CurrentLocationProvider {
    static let authChangedNotification = Notification.Name("AuthChanged")
    
    let notificationCenter: NotificationCenter
    init(notificationCenter: NotificationCenter = .default) {
        self.notificationCenter = notificationCenter
    }
    
    func notifyAuthChanged() {
        let name = CurrentLocationProvider.authChangedNotification
        notificationCenter.post(name: name, object: self)
    }
}

class CurrentLocationProviderTests: XCTestCase {
    func testNotifyAuthChanged() {
        let notificationCenter = NotificationCenter()
        let poster = CurrentLocationProvider(notificationCenter: notificationCenter)
        
        //   Uses default NotificationCenter, not properly isolating test
        let name = CurrentLocationProvider.authChangedNotification
        let expectation = XCTNSNotificationExpectation(name: name, object: poster,
                                                       notificationCenter: notificationCenter)
        
        poster.notifyAuthChanged()
        wait(for: [expectation], timeout: 0)
    }
}
```

## 3. mock 프로토콜로 외부 클래스 테스트하기 

### 개요
- 클래스는 종종 앱 또는 SDK의 다른 클래스와 상호 작용합니다.
- 많은 SDK 클래스를 직접 생성할 수 없습니다. (예를 들어, RxSwift처럼 내 테스트 코드에서 Rx 내부에 있는 클래스를 생성할 수는 없지. 설계가 그렇게 되어 있으니까)
- 그런 SDK 내부의 Delegate 메서드를 테스트 해야 하는 경우는 더 어렵습니다. 
- 해결 방법: 프로토콜을 사용해서 외부 클래스의 mock 인터페이스를 만듭시다. 

### 문제를 인식하기 
- 외부 SDK인 CoreLocation를 사용하는 CurrentLocationProvider 클래스가 있습니다. CLLocationManager 인스턴스를 생성하고, 정확도 수준을 지정하고, delegate = self를 해주었어요.  
- 그리고 중요한 부분이 checkCurrentLocation() 메소드인데요. 아래의 locationManager.requestLocation() 를 호출하면 주석에 나와있는 설명대로 결국에 delegate 메소드를 호출합니다. delegate 메소드에서는 만들어둔 클로저로 위치값이 전송되고, 그게 관심 장소인지에 따라서 Bool타입을 리턴하지요. 
- 테스트 코드를 볼게요. 일단 우리가 만든 CurrentLocationProvider()의 인스턴스를 생성하고 XCTAssertNotEqual, Nil을 사용해서 정확도가 제대로 설정되었는지와 delegate가 잘 위임되었는지를 확인합니다. 나쁘지 않아요. 
- 그런데, 그 다음이 문제입니다. 우리의 핵심로직은 checkCurrentLocation 이잖아요? 하지만 locationManager.requestLocation() 메서드가 언제 호출되는지 알 수 있는 방법이 없습니다. 이는 CLLocationManager의 메서드이고 우리 코드의 일부가 아니기 때문입니다.
- 이 테스트에서 발생할 수 있는 또 다른 문제는 CoreLocation에 사용자 인증이 필요하고 이전에 부여되지 않은 경우 장치에 권한 체크 상자가 표시된다는 것입니다. 이로 인해 테스트가 장치 상태에 의존하게 됩니다. 유지 관리가 더 어려워지고 궁극적으로 실패할 가능성이 높아집니다. 

```swift
import CoreLocation

//애플리케이션 코드 
class CurrentLocationProvider: NSObject {
    
    let locationManager = CLLocationManager()
    override init() {
        super.init()
        self.locationManager.desiredAccuracy = kCLLocationAccuracyHundredMeters //정확도 설정
        self.locationManager.delegate = self
    }
    
    var currentLocationCheckCallback: ((CLLocation) -> Void)?
    func checkCurrentLocation(completion: @escaping (Bool) -> Void) {
        self.currentLocationCheckCallback = { [unowned self] location in
            completion(self.isPointOfInterest(location))
        }
        // 이 메서드를 호출하면 위치를 얻고 그 결과로 delegate 메소드를 호출합니다.
        //하나의 위치 수정만 delegate에게 보고되며 그 이후에는 위치 서비스가 중지됩니다.
        //위치 수정을 결정할 수 없는 경우 위치 관리자는 delegate의 메서드를 대신 호출하고 오류를 보고합니다
        locationManager.requestLocation()
    }
    
    func isPointOfInterest(_ location: CLLocation) -> Bool {
        //전달받은 location가 관심 장소면 true 아니면 false
        return location == CLLocation(latitude: .zero, longitude: .zero) ? true : false
    }
}

extension CurrentLocationProvider: CLLocationManagerDelegate {
    func locationManager(_ manager: CLLocationManager, didUpdateLocations locs: [CLLocation]){
        guard let location = locs.first else { return }
        self.currentLocationCheckCallback?(location)
        self.currentLocationCheckCallback = nil
    }
}


//테스트 코드 
class CurrentLocationProviderTests: XCTestCase {
    func testCheckCurrentLocation() {
        let provider = CurrentLocationProvider()
        
        XCTAssertNotEqual(provider.locationManager.desiredAccuracy, 0)
        XCTAssertNotNil(provider.locationManager.delegate)
        
        let completionExpectation = expectation(description: "completion")
        provider.checkCurrentLocation { isPointOfInterest in
            XCTAssertTrue(isPointOfInterest)
            completionExpectation.fulfill()
        }
        //No way to mock the current location or confirm requestLocation() was called
        wait(for: [completionExpectation], timeout: 1)
    }
}
```

### 외부 클래스 서브클래싱, 오버라이딩은 해결책이 아니다.   
- 만약 과거에 이 문제가 있었다면 외부 클래스를 서브클래싱하고 호출하는 모든 메서드를 재정의하는 것을 고려했을 수 있습니다.
- 예를 들어 여기에서 CLLocationManager를 서브클래싱하고 RequestLocation 메서드를 재정의할 수 있습니다. 처음에는 효과가 있을지 모르지만 위험합니다. SDK의 일부 클래스는 하위 클래스로 설계되지 않았으며 다르게 작동할 수 있습니다.
- 게다가 우리는 여전히 슈퍼클래스의 이니셜라이저를 호출해야 하며 이는 우리가 오버라이드할 수 있는 코드가 아닙니다.
- 그러나 주요 문제는 CLLocationManager에서 다른 메서드를 호출하도록 내 코드를 수정한 경우 테스트 하위 클래스에서도 해당 메서드를 재정의해야 한다는 점입니다.
- 서브클래싱에 의존하는 경우 컴파일러는 내가 다른 메서드를 호출하기 시작했음을 알리지 않으며 내 테스트를 잊고 중단하기 쉽습니다. 따라서 저는 이 방법을 권장하지 않으며 대신 프로토콜을 사용하여 외부 유형을 조롱합니다.
		
![](https://velog.velcdn.com/images/dev_kickbell/post/5e83047b-5eaf-4f13-9d87-b7d7941cc95e/image.png)

### 코드 개선하기 (mock protocol)
- LocationFetcher 프로토콜을 새로 생성합니다. 여기에는 CLLocationManager에서 사용하는 정확한 메서드 및 속성 집합이 포함되어 있으므로 빈 extension을 만들 수 있죠. 
- 그러면 다형성에 의해서 기존의 LocationManager 인스턴스를 이름을 LocationFetcher로 바꿔줄 수 있어요. 타입도 마찬가지죠. 기존 앱 코드가 손상되지 않도록 기본값도 넣어줍니다. 
- checkCurrentLocation 메서드도 locationFetcher로 바꿔줍니다. 

```swift
//프로토콜 생성 
protocol LocationFetcher {
    var delegate: CLLocationManagerDelegate? { get set }
    var desiredAccuracy: CLLocationAccuracy { get set }
    func requestLocation()
}
extension CLLocationManager: LocationFetcher {}

//코드 변경 
import CoreLocation

class CurrentLocationProvider: NSObject {
    
    var locationFetcher: LocationFetcher
    init(locationFetcher: LocationFetcher = CLLocationManager()) {
        self.locationFetcher = locationFetcher
        super.init()
        self.locationFetcher.desiredAccuracy = kCLLocationAccuracyHundredMeters //정확도 설정
        self.locationFetcher.delegate = self
    }
    
    var currentLocationCheckCallback: ((CLLocation) -> Void)?
    func checkCurrentLocation(completion: @escaping (Bool) -> Void) {
        self.currentLocationCheckCallback = { [unowned self] location in
            completion(self.isPointOfInterest(location))
        }
        locationFetcher.requestLocation()
    }
}
```

- 다음은 delegate 부분인데요. 여기는 좀 까다롭습니다. 왜냐면, delegate는 manager: CLLocationManager가 될 것으로 예상하기 때문이죠. 외부 SDK 코드이기 때문에 여기를 방금 만든 LocationFetcher로 바꿔줄 수 없어서 그렇습니다. 

```swift
extension CurrentLocationProvider: CLLocationManagerDelegate {
    func locationManager(_ manager: CLLocationManager, didUpdateLocations locs: [CLLocation]){
        guard let location = locs.first else { return }
        self.currentLocationCheckCallback?(location)
        self.currentLocationCheckCallback = nil
    }
}
```

- 하지만 까다로울뿐 방법이 없는 것은 아닙니다. LocationFetcherDelegate 라는 프로토콜을 새롭게 정의하고 CLLocationManagerDelegate의 메소드와 거의 유사한 메소드를 만듭니다. 이름은 다르지요. 
- 그리고 LocationFetcher 프로토콜로 돌아가서 delegate 인스턴스를 지금 만든 것으로 바꿔줍니다. 
- 그러면 에러가 발생할건데, 이름이 다르니 extension에서 LocationFetcherDelegate 속성을 구현해줘야 합니다. 
- 그리고 강제 캐스팅을 사용하여 CLLocationManagerDelegate로 앞뒤로 변환하도록 getter 및 setter를 구현하고 여기서 강제 캐스팅을 사용하는 이유를 곧 설명하겠습니다.

```swift
protocol LocationFetcher {
    var locationFetcherDelegate: LocationFetcherDelegate? { get set }
    var desiredAccuracy: CLLocationAccuracy { get set }
    func requestLocation()
}

protocol LocationFetcherDelegate: AnyObject {
    func locationFetcher(_ fetcher: LocationFetcher, didUpdateLocations locs: [CLLocation])
}

extension CLLocationManager: LocationFetcher {
    var locationFetcherDelegate: LocationFetcherDelegate? {
        get { return delegate as! LocationFetcherDelegate? }
        set { delegate = newValue as! CLLocationManagerDelegate? }
    }
}
```

- 다음은 CurrentLocationProvider의 생성자에서 delegate 속성을 locationFetcherDelegate로 바꿔야 합니다.
- 마지막으로 새로운 mock delegate 프로토콜을 준수하도록 CurrentLocationProvider extension을 변경하는 것입니다. 그 부분은 쉽습니다. 준수하는 프로토콜을 CLLocationManagerDelegate에서 LocationFetcherDelegate으로 메서드 명을 locationManager에서 locationFetcher으로 바꿔주기만 하면 됩니다. 
- 하지만 실제로는 여전히 이전 CLLocationManagerDelegate 프로토콜도 준수해야 합니다. 실제 CLLocationManager가 내 LocationFetcherDelegate에 대해 알지 못하기 때문입니다.
- 따라서 여기서 요령은 CurrentLocationProvider를 확장해서 CLLocationManagerDelegate을 준수해서 locationManager 메서드가 호출될 때, 내부에서 내가 만든 self.locationFetcher를 호출해주는 것입니다. 
- 그리고 위에서 locationFetcherDelegate의 getter 및 setter에서 강제 캐스팅을 사용했다고 언급했는데 이는 내 클래스가 이 두 프로토콜을 준수하고 둘 중 하나를 잊지 않도록 하기 위한 것입니다.

```swift
class CurrentLocationProvider: NSObject {
    var locationFetcher: LocationFetcher
    init(locationFetcher: LocationFetcher = CLLocationManager()) {
// ...
        self.locationFetcher.locationFetcherDelegate = self
    }
}

extension CurrentLocationProvider: LocationFetcherDelegate {
    func locationFetcher(_ fetcher: LocationFetcher, didUpdateLocations locs: [CLLocation]){
        guard let location = locs.first else { return }
        self.currentLocationCheckCallback?(location)
        self.currentLocationCheckCallback = nil
    }
}

extension CurrentLocationProvider: CLLocationManagerDelegate {
    func locationManager(_ manager: CLLocationManager, didUpdateLocations locs: [CLLocation]){
        self.locationFetcher(manager, didUpdateLocations: locs)
    }
}
```

- 다음은 테스트인데요. CurrentLocationProviderTests 내부에 MockLocationFetcher라는 구조체를 만들어줄겁니다. 이 구조체는 locationFetcher 프로토콜을 준수하고 요구 사항을 채웁니다.
- requestLocation 메서드에서 테스트에서 사용자 지정할 수 있는 가짜 위치를 가져오기 위해 클로저를 호출한 다음 delegate 메서드를 호출하여 해당 가짜 위치를 전달합니다.
- 이제 준비가 다 되었으니 테스트를 해보겠습니다.
- testCheckCurrentLocation() 내부에서 방금 만든 MockLocationFetcher()의 인스턴스를 생성합니다. 그리고 handleRequestLocation 클로저를 설정합니다. 그러면 CLLocation(latitude: 37.3293, longitude: -121.8893)라는 가짜위치가 리턴되겠죠. 
- 다음엔 가짜값이 들어있는 MockLocationFetcher()의 인스턴스를 CurrentLocationProvider의 생성자 매개변수로 전달합니다. MockLocationFetcher가 LocationFetcher를 준수하고 있으므로 다형성에 의해서 이게 허용됩니다. 
- provider.checkCurrentLocation 내부에서 관심장소가 맞는지가 리턴되면 그걸로 XCTAssertTrue로 테스트 합니다. 
- 이제 실제 클래스를 생성할 필요 없이 Mock 프로토콜을 사용해서 테스트할 수 있습니다. 

```swift
class CurrentLocationProviderTests: XCTestCase {
    struct MockLocationFetcher: LocationFetcher {
        weak var locationFetcherDelegate: LocationFetcherDelegate?
        var desiredAccuracy: CLLocationAccuracy = 0
        var handleRequestLocation: (() -> CLLocation)?
        func requestLocation() {
            guard let location = handleRequestLocation?() else { return }
            locationFetcherDelegate?.locationFetcher(self, didUpdateLocations: [location])
        }
    }
    
    func testCheckCurrentLocation() {
        var locationFetcher = MockLocationFetcher()
        let requestLocationExpectation = expectation(description: "request location")
        locationFetcher.handleRequestLocation = {
            requestLocationExpectation.fulfill()
            return CLLocation(latitude: 37.3293, longitude: -121.8893)
        }
        let provider = CurrentLocationProvider(locationFetcher: locationFetcher)
        let completionExpectation = expectation(description: "completion")
        provider.checkCurrentLocation { isPointOfInterest in
            XCTAssertTrue(isPointOfInterest)
            completionExpectation.fulfill()
        }
        //   Can mock the current location
        wait(for: [requestLocationExpectation, completionExpectation], timeout: 1)
    }
}
```

### 정리하기 
- 먼저 외부 클래스의 인터페이스를 나타내는 새 프로토콜을 정의했습니다. 이 프로토콜은 외부 클래스에서 사용하는 모든 메서드와 속성을 포함해야 하며 종종 해당 선언이 정확히 일치할 수 있습니다.
- 다음으로, 프로토콜 준수를 선언하는 원래 외부 클래스에 대한 확장을 만들었습니다. 그런 다음 외부 클래스의 모든 사용을 새 프로토콜로 교체하고 테스트에서 이 타입을 설정할 수 있도록 초기화 매개변수를 추가했습니다. 의존성 주입으로 바꿔준거죠. 
- 또한 SDK의 일반적인 패턴인 delegate 프로토콜을 테스트하는 방법에 대해서도 이야기했습니다. 먼저, Mock delegate 프로토콜을 정의했습니다. 그리고 실제 CLLocationManager 타입을 프로토콜 타입으로 대체했습니다.
- 그런 다음 원래 Mock 프로토콜에서 delegate 속성의 이름을 바꾸고 extension에서 이름이 바뀐 속성을 구현했습니다.
- 따라서 이 접근 방식은 서브클래싱과 같은 대안보다 더 많은 코드가 필요할 수 있지만 시간이 지남에 따라 코드를 확장함에 따라 더 안정적이고 중단될 가능성이 적습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/8ee402ee-72f7-439a-aa96-229be3b64b08/image.png)


## 4. 테스트를 더 빠르게 실행하기 

### 지연된 작업(비동기 또는 타이머) 코드를 개선하기 1

- 테스트를 실행하는 데 시간이 오래 걸리면 개발 중에 테스트를 실행할 가능성이 줄어들거나 가장 오래 실행되는 테스트를 건너뛰고 싶을 수 있습니다. 따라서 우리는 테스트가 항상 가능한 한 빨리 실행되도록 하고 싶습니다.
- 지연된 작업을 테스트해야 하는 경우가 있습니다. 비동기식이나 타이머를 사용하는 경우죠. 하지만 테스트에서는 인위적인 지연은 절대 필요하지 않기 때문에 그것을 개선해야 합니다. 
- 아래 예시 코드를 볼게요. 추천 장소를 표시하는 클래스가 있습니다. 그리고 10초마다 새로운 위치를 표시합니다. 
- 그런데 테스트 코드를 보면 FeaturedPlaceManager를 생성하고 scheduleNextPlace 메서드를 호출하고 11초 동안 RunLoop를 실행합니다. 유예 기간으로 1초가 있어요. 마지막으로 currentPlace가 마지막에 변경되었는지 확인합니다.
- 테스트 한번 하는데 11초가 걸립니다. 이것을 수정하기 위해 interval 속성으로 시간 제한을 1초와 같이 더 짧게 사용자 정의할 수 있습니다.


```swift
class FeaturedPlaceManager {
    var currentPlace: Place
    
    func scheduleNextPlace() {
        // Show next place after 10 seconds
        Timer.scheduledTimer(withTimeInterval: 10, repeats: false) { [weak self] _ in
            self?.showNextPlace()
        }
    }
    
    func showNextPlace() {
        // Set currentPlace to next place...
    }
}

class FeaturedPlaceManagerTests: XCTestCase {
    func testScheduleNextPlace() {
        let manager = FeaturedPlaceManager()
        manager.interval = 1

        let beforePlace = manager.currentPlace
        manager.scheduleNextPlace()
        //   Slow! Not ideal
        //RunLoop.current.run(until: Date(timeIntervalSinceNow: 11))
        RunLoop.current.run(until: Date(timeIntervalSinceNow: 2))
        
        XCTAssertNotEqual(manager.currentPlace, beforePlace)
    }
}
```

### 지연된 작업(비동기 또는 타이머) 코드를 개선하기 2
- 개선된 코드는 이전보다는 낫습니다. 하지만 여전히 이상적이지는 않습니다. 지연은 여전히 있고 단지 더 짧을 뿐입니다.
- 실제 문제는 우리가 테스트하는 코드가 여전히 타이밍에 의존적이라는 것입니다. 즉, 예상되는 지연 시간을 점점 더 짧게 만들면 테스트의 신뢰성이 떨어질 수 있습니다. 예측 가능한 스케쥴링을 잡기 위해 CPU에 더 의존하게 되기 때문입니다. 따라서 더 나은 접근 방식을 살펴보겠습니다.
- 먼저 뭐때문에 지연되는지를 식별하는 것이 좋습니다. 이 예시에서는 타이머죠. 꼭 타이머가 아니더라도 흔히 쓰는 DispatchQueue에서 asyncAfter API 같은 것들도 있습니다. 이렇게 지연된 작업을 즉시 호출하고 지연을 우회할 수 있도록 해야 합니다. 
			
![](https://velog.velcdn.com/images/dev_kickbell/post/2dd6a40c-055a-4294-a5a0-b1cc27711d40/image.png)

- 다시 원래 코드가 있습니다. 이 scheduledTimer 메서드가 실제로 수행하는 작업을 살펴보는 것으로 시작하겠습니다.
- Timer.scheduledTimer(withTimeInterval: 10, repeats: false) { ... } 메서드는 실제로 두 가지 작업을 수행합니다. 
- 타이머를 생성하는 작업과 그 타이머를 런루프에 추가하는 작업이죠. 이것은 편리하지만, 이걸 2단계로 분리하면 테스트하기가 더 쉬워집니다. 
- 분리를 하고 봤더니 runLoop이 이 클래스와 상호 작용하는 또 다른 외부 클래스라는 것을 알 수 있습니다. 뭔가 익숙하지 않나요? 그렇습니다. 바로 위에서 했던 외부 클래스에 대한 테스트는 Mock 프로토콜을 사용하면 되겠죠. 

```swift
class FeaturedPlaceManager {
    let runLoop = RunLoop.current
    
    func scheduleNextPlace() {
        let timer = Timer(timeInterval: 10, repeats: false) { [weak self] _ in
            self?.showNextPlace()
        }
        runLoop.add(timer, forMode: .default)
    }
    
    func showNextPlace() { /* ... */ }
}
```

- 먼저 TimerScheduler 이라는 프로토콜을 생성해줍니다. TimerScheduler은 RunLoop가 가지고 있는 add 메소드를 그대로 명시했기 때문에 extension에 빈확장이 가능하죠.
- 그리고 다형성에 따라서 기존의 runloop를 timerScheduler로 대체할 수 있습니다. 
- 그리고 테스트에서 실제 runLoop를 TimerScheduler로 사용하고 싶지 않으니 아까처럼 MockTimerScheduler라는 TimerScheduler을 준수하는 구조체를 생성해줍니다. 
- 마찬가지로 타이머 핸들러를 만들어줍니다. 이제 테스트를 진행할 수 있습니다. 
- 먼저 테스트 코드에서 MockTimerScheduler의 인스턴스를 만듭니다. 그리고 타이머의 지연을 0초로 지정한 다음에 클로저를 이용해서 fire() 해줍니다. 아까하고 똑같죠? 아까는 위치값을 클로저에서 리턴해줬었습니다. 
- 자, 그리고 마찬가지로 아까처럼 이 timeScheduler를 FeaturedPlaceManager의 생성자로 넣어줍니다. 마지막으로 scheduleNextPlace를 호출하여 테스트를 시작하면 테스트가 더 이상 지연되지 않습니다.
- 매우 빠르게 실행되고 타이머에 의존하지 않으므로 더 안정적입니다. 그리고 보너스로 이제 맨 아래에 있는 XCAssertEqual을 사용하여 타이머 지연을 확인할 수도 있습니다. 이런 작업은 이전 테스트에서는 할 수 없었지요. 

```swift
//애플리케이션 코드 
protocol TimerScheduler {
    func add(_ timer: Timer, forMode mode: RunLoop.Mode)
}

extension RunLoop: TimerScheduler {}


class FeaturedPlaceManager {
    
    let timerScheduler: TimerScheduler
    init(timerScheduler: TimerScheduler) {
        self.timerScheduler = timerScheduler
    }
    
    func scheduleNextPlace() {
        let timer = Timer(timeInterval: 10, repeats: false) { [weak self] _ in
            self?.showNextPlace()
        }
        timerScheduler.add(timer, forMode: .default)
    }
    
    func showNextPlace() { /* ... */ }
}

//테스트 코드 
class FeaturedPlaceManagerTests: XCTestCase {
    
    struct MockTimerScheduler: TimerScheduler {
        var handleAddTimer: ((_ timer: Timer) -> Void)?
        func add(_ timer: Timer, forMode mode: RunLoop.Mode) {
            handleAddTimer?(timer)
        }
        
    }
    
    func testScheduleNextPlace() {
        var timerScheduler = MockTimerScheduler()
        var timerDelay = TimeInterval(0)
        timerScheduler.handleAddTimer = { timer in
            timerDelay = timer.fireDate.timeIntervalSinceNow
            timer.fire()
        }
        
        let manager = FeaturedPlaceManager(timerScheduler: timerScheduler)
        let beforePlace = manager.currentPlace
        manager.scheduleNextPlace()
        
        // No delay!
        XCTAssertEqual(timerDelay, 10, accuracy: 1)
        XCTAssertNotEqual(manager.currentPlace, beforePlace)
    }
    
}
```

### 환경변수를 추가해서 앱의 기능을 사용중지하기
- 마지막 테스트 속도 향상의 팁은 앱이 최대한 빨리 실행되도록 하는 것입니다. 
- 무슨 말이냐면, 앱이 일반적으로 실행될 때 수행하는 설정 작업들이 있습니다. 근데 이런 녀석들은 테스트로 시작되는 경우 많은 작업이 필요하지 않을 수 있겠죠. 
- 뷰컨트롤러 로드, 네트워크 요청시작, analytics 패키지 구성 같은 것들입니다. 그러면 테스트에서는 이런 작업을 피하면 됩니다. 
- 아래의 그림처럼 scheme 에디터를 열고 환경변수를 지정합니다. 특별히 정해진 것은 없고 여기서는 IS-UNIT-TESTING, 1로 지정했습니다. 
- 다음에 appDidFinishLaunching 메서드에서 isUnitTesting이 false 이면 테스트시에 생략할 수 있는 UI 관련 설정을 해줍니다. 이렇게 하면 테스트 속도가 더 향상될 수 있습니다.
		
![](https://velog.velcdn.com/images/dev_kickbell/post/26c0954c-d04e-465f-9b88-cf8b0e7c1055/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/e08c0029-ca38-4c88-8100-d6ce55bffe59/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/9e56ea9f-308a-4ca7-b411-3c4ccf1f298b/image.png)

    
## Reference

[https://developer.apple.com/videos/play/wwdc2018/417](https://developer.apple.com/videos/play/wwdc2018/417)				  

## Endnotes 

### ¹Assertion(어썰션)
- 어썰션은 로직을 테스트하는 데 사용할 수 있는 디버깅 기능입니다. 사전적인 의미로는 무언가를 긍정하거나 진술하는 행위를 의미합니다. 


