`Webkit`에는 꽤 재미난 기능이 하나 있습니다. 공식 문서는 `Webkit`-`WkWebViewConfiguration`-`dataDetectorTypes`인데요. 바로 `dataDetectorTypes` 이라는 녀석이에요. 

이 녀석은 `WKDataDetectorTypes` 타입이고, 이름 그대로 데이터를 탐지합니다. 

무슨 말이냐면, 애초에 `Webkit`에서 제공되는 기능이잖아요? ( 물론 `UITextView` 안에서도 되긴 합니다. 일부 지원되는 타입이 다른데 자세한 사항은 [여기](https://developer.apple.com/documentation/uikit/uidatadetectortypes)를 참고하세요. ) 그래서 `WKWebView`를 통해 웹페이지가 보여진다면 거기서 `WkWebViewConfiguration` 에 세팅된 탐지값에 따라서 자동으로 `WKDataDetectorTypes` 라는 타입들을 탐지해서 메뉴를 제공합니다. 

> var dataDetectorTypes: WKDataDetectorTypes { get set }    
The types of data detectors to apply to the web view’s content.         
The default value of this property is `none`.       
`iOS 10.0+`, `iPadOS 10.0+`, `Mac Catalyst 13.1+`         

기본값은 `none` 이라는 부분이 중요하겠네요. `WKDataDetectorTypes` 타입은 여러가지 종류가 있는데요. 아래와 같습니다. 네이밍만으로 어느정도 전달이 될테니, 설명은 생략할게요 😀

```swift
static var phoneNumber: WKDataDetectorTypes

static var link: WKDataDetectorTypes

static var address: WKDataDetectorTypes

static var calendarEvent: WKDataDetectorTypes

static var trackingNumber: WKDataDetectorTypes

static var flightNumber: WKDataDetectorTypes

static var lookupSuggestion: WKDataDetectorTypes

static var all: WKDataDetectorTypes       
```

스토리보드로 설정하는 방법은 WKWebView를 생성하고, 우측 `Attribute Inspector` 를 보시면 있구요.            

![](https://velog.velcdn.com/images/dev_kickbell/post/c2eee431-8f5b-4372-97dc-36005799771f/image.png)       

코드로도 당연히 가능합니다. 아주 간단하게 샘플코드를 작성해봤어요. `flightaware` 는 항공 관련 정보를 제공하는 페이지인데요. 꽤나 많은 사이트를 참고해봤지만, 제일 적당한 것 같아서 가져왔습니다😀. 아 그리고 한글은 영문처럼 완벽하게 인식이 되진 않는 것 같아요. 주소, 전화번호 같은 것들 말이죠. 

```swift
import UIKit
import WebKit

class ViewController: UIViewController {
    
    private var webView = WKWebView()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        configure()
    }
    
    private func configure() {
        let configuration = WKWebViewConfiguration()
        configuration.dataDetectorTypes = .all
        webView = WKWebView(frame: view.frame, configuration: configuration)
        view.addSubview(webView)
        
        let url = URL(string: "https://ko.flightaware.com/live/flight/AFR702")!
        let request = URLRequest(url: url)
        webView.load(request)
    }
    
}
```
자, 그럼 이제 `백문이 불여일견` 이라고 어떻게 동작하는지 직접 보시죠. 예시를 전부다 가져오진 못했지만, 대부분 가져왔어요.             		

## `phoneNumber`		        
![](https://velog.velcdn.com/images/dev_kickbell/post/274081d2-7821-48bb-915a-17cefd322b9a/image.gif)				        

## `link` 				      
![](https://velog.velcdn.com/images/dev_kickbell/post/3a1510e4-c1f6-43b1-81bf-d02a3300f5df/image.gif)		        			

## `address`				      
![](https://velog.velcdn.com/images/dev_kickbell/post/86993596-c95f-4487-b20d-de1e56958902/image.gif)						      

## `calendarEvent`				      
![](https://velog.velcdn.com/images/dev_kickbell/post/34a39f0f-ffdc-457d-9a9f-fd52cd2adaa2/image.gif)					      

## `flightNumber`			      
![](https://velog.velcdn.com/images/dev_kickbell/post/00bf5292-1064-4f84-827b-85626cb63937/image.gif)				      	


재미있죠?😆, 각 타입에 따라서 작은 팝업창이 뜨는데 액션을 탭하면 저희가 아무 작업을 해주지 않았음에도 불구하고 다 동작합니다. 그것도 꽤나 편하죠. `WKDataDetectorTypes` 같은 경우는 사실 지인 분께서 하이브리드 앱 구현 중에 생긴 CS에 대해 이야기하다가 알게 되었는데요. 상황에 따라 요긴하게 쓰거나, 하이브리드 앱 또는 텍스트뷰 등을 구현할 때 유의하면 좋을 것 같습니다. 


## Conclusion
- `WebKit`의 `WKWebView`에서는 `dataDetectorTypes`이라는 프로퍼티를 통해 웹뷰에서 제공하는 데이터들에 대한 탐지와 액션을 자동으로 제공합니다. 
- `WebKit`과 `TextView`에서 제공하는 `dataDetectorTypes`은 다릅니다. `UIDataDetectorTypes` 과 `WKDataDetectorTypes` 으로 대부분 비슷하지만, 타입의 종류가 일부 다르니 유의하세요. 
- `dataDetectorTypes`을 어떻게 지정했느냐에 따라 기획의도에서 벗어나는 이슈가 있을 수 있기 때문에 주의하면 될 것 같습니다. 

## Reference
[https://developer.apple.com/documentation/webkit/wkwebviewconfiguration/1641937-datadetectortypes](https://developer.apple.com/documentation/webkit/wkwebviewconfiguration/1641937-datadetectortypes)					
[https://developer.apple.com/documentation/webkit/wkdatadetectortypes
](https://developer.apple.com/documentation/webkit/wkdatadetectortypes)		[https://developer.apple.com/documentation/uikit/uidatadetectortypes](https://developer.apple.com/documentation/uikit/uidatadetectortypes)
