# RealTime Database를 API처럼 사용해보기

> RealTime Database에 JSON 형식의 데이터를 만들고 수정해가면서 API를 사용하는 것처럼 활용해봅니다.

\_**Firebase RealTime Database**\_는 클라우드기반의 호스팅 데이터베이스입니다.

데이터는 JSON으로 저장되며 연결된 모든 클라이언트에 실시간으로 동기화되고, Apple, Android 등 크로스 플랫폼 앱을 개발하면 모든 클라이언트가 하나의 실시간 데이터베이스 인스턴스를 공유하고 자동 업데이트로 최신 데이터를 수신한다고 하네요.

말그대로 실시간 데이터베이스니 채팅기능 같은 것들도 구현할 수 있을 거에요.

자세한 내용은 [공식 문서 링크](https://firebase.google.com/docs/database?authuser=1)에 가면 보실 수 있어요.

아래 시리즈인 `Storage`와 이 글의 내용은 유사합니다.

물론 용도가 더욱 다양하겠지만, 적어도 제가 사용하는 용도에 한해서 RealTime Database와 Storage의 가장 큰 차이는 이겁니다.

RealTime Database는 실제 백엔드서버처럼 데이터를 수정해도 클라이언트 코드를 바꾸지 않아도 되고, Storage는 말그대로 저장소이기 때문에 json 파일을 수정하면 재업로드하고 공유링크도 새로 받아와야 합니다.

이렇게 보면 당연히 RealTime Database가 더 편해보이죠?

하지만 마냥 그렇지 많은 않아요. 상황에 따라 선택하시면 될 텐데, Storage 같은 경우는 json을 업로드하고 공유링크만 받아오면 땡입니다. 데이터가 계속 변하지 않는다고 가정하면 굉장히 간편하죠.

하지만, RealTime Database는 상대적으로 초기 설정을 해야 하는 부분이 제법 있습니다. 하나씩 해볼까요 ?

## 1. 앱을 추가하여 시작하기

둘 다 아래 조건을 진행했다고 치고 진행할게요.

* Firebase SDK를 설치합니다.
* Firebase Console에서 Firebase 프로젝트에 앱을 추가합니다.

그러면 RealTime Database는 iOS 앱을 추가해야 해요. 아래 앱을 추가하여 시작하기 윗 부분에 iOS 버튼을 눌러줄게요.

![](https://images.velog.io/images/dev\_kickbell/post/27d68785-e475-4a04-9702-f70a41c8c2d2/image.png)

## 2. 번들 ID 넣어주기

그리고 시키는 대로 하면 되는데 `번들 ID` 를 넣어줘야 합니다. 어디있냐면, 내 프로젝트로 가서 좌측에 프로젝트 워크스페이스를 클릭하고 General-Target을 보면 상단에 Bundle Identifier가 보이죠? 저것을 그대로 복사해와서 붙여넣어 줍니다.

Bundle Identifier는 RealTime Database에서 내 앱을 구별하는 고유 값이 될 거에요. 나머지 2개는 선택사항이니 다음으로 넘어갑니다.

![](https://images.velog.io/images/dev\_kickbell/post/72b7dd02-4b61-4cf0-880e-b09df4801eb0/image.png)

## 3. 구성파일 추가하기

글에 나와있는대로 구성파일을 다운로드하고 프로젝트에 드래그앤드랍으로 추가해줍니다.

![](https://images.velog.io/images/dev\_kickbell/post/c7821af7-80f2-4b3c-9634-240050c9ec1d/image.png) ![](https://images.velog.io/images/dev\_kickbell/post/d3eee493-c0b3-4f91-aa4c-daa54f53dd10/image.png)

## 4. 패키지 추가하기

그리고 또 시키는 대로 Firebase SDK를 추가해줄게요. (시간 제법 걸립니다.)

![](https://images.velog.io/images/dev\_kickbell/post/8d4a0806-f748-4eec-8d5f-3a66539ef1d9/image.png) ![](https://images.velog.io/images/dev\_kickbell/post/f43ed02b-bec8-47b8-abc1-d841d7cbcb7e/image.png)

패키지 추가 팝업이 열리면 밑에 Github을 클릭하고 문서에 나와있는 링크를 복사붙여넣기해서 검색창에 검색합니다. 그러면 아래처럼 sdk를 찾을 수 있죠? 이것을 Add Package 할게요.

![](https://images.velog.io/images/dev\_kickbell/post/6e82247c-e76e-465e-9f20-36bbd36d5c15/image.png)

같이 선택할 패키지가 나오는데 저희는 RealTime Database만 사용해볼거니까 이것만 설치하겠습니다. 다른 것도 같이하셔도 무방합니다.

![](https://images.velog.io/images/dev\_kickbell/post/18ae6f62-eaf3-4bd5-bd96-d8ea3f567a06/image.png)

## 5. 초기화 코드 추가하기

패키지 추가가 완료되면 초기화 코드를 추가해주라고 하죠 ?

그런데 iOS 13부터는 SceneDelegate 라는 것이 생겼어요. 그래서 문서에 나와있는대로 하려면 SceneDelegate를 삭제해주어야 합니다.

1. SceneDelegate 삭제

![](https://images.velog.io/images/dev\_kickbell/post/168673af-28eb-4bb2-88d6-30297b11fad7/image.png)

1. Application Scene Manifest 삭제(리스트 우측 - 버튼)

![](https://images.velog.io/images/dev\_kickbell/post/9aeb0bda-2764-4881-a4a7-8389662acbbd/image.png)

1. Appdelegate에서 아래 함수 제거

```swift
  // MARK: UISceneSession Lifecycle

  func application(_ application: UIApplication, configurationForConnecting connectingSceneSession: UISceneSession, options: UIScene.ConnectionOptions) -> UISceneConfiguration {
    // Called when a new scene session is being created.
    // Use this method to select a configuration to create the new scene with.
    return UISceneConfiguration(name: "Default Configuration", sessionRole: connectingSceneSession.role)
  }

  func application(_ application: UIApplication, didDiscardSceneSessions sceneSessions: Set<UISceneSession>) {
    // Called when the user discards a scene session.
    // If any sessions were discarded while the application was not running, this will be called shortly after application:didFinishLaunchingWithOptions.
    // Use this method to release any resources that were specific to the discarded scenes, as they will not return.
  }
```

1. Appdelegate에서 window를 추가하고 파이어베이스 설정

```swift
import UIKit
import Firebase

@main
class AppDelegate: UIResponder, UIApplicationDelegate {

  var window: UIWindow?

  func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    FirebaseApp.configure()
    return true
}
```

여기까지 했으면 이제 설정은 끝난겁니다. 이것도 어렵진 않지만, 아까 말했다시피 Storage 보다는 복잡하죠? 그래서 상황에 맞게 적절히 사용하시면 될 것 같아요.

이제 사용해볼건데, 공식문서 샘플도 있지만 처음에 보기엔 다소 어려울 수 있기도 하고 저희는 그냥 서버처럼 써보기만 할거라서 따로 설명하지는 않겠습니다. 공식문서는 [여기](https://github.com/firebase/quickstart-ios/tree/master/database/DatabaseExample)에 있으니 보셔도 될 것 같아요.

## 6. JSON 문서 만들기

자, Storage때처럼 json을 업로해주진 않아요. 다만 json 형식으로 문서를 만들어 주면 되겠죠. 콘솔로 오면 친절히 데이터베이스 만들기라는 버튼이 있습니다.

![](https://images.velog.io/images/dev\_kickbell/post/03e0427f-ecb9-4b8b-9753-5c065f892122/image.png)

그리고 차례대로 변경없이 다음-사용설정을 눌러주면 데이터베이스가 생성됩니다.

![](https://images.velog.io/images/dev\_kickbell/post/c1e31bed-8335-4d3a-bb99-ffe0d4b96b0e/image.png)

익숙한 화면이죠? 똑같이 규칙을 볼까요? 잠금모드 이지만 읽기, 쓰기 권한이 모두 false로 되어있네요. 둘 다 true로 바꿔줍니다. 바꿔주지 않으면 접근할 때 권한이 없다고 나올거에요.

```swift
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

자, 다시 데이터로 돌아와서 + 버튼을 누르면 아래와 같이 입력창이 나오고 데이터를 생성할 수 있습니다.

![](https://images.velog.io/images/dev\_kickbell/post/8e92c01b-5510-4eb4-bb2c-7fec34931e13/image.png)

기본적으로 같은 키값을 가지고 있다면 데이터는 덮어쓰기 됩니다.

![](https://images.velog.io/images/dev\_kickbell/post/2d308728-ec3c-442b-a800-75dd926a194c/image.png)

이런식으로 데이터를 생성할 수 있다는 것만 알아둘게요. 왜냐면 저희는 이미 json 파일을 가지고 있기 때문이죠. 데이터베이스 우측 설정버튼을 누르고 json 가져오기를 클릭합니다.

파일을 선택하고 Storage에서 사용했던 json을 추가하면 아래와 같이 데이터가 생성되는 것을 볼 수 있지요.

![](https://images.velog.io/images/dev\_kickbell/post/0cafc690-7eea-4c78-9944-b00d9151d8c2/image.png)

## 7. 저장된 데이터베이스 불러오기

자, 이제 이걸 불러와야 할 겁니다. 코드를 볼까요 ? 자세한 방법은 [여기](https://firebase.google.com/docs/database/ios/read-and-write?hl=ko#read\_data\_once)에 있으니 참고하시면 됩니다.

일단, json 데이터 타입을 다시 보겠습니다. 아래와 같은 식이죠? 데이터의 키값이 `menus`이고, 데이터는 `Array`값으로 지정되어 있습니다.

```swift
{
  "menus": [
    {
      "order": 1,
      "name": "인바디 Q&A",
      "url": "https://m.cafe.naver.com/ca-fe/web/cafes/25016228/menus/1957"
    },
    {
      "order": 2,
      "name": "다이어트 톡",
      "url": "https://m.cafe.naver.com/ca-fe/web/cafes/25016228/menus/1648"
    },
    {
      "order": 3,
      "name": "제품랭킹",
      "url": "https://m.cafe.naver.com/ca-fe/web/cafes/25016228/menus/2089"
    },
    {
      "order": 4,
      "name": "오늘의 식단",
      "url": "https://m.cafe.naver.com/ca-fe/web/cafes/25016228/menus/333"
    }....
```

이전에 했던 Storage는 URLSesstion을 이용해 데이터 통신을 했지만, 지금은 Firebase를 이용하고 있으므로 URLSesstion은 필요 없어요. 아래는 전체 코드입니다.

```swift

import UIKit
import Firebase

class ViewController: UIViewController {
  @IBOutlet weak var tableView: UITableView!
  
  var ref: DatabaseReference!
  
  var menus = [Menus]()
  
  override func viewDidLoad() {
    super.viewDidLoad()
    request()
  }
  
  private func request() {
    ref = Database.database().reference()
    ref.child("menus").observeSingleEvent(of: .value, with: { [weak self] snapshot in
      for child in snapshot.children {
        let dataSnapshot = child as? DataSnapshot
        if let item = dataSnapshot?.value as? NSDictionary {
          let menu = Menus(name: item["name"] as? String ?? "",
                           url: item["url"] as? String ?? "")
          self?.menus.append(menu)
          DispatchQueue.main.async {
            self?.tableView.reloadData()
          }
        }
      }
    })
  }
  
}

extension ViewController: UITableViewDataSource {
  func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return menus.count
  }
  
  func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = tableView.dequeueReusableCell(withIdentifier: "cell", for: indexPath)
    cell.textLabel?.text = menus[indexPath.row].name
    cell.detailTextLabel?.text = menus[indexPath.row].url
    return cell
  }
}

struct Menus: Decodable {
  let name: String
  let url: String
}

```

여기서 중요한 점은 ref 값을 생성하면서 초기화하지 말아야 한다는 점, 무슨말이냐면 아래처럼 꼭 두 줄로 나눠줘야 한다는 겁니다. 한 줄로 하면 크래시가 발생해요.

```swift
  var ref: DatabaseReference!
  ref = Database.database().reference()
```

## 8. 데이터베이스 변경하고 다시 실행해보기

자, 그럼 이번엔 데이터를 바꿔볼까요? 그러려고 RealTime Database를 사용하는 거니까요.

현재 데이터는 아래와 같습니다.

![](https://images.velog.io/images/dev\_kickbell/post/ce9285ad-5312-49e8-beb0-ea8dfde9a27b/Simulator%20Screen%20Shot%20-%20iPhone%2013%20Pro%20Max%20-%202022-01-18%20at%2016.37.07.png)

그리고 이걸 음 파이어베이스 데이터베이스를 열고 원하는 인덱스에 마우스를 올리면 나오는 x를 눌러 데이터를 삭제해볼게요.

![](https://images.velog.io/images/dev\_kickbell/post/168ac2d0-5294-411d-a5da-ff02b649e778/image.png) ![](https://images.velog.io/images/dev\_kickbell/post/91de4bbc-1838-4284-ad2e-ec0c59ce0240/image.png)

몇 개의 데이터를 지웠고 현재 데이터는 이렇습니다.

![](https://images.velog.io/images/dev\_kickbell/post/28e24062-d4d8-4977-830d-3ff57999e72d/image.png)

클라이언트의 코드는 바꾸지 않았죠? 다시 프로그램을 실행해보겠습니다.

![](https://images.velog.io/images/dev\_kickbell/post/309dead5-46c5-4293-8506-5855261fb87b/Simulator%20Screen%20Shot%20-%20iPhone%2013%20Pro%20Max%20-%202022-01-18%20at%2016.39.40.png)

출력이 제대로 되는 것을 볼 수 있죠.

Storage와는 다르게 실시간으로 데이터베이스가 반영되는 것을 볼 수 있습니다. 실시간 데이터베이스는 읽기뿐만 아니라 수정도 얼마든지 가능합니다. 따라서 채팅같은 것도 구현이 가능하지요. 그건 나중에 해볼게요. 이상입니다.
