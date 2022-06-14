---
layout: post
title: "[Swift] Coordinator Pattern - 1. Basic"
tags: [sample post, readability, test, intro]
comments: true
---

그동안 미뤄두고 미뤄뒀던 Coordinator Pattern에 관해서 공부를 좀 해보려고 합니다. 

## 정의 
**Coordinator Pattern** 에 정의부터 말해보자면, 애플리케이션 디자인 설계 패턴 중에 하나입니다. 그리고 그 중에서도 **앱의 내비게이션 흐름**에 관한 설계 패턴이죠. 

무슨 말이냐면, 보통 앱의 어떤 Button, 혹은 Cell과 같은 View를 탭해서 화면을 전환할 때는 아래 와 같이 작업합니다. 

```swift
func pushToDetail() {
    let vc = DetailViewController()
    navigationController?.pushViewController(vc, animated: true)
}
```

물론 이 코드는 매우 정상적으로 잘 동작합니다. 하지만, 여기서의 중요한 점은 당사자인 내가 **DetailViewController**를 알고 있어야 한다는 것이죠.  즉, 해당 코드가 매우 종속적으로 연결되어있고, 하드 코딩 되어 있습니다.  

이것은 애플리케이션에서 결합도를 높이는 좋지 않은 설계입니다. 애플의 MVC 패턴에서 **ViewContorller** 는 참 여러가지 일을 하고 있고,  그 중에 하나가 이와 같은 **Navigation** 입니다. 

이런 문제를 해결하기 위해 코디네이터 패턴을 사용하면, 현재의 뷰컨트롤러는 다음에 오는 뷰컨트롤러가 무엇인지 알 수도, 알 필요도 없게 됩니다. 뷰컨트롤러는 오직 코디네이터에만 제어되며 SOLID의 SRP(단일 책임 원칙)에도 한결 더 가까워질 수 있을 겁니다. 

그림으로 설명하면 아래와 같은데요. 

![image](https://user-images.githubusercontent.com/85085822/162136676-f19943fd-eaa1-41a9-b806-4f050ec53199.png)

보통의 네비게이션에서 A -> B -> C 와 같은 흐름을 가졌다면, **Coordinator Pattern**에서는 모든 뷰컨트롤러가 **Coordinator** 하고만 이야기 합니다. 

결과적으로 A -> C 로 갈수도, A -> F 로 갈 수도 있게 되는 것이죠. 그럼 한 번 코드로 예시를 들어가면서 설명해보겠습니다. 

## 구현 

### 1. 프로토콜 선언 
```swift
protocol Coordinator {
    var navigationController: UINavigationController? { get  set }
    var children: [Coordinator]? { get set }
    func eventOccurred(with type: Event)
    func start()
}

enum  Event {
    case button01Tapped(Int)
    case button02Tapped(String)
}

protocol Coordinating {
    var coordinator: Coordinator? { get set }
}
```

먼저 Coordinator 라는 프로토콜을 선언해주었습니다. Coordinator는 네비게이션 컨트롤러를 가질 수 있고, 앱의 크기가 커지고 복잡해진다면 children 라는 이름으로 [Coordinator] 값을 가질 수 있습니다. 

또한, 앱의 시작지점을 설정해주어야 하므로 func start() 을 가지고 있어요. 그리고 이건 여러 각자의 글이나 스타일마다 다르지만, 저 같은 경우에는 어떤 화면으로 이동해야 하냐에 따라서 Event 라는 enum 값을 정의했고, 그걸 매개변수로 받는 func eventOccurred(with type: Event) 을 정의했습니다. 

그리고 화면 전환을 요구할 뷰 컨트롤러가 Coordinator와 이야기해야 하므로 Coordinating 이라는 프로토콜을 만들어주고 coordinator 값을 가질 수 있도록 정의했습니다. 

사실, 여러가지 예제를 보면서 느낀거지만 **Coordinator Pattern** 같은 경우에는 명확히 어떤식으로 해야 한다는 강제성은 없어요. 그래서 각자각자 스타일이 약간씩 다를 수가 있죠. 

처음 배우는 입장에서는 사실 그때문에 조금 더 어렵긴 했습니다만,, 아무튼 그렇습니다. 만약 2015년에 제일 처음으로 **The Coordinator** 라는 글을 작성한 **Khanlou**의 글을 보고 싶다면, [이 곳](https://khanlou.com/2015/01/the-coordinator/)에서 볼 수 있습니다. 

그래서, 여기서는 Coordinating 이라는 프로토콜을 선언해주긴 했습니다만 사실 해당 프로토콜을 선언해서 뷰 컨트롤러에서 채택하지 않고, 그냥 필요한 곳에서만 변수로 선언해줘도 됩니다. 여기서는 Coordinating 프로토콜을 선언해서 진행하겠습니다. 


### 2. 앱의 시작점 설정 

프로토콜을 선언했으니 앱의 시작점을 지정하겠습니다. 예제 코드에서는 스토리보드를 사용하고 있지 않으므로, Main 스토리보드를 삭제했고, SceneDelegate에서의 설정만 아래와 같이 작업해주었습니다. 

```swift
import UIKit

class SceneDelegate: UIResponder, UIWindowSceneDelegate {
    
    var window: UIWindow?

    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        guard let scene = (scene as? UIWindowScene) else { return }
        
        let navVC = UINavigationController()
        let coordinator = MainCoordinator(navigationController: navVC)
        
        window = UIWindow(frame: UIScreen.main.bounds)
        window?.rootViewController = navVC
        window?.windowScene = scene
        window?.makeKeyAndVisible()
        
        coordinator.start()
    }
}

```

