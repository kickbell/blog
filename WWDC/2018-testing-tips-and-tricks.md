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
- 테스트 피라미드를 기준으로 살펴보면 우리가 하고싶은 것은 저 4단계에 대한 테스트를 작성하는 겁니다. 
- 아래의 코드는 우리가 흔히 사용하는 네트워킹 요청에서의 코드입니다. 

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
- 자 그러면 여기서 트릭이 뭐냐면, URLProtocol을 상속하는 MockURLProtocol라는 이름의 클래스를 만듭니다. 그리고 실제 앱에서 사용하는 URLSession이랑 같은 애를 주입받는 거에요. 
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
- 그래서 아래 코드를 보면 client라고 있는데 얘가 URLProtocolClient 타입이에요. 그리고 메소드를 4개 호출하죠. 각자 주석에 설명이 적혀있는데 얘네들의 역할은 위에서 requestHandler에서 request를 하고 정상적으로 성공하면 (HTTPURLResponse, Data)를 리턴하잖아요? 그것을 받아서 urlProtocol 메소드로 응답 또는 오류를 시스템에 전달하는 겁니다.
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

```swift
```

## 3. mock을 만들 때 프로토콜 사용하기 


## 4. 테스트를 더 빠르게 실행하기 



```swift

```

    
## Reference

[https://developer.apple.com/videos/play/wwdc2018/417](https://developer.apple.com/videos/play/wwdc2018/417)				  

## Endnotes 

### ¹스냅샷(Snapshot)
- 사진을 찍듯이 특정 시점에 데이터를 별도의 파일이나 이미지로 저장, 보관하는 기술을 말합니다. 그래서 스냅샷 기능을 이용하여 데이터를 저장하면 유실된 데이터 복원과 일정 시점의 상태로 데이터를 복원할 수 있습니다.




```swift
  
```

<details>
  <summary><a href=""></a></summary>
  <p>

```swift
  
```
  </p>
</details>


식은 assert 식을 테스트하는 데 사용할 수 있는 디버깅 기능입니다. 디버그 모드에서 실패 시 어설션에서 시스템 오류 대화 상자를 생성합니다.

어설션은 무언가를 긍정하거나 진술하는 행위를 의미합니다. 또한 체크 포인트 또는 유효성 검증 포인트로 해석 될 수 있습니다. 요청이 웹 서버로 보내지면 응답이 수신됩니다. 응답에 예상되는 데이터가 포함되어 있는지 확인해야합니다.2018. 11. 24.


