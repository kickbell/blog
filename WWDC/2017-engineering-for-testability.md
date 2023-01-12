# Engineering for Testability


> _이 글은 [WWDC 2017 - Engineering for Testability](https://developer.apple.com/videos/wwdc2017)의 내용을 공부하고, 개인적으로 정리해놓은 글입니다._


![](https://velog.velcdn.com/images/dev_kickbell/post/1780f0bf-9a93-47a1-bbad-10e44b695d04/image.png)


## 1. 테스트 가능한 코드 

### 유닛 테스트의 구조 
- 테스트 코드에 대한 이야기는 참 많이 듣습니다. 너무 좋다. 내가 작성한 코드가 원래의 의도대로 잘 동작한다는 확신을 얻을 수 있다. 코드를 문서화할 수 있다 등등요. 
- 그런데, 막상 테스트 코드를 작성하려고하면 막막한 경우가 많습니다. 내가 코드 자체를 테스트하기 어렵게 작성하고 있었기 때문이죠. 
- 테스트는 보통 아래와 같이 일반화 됩니다. input을 준비하고, 테스트 할 코드를 호출하고, 마지막으로 return 된 output이 기대값과 맞는지를 확인하는 것이죠. 
- 이런 것을 보통 Arrange Act Assert Pattern 이라고 합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/135eb5bf-ed9e-46a1-812e-6079ce1e5bb3/image.png)

## 2. 테스트 가능한 코드의 특징 
- 클라이언트가 작동하는 모든 input을 제어할 수 있는 방법을 제공합니다.
- 클라이언트가 생성되는 output을 검사할 수 있는 방법을 제공합니다.
- 나중에 코드의 동작에 영향을 미칠 수 있는 내부 상태에 의존하지 않습니다. 

## 3. 프로토콜의 ¹매개변수화
- 첫번쨰 기술은 프로토콜과 매개변수화를 코드에 도입하는 방법입니다. 
- 아래 그림의 앱을 보겠습니다. 문서 프리뷰 앱으로 문서를 미리 볼 수있고, 수정하거나 더 자세히 보기 위해서 세그먼트 컨트롤이 있습니다. Open 버튼을 클릭하면 수정화면 또는 자세히보기 화면으로 넘어가죠. 
- 코드는 아래와 같은데요. 세그먼트 컨트롤에 따라서 보여줘야할 모드가 결정되고, 선택된 모드로 url이 결정됩니다. 
- 그리고 기본으로 지원하는 canOpenURL을 사용해서 이 url을 열 수 있는지 확인하고, 가능하다면 open URL 작업을 호출하고 아니면 handleURLError() 메소드를 호출합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/bd482a13-3f6a-46d8-bdde-7ea6b145f863/image.png)

```swift
    @IBAction func openTapped(_ sender: Any) {
        let mode: String
        switch segmentedControl.selectedSegmentIndex {
        case 0: mode = "view"
        case 1: mode = "edit"
        default: fatalError("Impossible case")
        }
        let url = URL(string: "myappscheme://open?id=\(document.identifier)&mode=\(mode)")!
        
        if UIApplication.shared.canOpenURL(url) {
            UIApplication.shared.open(url, options: [:], completionHandler: nil)
        } else {
            handleURLError()
        }
    }
```
- 일반적으로 많이 사용하는 디자인이죠? 이 동작을 테스트하는데는 몇 가지 방법이 있을 수 있습니다. 
- 하나는 UI 테스트를 하는 건데요. 코드를 직접 적진 않겠습니다만 이렇게 진행될겁니다. 
- 앱 실행 > 해당 화면으로 이동 > open버튼을 누르기 전에 설정 > open 버튼 탭 > 다른 앱으로 전환되는지 확인 
- 하지만, 여기에는 치명적인 단점이 있는데요. 만약에 연결해야 할 URL이 엄청 많다고 해볼게요. 그럼 일단 검사 시간이 오래걸립니다. 하나하나씩 다 실행해봐야하니까요. 그리고 또 정작 중요한 URL을 검사할 방법 자체가 없습니다. 이게 문제죠. 그래서 여기에는 Unit 테스트가 더 적합한 것 같습니다. 
- 아래는 Unit 테스트의 코드입니다. 컨트롤러를 가져와서 loadViewIfNeeded()로 컨트롤러의 뷰 데이터를 채워줍니다. 다음에 세그먼트 컨트롤을 설정하고 document를 제공하고 open 버튼을 탭합니다. 
- 다음에 예상값하고 결과값을 비교해야 되는데? 뭘비교해야 할까요? 명확하지 않습니다. 🥲

```swift
    func testOpensDocumentURLWhenButtonIsTapped() {
        let controller = UIStoryboard(name: "Main", bundle: nil)
            .instantiateViewController(withIdentifier: "Preview") as! PreviewViewController
        controller.loadViewIfNeeded()
        controller.segmentedControl.selectedSegmentIndex = 1
        controller.document = Document(identifier: "TheID")
        
        controller.openTapped(controller.button)
        
        // ???
    }
```

- 자, 다시 기존 openTapped() 코드로 돌아가서 뭐가 문제인지 살펴볼게요. 
- 일단 코드가 뷰컨트롤러에 있는 것만으로도 테스트가 더 어려워집니다. 테스트를 하려면 뷰컨트롤러 인스턴스를 생성해서 실행해야 되는데, 문제가 생길 수 있어요. 
- 또, switch 문에서 segmentedControl.selectedSegmentIndex 같은 식으로 뷰에서 상태값을 직접 꺼내고 있는데 간접적으로 제공하도록 해야 합니다. 
- 그러나 가장 큰 문제는 UIApplication.shared 같은 싱글톤이 문제입니다. 
- 왜 문제냐면, 우리가 canOpenURL을 외부에서 제어해줄 수 있어야 예상값 기대값으로해서 테스트를 할건데 얘는 시스템 메소드이므로 우리가 제어할 수가 없기 때문이다. 
- 또 open 에서는 open을 했을 때의 체크할 수 있는 방법이 존재하지 않고, 또 이 함수를 사용하면 safari 앱이 열리면서 앱이 background로 내려가는데 이것을 다시 forground 상태로 가져오는 방법이 존재하지 않는다.

```swift
    @IBAction func openTapped(_ sender: Any) {
        let mode: String
        switch segmentedControl.selectedSegmentIndex {
        case 0: mode = "view"
        case 1: mode = "edit"
        default: fatalError("Impossible case")
        }
        let url = URL(string: "myappscheme://open?id=\(document.identifier)&mode=\(mode)")!
        
        if UIApplication.shared.canOpenURL(url) {
            UIApplication.shared.open(url, options: [:], completionHandler: nil)
        } else {
            handleURLError()
        }
    }
```

### 코드 개선하기 
- 먼저 테스트할 코드를 뷰컨트롤러에서 꺼내도록 하겠습니다. 메소드 내부의 로직을 따로 캡슐화하기 위해서 DocumentOpener 클래스를 생성합니다. 
- DocumentOpener 클래스에는 OpenMode라는 enum이 있고, open을 할 때는 외부에서 값을 입력받을 수 있게 매개변수로 지정했습니다.  
- 그리고 UIApplication 을 외부에서 주입받을 수 있게 의존성 주입을 사용합니다. 그리고 생성자에는 기본 값을 할당하는데 이것은 기존 코드에 에러가 생기지 않으려고 하는 겁니다. 

```swift
    class DocumentOpener {
        let application: UIApplication
        init(application: UIApplication = UIApplication.shared) {
            self.application = application
        }
        
        enum OpenMode: String {
            case view
            case edit
        }
        
        func open(_ document: Document, mode: OpenMode) {
            let modeString = mode.rawValue
            let url = URL(string: "myappscheme://open?id=\(document.identifier)&mode=\(modeString)")!
            if application.canOpenURL(url) {
                application.open(url, options: [:], completionHandler: nil)
            } else {
                handleURLError()
            }
        }
    }
```

- 자, 이제 이렇게 해놓고 다시 테스트를 작성해보도록 하지요. 음? 그래도 여전히 문제는 해결되지 않았습니다.
- 왜냐면, UIApplication 은 여전히 싱글톤이고 여전히 통제가 안되죠. 이것을 해결하려면 음 canOpenURL과 open 메서드를 오버라이드하는 하위 클래스를 만들 것을 고려해볼 수 있겠죠. 하지만 UIApplication은 싱글톤이 엄격하게 적용되어 다른 객체를 생성하려고 하면 에러를 발생시킵니다. 
- 따라서 프로토콜을 사용해서 문제를 해결해보도록 하겠습니다. 
- URLOpening 이라는 이름의 프로토콜을 생성합니다. 그리고 여기는 UIApplication의 canOpenURL과 open 메서드가 똑같이 구현되어 있습니다. 
- 그리고 extension UIApplication: URLOpening 을 해줍니다. 프로토콜에는 이미 같은 함수가 구현되어 있기 때문에 아무것도 해줄 필요가 없습니다. 
- 다음으로 DocumentOpener 에서 application 인스턴스를 urlOpener: URLOpening으로 바꿔줍니다. 
- 기본값은 UIApplication.shared 로 그대로 두겠습니다. 그래야 뷰컨트롤러에서 사용할 때는 정상동작 하겠죠? 테스트에서만 따로 작업해주면 되니까요. 

```swift
    func testDocumentOpenerWhenItCanOpen() {
        let app = /* ??? */
        let opener = DocumentOpener(application: app)
    }
```
```swift
    protocol URLOpening {
        func canOpenURL(_ url: URL) -> Bool
        func open(_ url: URL, options: [UIApplication.OpenExternalURLOptionsKey : Any]) async -> Bool
    }
    extension UIApplication: URLOpening { }
    
    class DocumentOpener {
        let urlOpener: URLOpening
        init(urlOpener: URLOpening = UIApplication.shared) {
            self.urlOpener = urlOpener
        }

        enum OpenMode: String {
            case view
            case edit
        }

        func open(_ document: Document, mode: OpenMode) {
            let modeString = mode.rawValue
            let url = URL(string: "myappscheme://open?id=\(document.identifier)&mode=\(modeString)")!
            if urlOpener.canOpenURL(url) {
                urlOpener.open(url, options: [:], completionHandler: nil)
            } else {
                handleURLError()
            }
        }
    }
```

- 자, 다음은 방금 테스트에서 외부에서 넣어준다고 했잖아요. 그게 무슨말이냐면 실제 앱에서는 UIApplication.shared 해서 실제로 앱이 열린다거나 하겠지만 테스트에서는 굳이 그 동작이 될 필요가 없죠. 
- 여기서 우리가 테스트해야 할 canOpenURL, open을 가지고 있는 Mock 객체를 만들어줄 겁니다. 그래서 외부에서 이녀석을 주입해서, 테스트에서 예상값과 맞는지를 비교하면 테스트가 완료되는 것이죠. 
- MockURLOpener 클래스를 생성하고 URLOpening를 준수합니다. 메서드들을 구현해주는데요. canOpen이라는 변수와 openedURL이라는 변수를 구현했습니다. 
- canOpen이라는 변수는 기본값이 false입니다. 원래의 canOpen은 시스템에서 결정할 문제였죠. url이 유효한지를 UIApplication에서 결정해서 Bool 타입을 리턴해줬으니까요. 그런데, 이제는 우리가 컨트롤 할 수 있습니다. 
- 테스트 할 때, DocumentOpener에서 urlOpener.CanOpenURL을 호출하잖아요? 그때 여기서 true가 되어있어야 다음으로 넘어갈겁니다. 원래는 UIApplication에서 결정했던 거였고, 심지어 정보권한이 없으면 팝업이 떠서 그것도 문제가 됐었죠. 
- 또, 기존에는 url에 우리가 접근할 수 없었죠. 근데 여기서는 open 메소드를 호출하면 해당 url을 우리가 생성한 openedURL의 변수에 넣어줍니다. 값이 없다면 옵셔널이니 nil이 되겠죠. 
- 자, 그러면 이제 준비가 끝났으니 테스트를 다시 작성해볼건데요. 이름대로 CanOpen에 관한 테스트를 작성합니다. 먼저 MockURLOpener의 인스턴스를 생성하고, canOpen 값을 true로 설정합니다. 우리가 canOpen의 접근여부를 결정할 수 있어요. 
- 다음은 DocumentOpener 인스턴스를 Mock객체로 생성합니다. 그래야 DocumentOpener 내부에서 urlOpener.open(url...)이 불릴 때, 우리 Mock 객체에 있는 openedURL 변수에 값이 할당되겠죠. 
- 마지막으로 XCTAssertEqual에서 urlOpener.openedURL이 예상값과 맞는지 확인합니다.
- 추가적으로는 canOpen이 false인 경우, 같은 입력 데이터의 다른 변형에 대한 테스트도 추가할 수 있겠습니다만 여기서는 패스하겠습니다. 

```swift
    class MockURLOpener: URLOpening {
        var canOpen = false
        var openedURL: URL?
        
        func canOpenURL(_ url: URL) -> Bool {
            return canOpen
        }
        
        func open(_ url: URL,
                  options: [UIApplication.OpenExternalURLOptionsKey : Any]) async -> Bool {
            openedURL = url
        }
    }
    
    func testDocumentOpenerWhenItCanOpen() {
        let urlOpener = MockURLOpener()
        urlOpener.canOpen = true
        
        let documentOpener = DocumentOpener(urlOpener: urlOpener)
        documentOpener.open(Document(identifier: "TheID"), mode: .edit)
        
        XCTAssertEqual(urlOpener.openedURL, URL(string: "myappscheme://open?id=TheID&mode=edit"))
    }

```

### 정리하기 
- UIApplication은 싱글톤이라 우리가 간섭할 수가 없었죠. 그래서 URLOpening이라는 프로토콜을 만들었어요. 그리고 기존의 init(application: UIApplication) 같은 것들을 URLOpening의 인스턴스로 대체했습니다. 
- 이런 것을 의존성 주입(생성자 주입)이라고 합니다. 그리고 소제목처럼 프로토콜이 마치 매개변수(파라미터)처럼 사용되고 있죠. 그래서 프로토콜의 매개변수화라고 하는 겁니다. 
- 또, 우리는 불변 클래스인 UIApplication 를 대체해서 Mock 객체를 만들었습니다. 그것은 우리에게 입출력에 대한 가시성을 제공했어요. Mock 객체로 대체해서 테스트를 할 수 있었죠. 

## 4. logic와 effect를 분리하기 

### 디스크 캐시 
- 다음으로는 로직과 효과를 분리하는 것이 테스트에 어떻게 사용될 수 있는지 이야기해보겠습ㄴ디나. 
- onDiskCache 클래스가 있습니다. 캐시가 파일 시스템에서 너무 많은 공간을 차지하지 않도록 주기적으로 호출해야 합니다.
- 먼저 현재 아이템을 다 호출하고, 가장 오래된 순으로 정렬합니다. 그리고 누적(cumulative) 사이즈를 확인하다가 최대 크기가 되면 아이템을 하나씩 제거하기 시작합니다. 
- 이것을 테스트해보려고 합니다. input과 output은 무엇인가요? 

```swift
    class OnDiskCache {
        struct Item {
            let path: String
            let age: TimeInterval
            let size: Int
        }
        var currentItems: Set<Item> { /* ... */ }

        func cleanCache(maxSize: Int) throws {
            let sortedItems = self.currentItems.sorted { $0.age < $1.age }
            var cumulativeSize = 0
            for item in sortedItems {
                cumulativeSize += item.size
                if cumulativeSize > maxSize {
                    try FileManager.default.removeItem(atPath: item.path)
                }
            }
        }
    }
```

### FileManager에 대한 종속성 
- input 중에 하나는 maxSize이죠. 얘는 이미 cleanCache의 매개변수로 있습니다.
- 또 하나는 currentItems 인데요. 여기서 문제가 되는 부분은 currentItems는 파일 목록을 검색하기위해 FileManager를 사용합니다. 종속성을 띄고 있는 것이죠. 
- 다른 문제는 cleanCache에 return 값이 없다는 겁니다. FileManager에서 아이템이 삭제될 뿐이니까요.
- FileManager에 종속성을 띄고 있기 때문에 테스트에서도 FileManager을 사용할 수 밖엔 없는데, 테스트 setup을 위해서는 임시적인 디렉토리를 생성하여 특정 크기의 파일로 채우고, 파일들에게 각각 특정한 timestamp를 제공해주어야한다. 그리고 output을 검증하기 위해서 파일 시스템으로 돌아가서 어떠한 파일들이 남아있는지를 확인해야 합니다. 
- 이에 접근하는 한 가지 방법은 우리가 위에서 했던 프로토콜, 매개변수화를 사용하는 방법인데요. 파일 목록을 가져오고 파일을 제거하는 데 필요한 방법이 있는 파일 관리자 프로토콜을 만들 수 있습니다. 
- 그런 다음 반환될 파일 목록과 제거된 파일에 대한 쿼리를 지정할 수 있는 테스트 구현을 만듭니다. 그래도 이렇게 하면 파일 관리자에 의해 테스트하려는 코드와 간접적으로 상호 작용할 수 있습니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/9a117e88-51e6-47c9-877e-0d6f6bbd31bf/image.png)

### 더 간단하고 쉬운 방법 
- 머리가 아프죠? 상대적으로 더 간단한 방법이 있습니다. 소제목대로 logic과 effect를 분리하자는 거죠. 
- 여기서의 logic은 "제거해야 할 파일을 결정하는 logic"입니다. effect는 파일시스템에서 삭제하는 메소드겠죠. 
- 무슨말이냐면, 기존에는 "cleanCache() 주기적으로 실행 -> 전체아이템 파일매니저에서 가져오기 > 정렬 > 누적사이즈계산 > 누적사이즈가 맥스보다 크면 파일매니저에서 데이터 삭제"의 흐름이이었습니다. 
- 근데 이걸 2개로 나눈거에요. 새로만든 CleanupPolicy, MaxSizeCleanupPolicy에서는 맥스사이즈를 기준으로 파일매니저에 접근하지 않고 데이터를 var로 생성해서 삭제해야될 아이템을 결정하고 리턴합니다. 
- 그러면 cleanCache에서는 그냥 넘어오는 아이템을 삭제만 해주는 거죠. 얘가 멍청해진겁니다. 얘는 그냥 "아 이거 삭제하라고? 알았어."만 하는겁니다. 
- 또하나 재밌는 점은 cleanCache에서 원래 maxSize라는 파라미터를 받았는데 지금은 CleanupPolicy라는 프로토콜을 받죠. 그리고 우리가 만든 구조체인 MaxSizeCleanupPolicy이 CleanupPolicy을 준수하니까 더 상위 객체인 CleanupPolicy가 매개변수로 들어올 수 있죠.  
- 자, 그러면 테스트 코드를 봅시다. 삭제해야할 inputItems를 만들어요. 그리고 아이템이 3개 있네요. size는 7,2,9입니다. 
- 그리고 삭제해야할아이템을 정하는데, max가 10인데 지금 사이즈가 7+2+9이므로 10을 넘죠. 그래서 제일 마지막인 3번 아이템을 지워야해요. outputItems으로 로직에 의해 item3번이 들어옵니다. 
- 그러면 outputItems과 기대값을 비교해서 테스트를 진행할 수 있게 됩니다. 파일 매니저는 보이지도 않죠. 이 로직을 시각화 한다면 아래의 그림과 같습니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/19662767-af69-4ed9-a561-80311d6392b7/image.png)

```swift
    protocol CleanupPolicy {
        func itemsToRemove(from items: Set<OnDiskCache.Item>) -> Set<OnDiskCache.Item>
    }

    struct MaxSizeCleanupPolicy: CleanupPolicy {
        let maxSize: Int
        func itemsToRemove(from items: Set<OnDiskCache.Item>) -> Set<OnDiskCache.Item> {
            var itemsToRemove = Set<OnDiskCache.Item>()
            var cumulativeSize = 0

            let sortedItems = allItems.sorted { $0.age < $1.age }
            for item in sortedItems {
                cumulativeSize += item.size
                if cumulativeSize > maxSize {
                    itemsToRemove.insert(item)
                }
            }
            return itemsToRemove
        }
    }

    class OnDiskCache {
        /*...*/

        func cleanCache(policy: CleanupPolicy) throws {
            let itemsToRemove = policy.itemsToRemove(from: self.currentItems)
            for item in itemsToRemove {
                try FileManager.default.removeItem(atPath: item.path)
            }
        }
    }
```
```swift
    func testMaxSizeCleanupPolicy() {
        let inputItems = Set([
            OnDiskCache.Item(path: "/item1", age: 5, size: 7),
            OnDiskCache.Item(path: "/item2", age: 3, size: 2),
            OnDiskCache.Item(path: "/item3", age: 9, size: 9)
        ])
        
        let outputItems = MaxSizeCleanupPolicy(maxSize: 10).itemsToRemove(from: inputItems)
        
        XCTAssertEqual(outputItems, [OnDiskCache.Item(path: "/item3", age: 9, size: 9)])
    }
```


## 5. 확장가능한 테스트 코드 

### 테스트 피라미드
- 일반적으로 UI 테스트는 발생할 수 있는 일의 수 때문에 유지 보수 비용이 더 높은 경향이 있습니다. 반면에 Unit 테스트는 유지보수 비용이 낮습니다. 따라서 Unit 테스트가 실패하면 무엇이 잘못되었는지 즉시 알 수 있습니다.
- UI 테스트를 사용하면 이해하기 어렵거나 실행한 테스트와 관련이 없을 수 있는 실패를 얻을 수 있는 넓은 그물을 던지는 것과 같습니다. 그래서 조금 더 까다로울 수 있죠.
- Unit 테스트는 모든 앱 소스 코드에 액세스하지 않고는 확인하기 어려울 수 있는 작은 코드 조각을 테스트하는 데 유용합니다. 반면에 UI 테스트는 함께 작동하는 큰 코드 청크를 테스트해야 할 때 유용합니다. 물론, UI 테스트는 그렇지 않지만 Unit 테스트는 우리 앱의 모든 소스에 접근할 수 있다는 것을 명심해야 합니다.
- UI 테스트에 더 중점을 두고 테스트 코드의 품질을 개선하기 위해 할 수 있는 몇 가지 방법을 살펴보겠습니다. 제가 제안하려는 몇 가지 변경 사항을 적용함으로써 앱 코드와 함께 확장되는 테스트를 더 쉽게 만들 수 있습니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/0c2b0430-1364-4f76-a43e-fd2998565990/image.png)

### UI 요소 쿼리를 추상화하기
- 먼저 UI 요소 쿼리를 추상화하는 방법을 살펴보겠습니다. 첫번째 그림과 같이 7개의 버튼이 있다고 생각해볼게요. 너무 많죠. 두번째 사진처럼 교체합니다. 
- 그런데, tapButton을 사용해서 바꿨더니 이름을 제외하고는 또 다 똑같습니다. 그러면 다시 리팩토링 할 수 있습니다. 세번쨰 사진처럼요. 
- 이렇게 배열에 이름들을 넣고 tapButton을 사용하면 유지보수에 이점이 있습니다. 나중에 버튼이 추가되면 배열에만 이름을 하나 추가해주면 되기 때문입니다. 
- 정리하면, 동일한 쿼리를 여러번 사용하는 경우 변수로 저장합니다. 또, tapButton처럼 헬퍼 메소드를 만들어주면 더 나아지겠죠. 
- 리팩토링한 코드는 더 깔끔해보이고 읽기 쉬워질것이고 테스트를 확장함 있어서 코드가 짧고 읽기 쉬워지는 것은 새로운 테스트를 더 쉽고 빠르게 만들 수 있다는 것과 같습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/443b7e11-5594-4d4f-9188-32c7209b63ef/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/7233f250-9dc3-45d0-bcd9-93d4377039e4/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/da9c6bae-4d94-4ac9-a567-643f83b03c77/image.png)


### 추상화, 여러곳에서 사용할 수 있도록 객체화하기
- 이제 유틸리티 기능에서 개체를 만드는 것으로 넘어갑시다. 아래의 코드는 게임에서의 난이도와 소리를 변경하는 코드입니다. 
- 설정 > 난이도 > 초심자 > 뒤로 > 소리 > 소리끔 > 뒤로 > 뒤로 
- 이것은 확장 가능한 코드의 좋은 예가 아닙니다. 코드를 작성한 사람은 앱에서 무슨 일이 일어나고 있는지 알겠지만, 몇 달 후에 다시 이 코드를 보거나 다른 사람이 이것을 읽는다면 전혀 이해하지 못할 수 있습니다. 
- 설정 페이지가 있다는 것을 알아야 하고, 난이도와 사운드 페이지가 있다는 것을 알아야 하고 각각 뒤로 가기를 한번씩 해야 된다는 것도 알아야 하기 때문입니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/37073112-11ce-4009-a185-2d7ca5bc7e57/image.png)

- 따라서 이러한 로직을 헬퍼 메서드로 추상화 해보겠습니다. 먼저 setDifficulty라는 이름으로 난이도를 파라미터에 따라서 난이도를 설정하는 메소드를 만듭니다. 그리고 이어서 setSound도 같은 방식으로 만듭니다. 
- 그런데, 파라미터를 enum으로 바꾸면 어떨까요? 그러면 String값이 틀려도 컴파일 타임에 오류를 체크할 수 있습니다. 
- 세번째 사진을 보면 코드가 많이 좋아보이는 것을 알 수 있습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/b4ecb92b-903f-4050-b2c4-306dc2e32edc/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/1996eac0-6d0e-4b86-ac32-3d8c807c2148/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/9e534eb1-027e-4a01-bcaa-9d568cacfd12/image.png)

- 그런데, 아직 내비게이션바에서 설정으로 가는 버튼과 뒤로가는 버튼이 남아있네요. 이것도 변경할 수 있을까요? 
- 가능합니다. 일단, GameApp이라는 클래스를 만듭니다. 그리고 기존에 만들었던 enum과 추상화 메소드들도 포함합니다. 
- 그리고 기존의 테스트 코드를 configureSesstings 라는 이름의 메소드를 만들고 그 안에 넣어줍니다. 이렇게 하면, GameApp 클래스가 있기 때문에 코드 한줄로 설정 방법을 테스트할 수 있습니다. 
- 만약에 나중에 앱에 더 많은 설정을 추가하기로 한 경우를 생각해볼게요. 그러면 테스트를 확장하기 위해 추상화를 나중에 자체 라이브러리에 넣을 수 있도록 바꿔야 합니다. 
- 예를 들어, 난이도나 설정같은 것들도 종류가 더 많아질것이고 그런것들을 하나로 모아야겠죠. 그리고 그걸 import 해와서 테스트를 하는 겁니다. 패드, 워치, 폰, 맥에 전부 되도록 라이브러리화 하면 좋겠네요. 업데이트도 라이브러리 한곳에서만 하면 되구요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/d184ad8d-042f-4662-beed-a6634a4347fe/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/5c881134-3aea-4df9-a76a-f3202d261e54/image.png)

- 마지막으로, XCTContent.runActivity 를 소개하고 싶습니다. 
- 이것의 이점은 간단합니다. 아래의 사진처럼 함수를 저렇게 호출합니다. 그러면 테스트를 실행할 때, 최상위 수준에서 수행한 모든 작업이 포함된 로그가 아닌 runActivity를 사용하여 생성된 로그를 볼 수 있습니다. 
- 이것은 테스트를 조금 더 깨끗하게 보이게 하는 데 도움이 됩니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/a671375d-a7e8-4476-b716-5d94868f1f88/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/a36bb892-c238-459c-8be4-c94852ae588b/image.png)


### MacOS 키보드 단축키를 활용하기
- 사용자가 표준 macOS 색상 선택기를 사용하여 텍스트의 색상을 선택할 수 있는 앱이 있다고 가정해 보겠습니다. 그리고 색상이 제대로 설정되었는지 확인하기 위해 테스트를 작성하고 있습니다.
- 일반적으로 색상 선택기를 불러오는 방법은 메뉴바 > 포맷 > 폰트 > Show Colors 입니다. 그래서 두번째 사진처럼 앱의 메뉴바를 불러와서 테스트 코드를 작성할 수 있죠. 
- 그런데, 색상 선택기에는 그림에 보이다시피 단축키가 있습니다. 그러면 여러 줄의 코드 대신에 한줄로 색상 선택기를 불러올 수 있습니다. 
- 결론적으로 아래의 코드처럼 app.typeKey()로 4줄의 코드를 1줄로 줄일 수 있습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/eaa90241-2bfe-4634-80aa-d1729bfff206/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/f271ef68-f9a6-459b-8736-410fd500a9f6/image.png)
				
```swift
    //단축키 사용전 코드
    func testChangeColor() {
        let menuBarsQuery = app.menuBars
        menuBarsQuery.menuBarItems["Format"].click()
        menuBarsQuery.menuItems["Font"].click()
        menuBarsQuery.menuItems["Show Colors"].click()
        // test code
    }
    
    
    //단축키 사용후 코드
    func testChangeColor() { showColors()
        showColors()
    }
    
    func showColors() {
        app.typeKey("c", modifierFlags:[.command, .shift])
        // test code
    }
```


## 6. 테스트 코드 품질의 중요성
- 마지막으로, 저는 여러분 모두에게 좋은 테스트를 작성하는 것은 좋은 코드를 작성하는 것이라는 것을 강조하고 싶습니다.
- 테스트를 나중에 작성하는 것은 쉽죠. 우리는 좋은 디자인의 모든 원칙을 고수해서 가능한 최고의 앱을 만들기 위해 노력합니다. 하지만 테스트는 그렇지 않습니다. 마지막에 붙이는 것일 수도 있고 확인차 급하게 작성하는 것일 수도 있습니다.
- 낮은 품질의 테스트 코드는 앱을 변경할 때마다 테스트를 업데이트해야 하는 부담이 됩니다. 항상 품질을 염두해 두고 의식적으로 테스트 코드를 설계하면 유지보수 부담도 적어집니다.
- 즉, 테스트 코드와 앱 코드를 동일하게 보아야 합니다. 앱 코드를 업데이트할 때 테스트 코드도 업데이트해야 합니다.
- 최고의 솔루션은 우리는 테스트 코드에 대한 코드 리뷰를 가져야 합니다. 앱 코드에 대한 코드 리뷰만 하는 것이 아닙니다.
- 우리의 코드를 더 테스트 가능하게 만들고, 테스트 코드를 당신의 앱 코드와 동일하게 취급함으로써, 당신은 전체 앱의 품질을 향상시킬 수 있다.

![](https://velog.velcdn.com/images/dev_kickbell/post/57505953-e604-4d87-a7fe-a933d2ec93b9/image.png)

    
## Reference

[https://developer.apple.com/videos/wwdc2017/414](https://developer.apple.com/videos/wwdc2017)			

## Endnotes 

### ¹매개변수화(parameterization)
- 위에서 코드를 수정할 때보면, URLOpening 이라는 프로토콜을 생성해서 UIApplication 이라는 내부 API를 대체했죠. 
- 생성자에서도 바로 URLOpening 타입으로 받구요. 이렇게 프로토콜이 마치 매개변수처럼 변했기 때문에 매개변수화라고 합니다. 



