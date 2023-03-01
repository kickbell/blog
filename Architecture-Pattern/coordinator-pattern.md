# Coordinator Pattern

코디네이터 패턴은 ViewContoroller에서 Navagation의 기능을 분리하기 위해 사용합니다. 정확하게는 코디네이터 패턴을 사용하여 A -> B -> C 와 같이 화면이 이동하는 제어 흐름을 별도의 코디네이터라는 객체로 대체하는 것이지요. 

가장 큰 장점은 ViewContoroller에서 Navagation의 기능을 별도의 객체로 분리한다는 것도 있지만, 기존의 A가 B라는 ViewContoroller를 생성하고 B에 대한 것을 알고 있어야만 한다는 단점이 있었습니다. 

```swift
    //normal 
    func userTapped(widget: Int) {
        let vc = NextViewController()
        b.widgetToBuy = widget
	present(vc, animate: true)
    }
    
    //use coordinator
    func userTapped(widget: Int) {
	coordinator?.buy(widget) 
    }    
```

말그대로 하드코딩이기 때문에 재사용할 수가 없고, 강한 결합도(coupling)가 생성됩니다. 스토리보드로 개발을 한다고 했을 때, segue를 사용하는 경우는 유독 더 그렇죠. 

이런 문제들을 코디네이터 객체로 대체하면, A는 B를 몰라도 됩니다. 그저 코디네이터에게 위임해버리면 끝인거죠. 또, 더 커다란 앱에서 코디네이터는 프로토콜일 가능성이 높으므로 모든 것을 동적으로 교체하고 다른 프로그램 흐름을 얻을 수 있습니다. 그리고 코디네이터를 하위 코디네이터로 나누어서 한 부분을 처리하도록 쪼갤 수도 있지요. 

결론적으로 Navigation을 코디네이터로 분리하는 작업은 우리의 코드를 SOLID에서의 S인 단일책임원칙을 준수하는 코드로 더 가까워지게 합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/a4f18ec9-12d0-4c12-9f1d-51ad144a9ffc/image.png)


## 1. 기본적인 코디네이터 패턴 구현 
기본적인 코디네이터를 구현해보겠습니다. 일단 코디네이터를 쉽게 만들기 위해 Coordinator라는 프로토콜을 하나 선언합니다. 프로토콜에는 아래 코드에 보이는 것처럼 3가지 요소가 있습니다. 

```swift
import Foundation
import UIKit

protocol Coordinator {
    var childCoordinators: [Coordinator] { get set }
    var navigationController: UINavigationController { get set }
    
    func start()
}
```
또, 이 예제에서는 스토리보드로 화면을 만드므로 UIViewController를 리턴하는 Storyboarded 프로토콜과 extension 에서 기본구현을 해줘서 프로토콜만 추가하면 바로 사용할 수 있도록 해주겠습니다.

```swift
protocol Storyboarded {
    static func instantiate() -> Self
}

extension Storyboarded where Self: UIViewController {
    static func instantiate() -> Self {
        let id = String(describing: self)
        let storyboard = UIStoryboard(name: "Main", bundle: Bundle.main)
        return storyboard.instantiateViewController(withIdentifier: id) as! Self
    }
}
```
다음으로는 화면을 만들건데요. 메인이되는 ViewController에서 버튼으로 이동할 BuyViewController, CreateAccountViewController를 생성하고 구별을 위해 레이블을 하나씩 넣어줍니다. 각 뷰컨트롤러의 이름으로 StoryboardID 를 지정해주고, 우리는 코디네이터를 생성해서 코드로 실행을 할거니까 TARGETS-General-Main Interface를 Main에서 공란으로 수정해줍니다. 
			
![](https://velog.velcdn.com/images/dev_kickbell/post/cdc84aac-9a33-42a2-bff2-7ea5ed0054dd/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/78b0a73c-2158-42f8-ad7a-e86305c0df9c/image.png)

그리고 이제 실제로 코디네이터를 만들어 줄 건데요. MainCoordinator 입니다. 아래처럼 네비게이션 컨트롤러는 외부에서 주입받을 수 있게 해주고, 만들어놓은 Storyboarded 프로토콜을 통해 간단히 vc값을 얻어올 수 있습니다. 

그리고 앱델리게이트로 가서 MainCoordinator 인스턴스를 옵셔널로 하나 선언해주고, 새로 생성해서 할당해줍니다. 그리고 앱을 실행해보면 정상적으로 화면이 뜨는 것을 볼 수 있어요. 뭐 이제까지는 아무것도 하지 않았지만요. 

```swift
import Foundation
import UIKit

class MainCoordinator: Coordinator {
    var childCoordinators: [Coordinator] = []
    var navigationController: UINavigationController
    
    init(navigationController: UINavigationController) {
        self.navigationController = navigationController
    }
    
    func start() {
        let vc = ViewController.instantiate()
        navigationController.pushViewController(vc, animated: true)
    }
}

import UIKit

class SceneDelegate: UIResponder, UIWindowSceneDelegate {
    var coordinator: MainCoordinator?
    var window: UIWindow?

    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        guard let windowScene = (scene as? UIWindowScene) else { return }
        
        let navController = UINavigationController()
        coordinator = MainCoordinator(navigationController: navController)
        coordinator?.start()
        
        window = UIWindow(windowScene: windowScene)
        window?.rootViewController = navController
        window?.makeKeyAndVisible()
    }
}
```
이어서 그러면 실제로 화면의 이동을 위한 코드를 구현해보겠습니다. 메인이되는 뷰컨트롤러에 IBAction을 각각 연결하고 coordinator 변수를 옵셔널로 선언합니다. 옵셔널인 이유는 밑에 코드에 나올거지만, 메인 코디네이터에서 start() 함수에 self를 할당해주기 때문이에요. 

그리고 이벤트가 들어오면 buyTapped와 createAccountTapped에서 각각 buySubscription(), createAccount()를 호출해줍니다. 

```swift
import UIKit

class ViewController: UIViewController, Storyboarded {
    weak var coordinator: MainCoordinator?

    @IBAction func buyTapped(_ sender: Any) {
        coordinator?.buySubscription()
    }
    
    @IBAction func createAccountTapped(_ sender: Any) {
        coordinator?.createAccount()
    }
}

class BuyViewController: UIViewController, Storyboarded {}
class CreateAccountViewController: UIViewController, Storyboarded {}
```

그리고 MainCoordinator를 수정해야 합니다. 위에서 말한 것처럼 coordinator?.buySubscription()과 같은 메소드가 실행이되려면 coordinator가 nil이 아니어야겠죠? 그래서 start() 함수에서 vc.coordinator = self 해줍니다. 

그리고 buySubscription(), createAccount() 함수를 채워주면 되는데요. 둘 다 Storyboarded 프로토콜을 준수하기 때문에 간단하게 vc를 생성할 수 있습니다. 그리고 앱을 실행해보면 정상적으로 화면이 이동하는 것을 볼 수 있어요. 

```swift
import Foundation
import UIKit

class MainCoordinator: Coordinator {
    var childCoordinators: [Coordinator] = []
    var navigationController: UINavigationController
    
    init(navigationController: UINavigationController) {
        self.navigationController = navigationController
    }
    
    func start() {
        let vc = ViewController.instantiate()
        vc.coordinator = self
        navigationController.pushViewController(vc, animated: true)
    }
    
    func buySubscription() {
        let vc = BuyViewController.instantiate()
        navigationController.pushViewController(vc, animated: true)
    }
    
    func createAccount() {
        let vc = CreateAccountViewController.instantiate()
        navigationController.pushViewController(vc, animated: true)
    }
}
```
여기까지는 기본적인 코디네이터 패턴을 알아봤습니다. 특이한 점이 뭐가 있죠? 글의 도입부에서 말했던대로 ViewController는 BuyViewController, CreateAccountViewController 를 알지 못합니다. 그저 coordinator 라는 인스턴스를 가지고 있고, coordinator 에서 특정 함수를 호출할 뿐이죠. 모든 세부적인 작업은 MainCoordinator에서 실행됩니다. 

그리고 지금부터는 코디네이터 패턴을 사용하는 더 세부적인 방법에 대해서 이야기해보도록 하겠습니다.

## 2. child coordinators를 사용하는 방법 

![](https://velog.velcdn.com/images/dev_kickbell/post/a4f18ec9-12d0-4c12-9f1d-51ad144a9ffc/image.png)

앱이 거대해지면 위의 그림처럼 각 기능이나 탭별로 코디네이터를 나눠주는 것이 좋습니다. 그래서 위에서 구현한 기본적인 코디네이터를 예시로 코드를 조금 바꿔보겠습니다. 코드는 생각보다 간단합니다. 

일단, 코디네이터를 나눌 것이므로 BuyCoordinator를 하나 더 생성해줍니다. 코디네이터 프로토콜을 준수하고, start()에는 MainCoordinator에서 buySubscription() 함수에 구현했던 코드를 그대로 복사해올게요. 

다음으로 MainCoordinator를 수정해주어야 하는데요. buySubscription() 함수내부에서 BuyCoordinator의 인스턴스를 생성합니다. 그리고 MainCoordinator의 childCoordinators에 새로 만든 인스턴스를 추가해주고 child.start()를 해주는 것이죠. 

그리고 앱을 실행해보면 이전과 똑같이 정상적으로 동작합니다. 간단하죠? CreateAccountCoordinator 또한 같은 방법으로 구현해줄 수 있을 겁니다. 

```swift
import Foundation
import UIKit

class BuyCoordinator: Coordinator {
    var childCoordinators: [Coordinator] = []
    var navigationController: UINavigationController
    
    init(navigationController: UINavigationController) {
        self.navigationController = navigationController
    }
    
    func start() {
        let vc = BuyViewController.instantiate()
        navigationController.pushViewController(vc, animated: true)
    }
}

class MainCoordinator: Coordinator {
    //...
    func buySubscription() {
        let child = BuyCoordinator(navigationController: navigationController)
        childCoordinators.append(child)
        child.start()
    }
    //...
}
```


## 3. 이전 view controller로 돌아가기

이전 view controller로 돌아가기는 단순히 돌아가는 것으로만 끝나는 것은 아닌데요. 왜냐하면, child 코디네이터를 추가할 때 MainCoordinator에서 childCoordinators.append(child) 와 같은 식으로 childCoordinators 배열에 child를 추가해줬기 때문에 그것을 적절히 제거되도록 해주어야 합니다. 

이럴 땐 어떤 trigger가 필요할거에요. 예를 들어, BuyViewController 에서 어떤 구매작업을 완료하고 화면을 pop한다고 생각해보죠. 그러면 BuyCoordinator 의 작업은 끝났으니 그것을 MainCoordinator 에서 감지해서 childCoordinators 배열에서 제거해주는 작업이 필요하겠죠. 

단순하게 구현하려면 BuyCoordinator 에서 weak var parentCoordinator: MainCoordinator? 라는 부모 코디네이터의 인스턴스를 가지고 있다가 BuyCoordinator 에서 특정 메소드를 실행해서 콜백하고 부모 코디네이터에게 알리는 방법이 있을거에요. 

하지만, 코디네이터 패턴을 처음 제시한 khanlou는 더 나은 솔루션을 제시해주었어요. 바로 UINavigationControllerDelegate를 사용하는 방법인데요. 애초에 자식 코디네이터에서 콜백을 받거나 하기보다 MainCoordinator에서 내비게이션 컨트롤러와의 상호 작용을 직접 감지하도록 만드는 것이죠.

먼저 MainCoordinator 클래스에서 UINavigationControllerDelegate 를 준수해줍니다. 그리고 NSObject 를 상속해야 합니다. 그리고 start()에서 navigationController.delegate = self 를 지정해줍니다. 그러면 이제 우리는 컨트롤러가 보여질 때마다 감지할 수 있겠죠. 

`navigationController(_:didShow:animated:)` 메소드를 작성합니다. 먼저 fromViewController 를 가져옵니다. 여기서 조금 헷갈릴 수 있는 부분이 그거에요. 예를 들어 Main -> Buy 로 이동한다고 해보죠. didShow 가 호출되면 델리게이트 메소드가 실행되겠죠? 그럼 이 상황에서 아래의 `navigationController.viewControllers, viewController, fromViewController` 를 print 해보면 `[Main, Buy], Buy, Main` 이 출력됩니다. 이상하지 않죠? 

그런데, 반대로 Buy -> Main 으로 뒤로가기를 한다고 하고 똑같이 `navigationController.viewControllers, viewController, fromViewController` 를 print 해보면 `[Main], Main, Buy` 가 출력됩니다. 즉, 이 말은 뭐냐면 navigationController.viewControllers는 현재 네비게이션 스택에 있는 뷰컨이 출력되는 것이겠고, viewController 같은 경우는 현재 이동이 완료되서 뜬 viewController 가 출력되고, fromViewController 은 앞으로갔던 뒤로갔던 출발한 쪽의 뷰컨이 출력되는 것이라는 거죠. 

다시 돌아와서, 우리는 지금 MainCoordinator의 childCoordinators 배열에서 자식 코디네이터를 제거해주려고 하는 거죠? 그러면 아래 코드처럼 작업해주면 됩니다. 먼저 from을 가져옵니다. 출발하는 쪽이나 닫히는 쪽이 되겠죠. 그리고 navigationController.viewControllers 에서 from 이 포함됐는지를 체크합니다. 만약에 from이 포함됐다면? 그건 pop이 아니라 push 라는 거겠죠? 그러면 우리한테는 의미가 없으니 return 합니다. 마지막으로 from를 BuyViewController로 특정하고, 거기서 coordinator 인스턴스를 새로 생성한 childDidFinish()로 넘겨서 childCoordinators 배열에서 제거해줍니다. 


```swift
import Foundation
import UIKit

class MainCoordinator: NSObject, Coordinator, UINavigationControllerDelegate {
    var childCoordinators: [Coordinator] = []
    var navigationController: UINavigationController
    
    func start() {
        navigationController.delegate = self
	//...
    }
    
    func buySubscription() {
        let child = BuyCoordinator(navigationController: navigationController)
        childCoordinators.append(child)
        child.start()
    }
    
    //... 
    
    func childDidFinish(_ child: Coordinator?) {
        for (index, coordinator) in childCoordinators.enumerated() {
            if coordinator ¹=== child {
                childCoordinators.remove(at: index)
                break
            }
        }
    }
    
    func navigationController(_ navigationController: UINavigationController, didShow viewController: UIViewController, animated: Bool) {
        guard let fromViewController = navigationController.transitionCoordinator?.viewController(forKey: .from) else {
            return
        }
        
        if navigationController.viewControllers.contains(fromViewController) {
            return
        }
        
        if let buyViewController = fromViewController as? BuyViewController {
            childDidFinish(buyViewController.coordinator)
        }
    }
}
```

추가적으로 child가 여러개라면 childDidFinish 부분은 이렇게 수정해주는 것이 좋을 것 같아요. if 문이 너무 많아지는 것보다는 더 간결해보입니다. 

```swift
    func navigationController(_ navigationController: UINavigationController, didShow viewController: UIViewController, animated: Bool) {
	//...

	//before
        if let buyViewController = fromViewController as? BuyViewController {
            childDidFinish(buyViewController.coordinator)
        }
        
        //after
        switch fromViewController {
        case let vc as BuyViewController: childDidFinish(vc.coordinator)
        case let vc as CreateAccountViewController: childDidFinish(vc.coordinator)
        default:
            break
        }
    }
```

## 4. vc to vc로 데이터를 전달하기

vc to vc 사이의 데이터 전달을 코디네이터로 하는 것은 얼핏 복잡해보일 수도 있습니다. 하지만, 하드코딩이 아니기 때문에 오히려 결합도가 떨어져서 더 좋습니다. 코드도 어렵지 않아요. 예시를 보겠습니다. 

ViewController에 segmentControl을 하나 추가합니다. 제품 타입을 컴퓨터/자전거로 지정할게요. 그리고 BuyViewController를 실행할 때, 무슨 타입을 Buy하려고 하는지 넘겨주려고 합니다. 

MainCoordinator의 buySubscription(_ productType: Int)로 수정하고, BuyViewController에 var selectedProduct: Int = 0 이라는 변수를 추가합니다. 그리고 여기서는 child가 있으니 BuyCoordinator에서 buySubscription()라는 메소드를 새로 작성해주었습니다. start()는 프로토콜에 포함되어있고, 파라미터가 따로 없으니 사용하지 않을게요. 그리고 BuyViewController의 viewDidLoad 에서 selectProduct의 변수값을 출력해보면 선택한 구매 타입에 따라 다르게 출력되는 것을 볼 수 있습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/73cd35f0-9d58-47c9-ba17-4b79fb0f806f/image.png)


```swift
class MainCoordinator: NSObject, Coordinator, UINavigationControllerDelegate {
    //...
    func buySubscription(_ productType: Int) {
	//...
        child.buySubscription(productType)
    }
    //...
}

class ViewController: UIViewController, Storyboarded {
    //...
    @IBOutlet weak var product: UISegmentedControl!
        
    override func viewDidLoad() {
        super.viewDidLoad()
        product.setTitle("컴퓨터", forSegmentAt: 0)
        product.setTitle("자전거", forSegmentAt: 1)
    }
    //...
}
    
class BuyCoordinator: Coordinator {
    //...    
    func buySubscription(_ productType: Int) {
        let vc = BuyViewController.instantiate()
        vc.selectedProduct = productType
        vc.coordinator = self
        navigationController.pushViewController(vc, animated: true)
    }
    //...
}

class BuyViewController: UIViewController, Storyboarded {
    weak var coordinator: BuyCoordinator?
    var selectedProduct: Int = 0

    override func viewDidLoad() {
        super.viewDidLoad()
        print("selectedProduct: \(selectedProduct)")
    }
}
```

여기에서의 핵심은 ViewController가 다른 BuyViewController 같은 애들이 존재한다는 것을 모른다는 점입니다. ViewController는 그저 코디네이터에게 요청할 뿐입니다. 그리고 데이터 전달이나 어느 화면으로 이동할지는 모두 코디네이터가 결정합니다. 

## 5. tab bar controller에서의 코디네이터

![](https://velog.velcdn.com/images/dev_kickbell/post/406cfd6b-2430-44d2-9da6-3635277ff9a4/image.png)

탭바에서의 코디네이터 패턴의 활용을 이야기해보겠습니다. 결론부터 간단히 말하면 탭을 각자 하나의 코디네이터로 지정하면 되요. 이전까지는 MainCoordinator가 BuyCoordinator, CreateAccountCoordinator 를 가지고 있었다고 하면 탭바에서는 각 코디네이터를 하나의 탭으로 볼 수 있을 겁니다. 

먼저 MainTabBarController를 생성합니다. 그리고 탭을 3개로 지정하기위해 각각의 코디네이터 인스턴스를 생성합니다. 이어서 각 start() 함수를 호출한 후, viewControllers 배열에 각 코디네이터의 네비게이션 컨트롤러를 넣어주는 것이죠. 

```swift
import UIKit

class MainTabBarController: UITabBarController {
    let main = MainCoordinator(navigationController: UINavigationController())
    let buy = BuyCoordinator(navigationController: UINavigationController())
    let createAccount = CreateAccountCoordinator(navigationController: UINavigationController())

    override func viewDidLoad() {
        super.viewDidLoad()
        main.start()
        buy.start()
        createAccount.start()
        
        viewControllers = [
            main.navigationController,
            buy.navigationController,
            createAccount.navigationController
        ]
    }
}
```

다음으로는 SceneDelegate 를 수정해주어야 합니다. 코디네이터 변수가 필요없으니 다 주석처리하고 rootViewController에 MainTabBarController를 생성해서 할당해줍니다. 

```swift
class SceneDelegate: UIResponder, UIWindowSceneDelegate {
//    var coordinator: MainCoordinator?
    var window: UIWindow?

    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        guard let windowScene = (scene as? UIWindowScene) else { return }
        
//        let navController = UINavigationController()
//        coordinator = MainCoordinator(navigationController: navController)
//        coordinator?.start()
        
        window = UIWindow(windowScene: windowScene)
        window?.rootViewController = MainTabBarController()
        window?.makeKeyAndVisible()
    }
}
```

마지막으로 탭바아이템을 생성해서 넣어줄거에요. 각 코디네이터에서 vc를 생성하고, tabBarItem 을 생성해서 할당해주면 됩니다. 그러면 아래의 그림처럼 탭바가 보이는 것을 볼 수 있죠. 

```swift
class MainCoordinator: Coordinator {
    func start() {
        let vc = ViewController.instantiate()
        vc.tabBarItem = UITabBarItem(tabBarSystemItem: .favorites, tag: 0)
        vc.coordinator = self
        navigationController.pushViewController(vc, animated: true)
    }
}

class BuyCoordinator: Coordinator {
    func start() {
        let vc = ViewController.instantiate()
        vc.tabBarItem = UITabBarItem(tabBarSystemItem: .bookmarks, tag: 1)
        vc.coordinator = self
        navigationController.pushViewController(vc, animated: true)
    }
}

class CreateAccountCoordinator: Coordinator {
    func start() {
        let vc = ViewController.instantiate()
        vc.tabBarItem = UITabBarItem(tabBarSystemItem: .contacts, tag: 2)
        vc.coordinator = self
        navigationController.pushViewController(vc, animated: true)
    }
}
```
하나 재밌는 점은, MainCoordinator 에서 UINavigationControllerDelegate 프로토콜을 준수하고 있지 않다는 거겠죠? 왤까요? 네, 현재상으로는 MainCoordinator 는 자식을 가지고 있지 않은 하나의 코디네이터 뿐이기 때문입니다. 만약에 앱이 더 거대해져서 MainCoordinator 가 다른 자식 코디네이터를 갖게 된다면 그때 같은 방식으로 추가해주면 될 거에요.

![](https://velog.velcdn.com/images/dev_kickbell/post/9bb51aa6-b2be-4db4-976c-29e27fbd94fc/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/0d3e4bff-cce9-4c8a-bf89-cdd6a8e25cbe/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/0ef111c8-01b0-47d1-b4b3-90ccbf02afe2/image.png)


## 6. segue 사용하기

세그웨이는 뷰 컨트롤러 사이에 링크를 생성하여 스토리보드에 추가되고 iOS에 의해 자동으로 트리거되거나 세그웨이 prepare 함수를 호출하여 트리거됩니다.

문제는 세그웨이는 a -> b 와 같은 식으로 스토리보드에서 특정하거나, prepare() 함수에서도 segue.identifier 값을 기준으로 도착하는 뷰 컨트롤러를 특정하기 때문에 코디네이터 패턴의 주요 이점 중 하나를 무효한다는 것입니다.

결론적으로 코디네이터 패턴을 처음 제시한 khanlou의 답변은 "그냥 segue를 버려라"는 것입니다. 스토리보드를 UI의 도구로 사용하지 말라는 뜻이 아닙니다. 뷰 컨트롤러의 디자인은 얼마든지 하되, segue를 사용하지 말라는 것이죠. 코디네이터 패턴의 요점은 뷰 컨트롤러를 분리하는 것이고 세그웨이를 제거하는 것이 정확히 그렇게 하기 때문입니다.

## 7. Coordinator vs Delegate

코디네이터를 사용할 때 사람들을 혼란스럽게 하는 한 가지는 그들이 델리게이트와 어떻게 다른가 하는 것입니다. 실제로 현재 코드에서 코디네이터의 이름을 델리게이트로 바꿔도 동작은 잘 합니다. 

그럼 뭐가 다를까요? 결론적으로 말하면 그냥 네이밍의 명료함의 차이일 뿐입니다. 무슨 말이냐면, 델리게이트는 엄청 많죠? 테이블뷰, 네비게이션, 스크롤뷰 등등.. 대리자의 역할을 하죠. 

이름을 델리게이트로 해도 동작은 잘 합니다. 하지만, 조금 혼란스러울 수 있어요. 우리가 일반적으로 생각하는 델리게이트에 대한 지식이 있기 때문입니다. 이것을 코디네이터로 명명하면 "아 얘는 앱의 네비게이션을 처리하는 녀석이구나" 라고 분명하게 인지할 수 있게 됩니다. 그게 전부입니다.

## 8. 코디네이터대신 protocol, closure 사용하기

#### protocol

코디네이터 대신 프로토콜을 사용할 수도 있습니다. 이것의 이점은 유연성이 증가하기 때문에 더 큰 앱에서 좋은 아이디어입니다. 

코드를 볼게요. 기존의 코디네이터 역할을 하던 BuyCoordinator, CreateAccountCoordinator 대신에 Buying, AccountCreating 이라는 프로토콜을 생성합니다. 그리고 MainCoordinator에서 생성한 프로토콜을 준수하고 ViewController에서 coordinator 인스턴스의 타입을 MainCoordinator? 대신, (Buying & AccountCreating)? 로 변경합니다. 이미 MainCoordinator에서 2가지 프로토콜을 준수하고 있기 때문에 오류는 발생하지 않습니다. 

이렇게 하면 프로그램이 더 유연해집니다. MainCoordinator가 아닌 다른 새롭게 생성한 코디네이터로 교체를 해도 괜찮습니다. Buying과 AccountCreating 를 준수하는 코디네이터라면 말이죠. 또, 프로토콜을 새롭게 생성해서 추가해줄 수도 있습니다. 그리고 AB 테스트 등을 위해 다른 코디네이터에서 자유롭게 교체할 수도 있죠. 

```swift
protocol Buying: AnyObject {
    func buySubscription(_ productType: Int)
}
protocol AccountCreating: AnyObject {
    func createAccount()
}
class MainCoordinator: Coordinator, Buying, AccountCreating {
    //...
}

class ViewController: UIViewController, Storyboarded {
//    weak var coordinator: MainCoordinator?
    weak var coordinator: (Buying & AccountCreating)?
    //...
}
```

#### closure

클로저를 대신 사용할 수도 있습니다. ViewContorller에서 기존의 coordinator 변수를 주석처리하고 콜백클로저를 2개 생성합니다. 그리고 @IBAction 내부도 수정해주고, MainCoordinator 에서 콜백클로저를 작성해주기만 하면 됩니다. 

실행해보면 동작은 똑같이 실행됩니다. 여전히 같은 결과를 얻지만 ViewContorller는 코디네이터가 내비게이션을 제어하고 있다는 사실을 전혀 모릅니다. 하지만 이 방법은 콜백클로저가 1~2개만 있을 때 유용합니다. 10개가 있다고 콜백클로저를 10개씩 만드는 것은 바람직하지 않을테니까요. 

개인적으로는 프로토콜, 클로저를 사용하는 방법보다 더 명료하게 그냥 코디네이터를 사용하는 방법이 더 좋은 것 같습니다. 끝으로 글의 주제가 되는 유투브 영상 링크와 khanlou의의 블로그 글을 아래에 첨부해두었습니다. 글에 사용된 코드가 필요하시다면 [여기](https://github.com/kickbell/Coordinator.git)에 있습니다.

```swift
import UIKit

class ViewController: UIViewController, Storyboarded {
//    weak var coordinator: MainCoordinator?
    
    var buyAction: ((Int) -> Void)?
    var createAccountAction: (() -> Void)?

    //...
    
    @IBAction func buyTapped(_ sender: Any) {
//        coordinator?.buySubscription(product.selectedSegmentIndex)
        buyAction?(product.selectedSegmentIndex)
    }
    
    @IBAction func createAccountTapped(_ sender: Any) {
//        coordinator?.createAccount()
        createAccountAction?()
    }
}
```
```swift
class MainCoordinator: Coordinator {
    //...
    
    func start() {
        navigationController.delegate = self
        let vc = ViewController.instantiate()
        vc.tabBarItem = UITabBarItem(tabBarSystemItem: .favorites, tag: 0)
        vc.buyAction = { [weak self] productType in
            self?.buySubscription(productType)
        }
        vc.createAccountAction = { [weak self] in
            self?.createAccount()
        }
//        vc.coordinator = self
        navigationController.pushViewController(vc, animated: true)
    }
    
    //...
}
```
    
## Reference

[https://youtu.be/7HgbcTqxoN4](https://youtu.be/7HgbcTqxoN4)				  		
				
[https://youtu.be/ueByb0MBMQ4](https://youtu.be/ueByb0MBMQ4)					
				
[https://khanlou.com/2015/01/the-coordinator/](https://khanlou.com/2015/01/the-coordinator/)				


## Endnotes 

### ¹===
`a === b`: a가 참조하고 있는 인스턴스와 b가 참조하고 있는 인스턴스가 같은지를 비교, class 타입에서만 사용가능

```swift
let s1 = Student(id: 1, name: "kim")
let s2 = Student(id: 1, name: "kim")
let s3 = s1

print(p1 === p2) // false
print(p1 === p3) // true
```

