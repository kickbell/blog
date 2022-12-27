# Storage를 API처럼 사용해보기

> Storage에 JSON 파일을 업로드해서 API를 사용하는 것처럼 활용해봅니다.     
        
_**Firebase Cloud Storage**_ 는 사진, 동영상 등의 사용자 제작 콘텐츠를 저장하고 제공해야 하는 앱 개발자를 위해 만들어졌다고 합니다.

자세한 내용은 [공식 문서 링크](https://firebase.google.com/docs/storage?authuser=1)에 가면 보실 수 있어요. 

하지만 저도 이미지, 오디오, 동영상 등은 아직 업로드해서 테스트해보진 않았어요. 저는 주로 개인앱을 만들 때 테스트 용도로 Storage를 많이 사용합니다. 

예를들어, 프로젝트를 진행하는데 기획서만 나오고 서버와 디자인은 아직 개발 중이라고 생각을 해보죠. 그러면 iOS 개발자는 뭘하고 있어야 할까요? 

네, 설계를 해야죠 :) 

기획서를 바탕으로 이 화면은 어떤 뷰를 사용할지, 디자인 패턴은 무엇으로 할 지, 클래스는 어떻게 나눌지 등등.. 말이죠. 

그런데 그럼에도 불구하고, 개발 일정 지연을 등으로 시간이 조금 뜰 때가 있어요. 그럴 때는 내가 디자이너 또는 백엔드 개발자이고 싶죠. 
(물론 붕 뜨는 시간 동안 러프하게 UI를 짜고 있거나, 재사용이 가능하도록 UI컴포넌트를 미리 만들고 있어도 됩니다.)

이럴 때, 저같은 경우는 _**Firebase Cloud Storage**_ 를 활용합니다. 

지금부터 할 일을 요약하자면 아래와 같습니다.  
- Storage의 `Rules`에서 접근권한을 설정해준다. 
- 임의의 json 파일을 생성한다. 
- json 파일을 Storage에 업로드한다. 
- 공유링크를 받아서 Storage를 API처럼 활용한다. 
- API가 생겼으니 열심히 앱을 개발한다.😄

## 1. Storage의 `Rules`에서 접근권한을 설정해준다. 

일단 프로젝트를 원하는 이름으로 하나 만듭니다. 
그리고 프로젝트를 열고 Storage 메뉴로 들어가볼게요. 그러면 보통 아래의 화면이 보일 겁니다.         
          
![](https://images.velog.io/images/dev_kickbell/post/266dae82-7b61-4b97-8738-175210bda418/image.png)      

여기서 `Rules` 메뉴를 탭할게요. 그러면 아래의 화면이 나옵니다.       
        
![](https://images.velog.io/images/dev_kickbell/post/9d423f69-9736-49e6-a3dc-18f328c4bff9/image.png)        

이건 해당 프로젝트 스토리지에 접근할 수 있는 권한을 규칙으로 정의해 놓은 건데요. 현재는 모두 읽기가 허용되고, 수정하기는 허용되지 않은 상황입니다. 자세한 규칙은 [여기](https://firebase.google.com/docs/rules/get-started?authuser=1)를 참고하세요. 

## 2. 임의의 json 파일을 생성한다. 
이제 json 파일을 하나 만들어보겠습니다. json 파일이야 다양한 방법으로 만들 수 있겠으나, 저는 Storage 바로 위 메뉴인 Realtime Database를 통해서 만들어 봤어요. 

서버에서 메뉴를 받아오는 데이터같은 느낌을 주기위해 아래처럼 menus라는 키값 고정된 값의 배열을 주었습니다. 그리고 우측 상단에서 JSON 내보내기를 선택해주면 데스크탑에 JSON 파일이 간단히 저장됩니다.         
          
![](https://images.velog.io/images/dev_kickbell/post/b868f93f-744b-428d-a91e-cfe6ea7bf862/image.png)

## 3. json 파일을 Storage에 업로드한다. 

자, 이제 json 파일이 생겼으니 업로드를 해볼까요? 다시 Storage로 돌아와서 파일업로드 버튼을 누르고 방금 다운받은 json 파일을 선택해줍니다.      
          
![](https://images.velog.io/images/dev_kickbell/post/3897a9f9-3023-4b65-a3d0-1537ab99e32f/image.png)

그리고 업로드 된 json 파일을 클릭해보면, 우측에 해당 파일의 정보가 뜨는데요.      
        
![](https://images.velog.io/images/dev_kickbell/post/94a232cc-b760-48cf-adea-5119e7119b6e/image.png)      
          
파란색으로 칠해진 링크를 클릭해봅시다. 그러면 어떻게 되죠? 저희가 GET으로 데이터를 불러오는 것 처럼 json이 아래와 같이 보여집니다 :) 

## 4. 공유링크를 받아서 Storage를 API처럼 활용한다. 
      
![](https://images.velog.io/images/dev_kickbell/post/d392318a-2c01-44aa-a117-85f33d8dc63f/image.png)        
        
이 링크를 한 번 분석해보면, 

`https://firebasestorage.googleapis.com/v0/b/carserver-bb9be.appspot.com/o/carserver-bb9be-default-rtdb-export%20(1).json?alt=media&token=14aa301d-ee00-4345-8565-d76f5b165b29` 

https부터 .json까지의 부분이 base url이라고 볼 수 있겠고, 나머지 `alt`와 `token`값이 파라미터라고 볼 수 있겠네요. 

유의할 점은 값이 `token`이니 firebase를 로그아웃하거나 토큰이 만료되면 토큰값이 변동된다는 점이 있을 수 있겠죠. 또한, 아까 이야기했던 rules에서 읽기 권한을 false로 설정한다면 아마 보이지 않을 겁니다. 이 부분을 주의해주셔야 해요. 

## 5. API가 생겼으니 열심히 앱을 개발한다.

자, 그럼 API가 생겼네요. 이제 열심히 개발해보면 되겠죠 ? 

간단하게 테이블 뷰를 스토리보드로 만들고 delegate를 연결해줍니다. 
우리가 가지고있는 URL은 MY라는 enum으로 만들었습니다. 
그리고 URLSession을 사용해서 데이터를 리턴해주도록 했습니다. 

최종화면과 전체 코드는 아래와 같습니다. 
        
![](https://images.velog.io/images/dev_kickbell/post/c94cbe6d-c428-4a27-a8bf-713c537f27b4/image.png)      

```swift

import UIKit

class ViewController: UIViewController {
  @IBOutlet weak var tableView: UITableView!
  
  var menus = [Menus]()
  
  override func viewDidLoad() {
    super.viewDidLoad()
    request()
  }
  
  private func request() {
    APIService.request(urlStr: MY.URL) { [weak self] menus in
      self?.menus = menus
      DispatchQueue.main.async {
        self?.tableView.reloadData()
      }
    }
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


enum MY {
  static let URL = "https://firebasestorage.googleapis.com/v0/b/carserver-bb9be.appspot.com/o/carserver-bb9be-default-rtdb-export%20(1).json?alt=media&token=14aa301d-ee00-4345-8565-d76f5b165b29"
}


class APIService {
  static func request(urlStr: String, complectionHandler: @escaping ([Menus]) -> ()){
    guard let url = URL(string: urlStr) else { return print("invalid URL...") }
    
    URLSession.shared.dataTask(with: url) { data, responds, error in
      guard let data = data else { return }
      guard error == nil else { return print(error!.localizedDescription) }
      
      struct Response: Decodable {
        let menus: [Menus]
      }
      guard let result = try? JSONDecoder().decode(Response.self, from: data) else { return }
      
      complectionHandler(result.menus)
      
    }.resume()
  }
}

```

사실, 임의의 json파일을 번들에 넣어놓고 가져오는 더 쉬운 방법도 있을 거에요. 하지만 저같은 경우는 서버에서 데이터를 가져올 것을 가정하고 진행했습니다. 이렇게하면 나중에 서버가 개발되었을 때 모델링하는 Codable 부분만 조정해주면 같은 결과를 얻을 수 있을 겁니다. 



