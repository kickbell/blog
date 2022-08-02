이 글은 [베어코드](https://www.youtube.com/channel/UCEuyt4RB4mlMfADQMz-d2DQ) 님의 [Swift Object Oriented Progoramming](https://youtube.com/playlist?list=PLoPKxuu4_dG3jCiMcbskRuNsgZsdZ4zz6) 을 보고 정리한 글 입니다. 




> ## 요약 
- 처음 제한한 사람은 딱히 없는듯, 그냥 로버트.C.마틴이 다모아서 SOLID라고 했으니 얘인걸로.
- 어떤 클래스든 본인은 본인일만 해야된다. 
- 대표적인 잘못된 예로 UIViewControlelr를 들 수 있다. 고전 코코아 프레임워크는 뷰컨트롤러가 없다. 그런데 부족한 메모리, 뷰라이프싸이클관리를 이유로 모바일로 넘어오면서 뷰컨틀롤러가 생겼다. 
- 문제는 얘가 너무 많은 일을 한다는 것이다. 컨트롤러의 역할은 뷰,모델간의 바인딩이지만, 레이아웃을 그리기도하고 뷰라이프싸이클을 관리하기도 한다. 이건 대표적인 SRP 위배사항이다. 
- 뷰컨이아니라 뷰매니저(뷰라이프싸이클관리), 레이아웃매니저(레이아웃관리), 컨트롤러(뷰,모델간의 바인딩) 같은 식으로 정의되었으면 더 좋았을 것이다. 
- MVP, MVVM, VIPER 같은 디자인 패턴 또한 각각의 역할을 SOLID의 관점에서 나눠주려고 한 고민의 결과물에 불과하다. 그것에 따라서 이름이 다르게 붙여질 수도 있는것이다. 라우터, 프레젠터, 같은것들처럼... 





Single Responsibility Principle (단일 책임 원칙)
- 하나의 클래스는 하나의 책임만 가져야 한다.

**_`단일`_** 의 의미는 상대적이다. 앱 전체가 단일이 될 수도 있고, 두 개의 숫자를 더하는 작은 기능이 단일이라고 할 수도 있다. 또는 특정 뷰 컨트롤러 하나가 단일의 의미가 될 수도 있다. 

되도록이면 작은 범위의 단위를 정의해야 하지만, 그렇게 되면 정말 밑도끝도없이 단일의 범위가 작아지게 된다. 
그래서 단일을 `책임`과 함께 고려해서 적당한 `단일`의 의미를 선택해야 한다. 


책임이란 ? 
SRP에서는 책임을 `변경을 위한 이유`라고 표현하고 있는데 무슨말인지 이해가 잘 안간다. 

추가적으로 설명하자면, 

만일 "특정기능을 변경하기 위해 여러 클래스를 고쳐야 한다."면 설계가 잘못되었을 가능성이 크다. 하나의 기능을 구현하는 코드가 응집력을 갖지 못하고 여기저기 흩어져있다는 뜻이기 때문이다. 동일한 수정은 한 곳에서 이루어져야 한다. SRP의 위반사례라고 볼 수 있다. 

또, 반대로 "특정 기능을 수정하기 위해서 코드를 수정했는데 클래스의 대부분이 수정되지 않았거나, 극히 일부분만 수정됐다면 이것 역시 SRP의 위반사례라고 볼 수 있다. 그 클래스는 수정된 기능 이외의 여러가지 기능을 가지고 있다는 이야기가 되기 때문이다. 

만일 어떤 클래스가 있고 논리적으로 단일기능으로 판단되나, 차후에 변경될 여지가있고 변경될 부분이 있다면 그 변경될 부분을 따로 클래스를 만드는 것이 좋다. 그래야 SRP를 지키는 모양새가 된다. 


로버트 C 마틴은 모뎀이라는 클래스로 이를 설명하고 있다. 
그냥 봤을 때, 이것은 당연하게도 `연결`, `종료`, `전송`, `수신`의 기능을 가지고 있는 모뎀이다.


![](https://images.velog.io/images/dev_kickbell/post/b2298eba-7868-4f37-9263-e26fb97eb975/image.png)


하지만, 여기서 `다이얼`의 역할이 세분화 된다고 생각해보자. 다이얼의 커넥션 방식이 `2G/3G/4G/5G/Wi-Fi`과 같은식으로 세분화 될 수 있다. 그러면 이 클래스는 아래와 같이 될 것이다. 

![](https://images.velog.io/images/dev_kickbell/post/79ce7672-3a8d-4287-87d0-3a3ea62a82d1/image.png)

그리고 이렇게 된 상태에서 case a, b, c의 기능이 각각 수정될 수 있다. 그렇게 된다면 이건 각각의 다른 클래스로 구분되어야 한다고 마틴은 말한다.
![](https://images.velog.io/images/dev_kickbell/post/957e0788-5258-4cf6-9d64-a09dcb926e48/image.png)

그렇게 된다면 위 그림처럼 dial 이라는 변수를 Dial이라는 별도의 클래스로 만들고, 각각의 기능들 또한 Dial A,B,C로 구현되게 된다. 이런 Dial 클래스들은 매우 응집도가 높고 결합도가 낮은 클래스가 된다. 만약 Dial A에 대한 변경이 필요하다면, Dial A만 수정하면 된다. 이것은 SRP를 처음 설명했을 때 처럼, 하나의 클래스는 하나의 책임만을 갖는다는 설명에 부합되며, `책임은 변경을 위한 이유`라고 설명한 것에도 부합된다. 반면 Dial처럼 확장/변경이 필요없는 경우에 클래스를 분리한다면 그것은 불필요한 복잡성을 유발한다고 이야기 한다. 

즉, SRP가 추구하는 그리고 준수하는 클래스의 특징은 아래와 같다. 
- 응집도가 낮음 
- 결합도가 낮음 
- 특정 기능의 변경을 위한 수정이 한곳에 집중 

이것의 장점은 이미 응집도와 결합도에서 공부했듯이 알고 있다. 이정도만 되도 코드의 퀄리티는 높아진다. 

### SRP를 지키는 클래스의 응용
- 각종 디자인 패턴들 
	- Abstract Factory : 생성을 위한 추상화 인터페이스와 구체적인 팩토리로 만들어진다. 팩토리들은 SRP를 준수하기 때문에 수정할 때 하나의 구체화된 팩토리만 수정하면 된다. 다른 팩토리들과 영향을 주고 받지 않는다. 
	- Bridge : 추상팩토리패턴과 마찬가지로 추상화 인터페이스와 구현체로 이루어지고, 구현체들은 SRP들을 준수한다. 그러므로 각 독립적이다.
	- State, Strategy, Command : 이것들 역시 각각 개념은 다르지만 독립된다는 것은 같다. 
- SOLID의 다른 영역들(OCP, LSP, ISP, DIP)은 SRP를 근간으로 한다. 

### ViewController

고전 코코아프레임워크는 뷰컨이 없다. 그런데 부족한 메모리, 또는 뷰라이프싸이클 관리라는 이유로 viewcontroller가 나오게 되었다. 실제로 macos(고전적인 코코아 프레임워크)에서는 view lifecycle을 관리하지 않았고, 레이아웃은 nib을 주로 이용했다. 그래서 nsobject를 상속받은 controller는 mvc에서 말하는 controller의 역할만 충실히 수행했따. 

그러나 view-model간의 바인딩을 담당하는 컨트롤러가 viewcontroller라는 이름으로 나오게 되면서 뭔가 꼬인거다. 기존의 controller + layout + view life cycle 까지 되어버린거다. 

- viewmanager -> view life cycle  
- layoutmanager -> layout 
- controller -> view, model 간의 binding 



### iOS에서의 대표적인 SRP위반 사례

iOS에서의 대표적인 SRP위반 사례로 가장 많이 쓰는 ViewController의 서브클래스를 말하고 있다. 그런데 여기서 말하는 서브클래스가 아래와 같은 클래스들을 말하는 건지, 아니면 우리가 아무렇게나 이름지어 만들어 사용하는 그런 뷰컨트롤러들 (그것도 UIViewController를 상속받고있으니)을 말하는 것인지는 모르겠다 .
```swift
    UITableViewController
    UIAlertController
    UINavigationController
    UISearchController
    UITabBarController
    UISplitViewController
    UIPageViewController
    UIInputViewController
```

그리고 디자인 패턴으로 MVP, MVVM, VIPER가 있는데 이것들의 의미를 아는 것보다 SRP를 위반하지 않기 위해 고민해서 만들어진 것들이 바로 MVP, MVVM, VIPER 패턴들이라는 것이다. 

- ViewController가 SRP를 위반하고 있다고 판단하는 근거는 ? 
![](https://images.velog.io/images/dev_kickbell/post/519ab2d4-d452-45a7-95a5-b7d1ef07e37d/image.png)
![](https://images.velog.io/images/dev_kickbell/post/a52a5916-2d9c-4fe1-8154-9c3e3765cb65/image.png)

애플 문서에 따르면 앱의 뷰 계층 구조를 관리하는 개체라고 표현하고 있다. 

지금 만들어놓은 아무 뷰컨트롤러에 들어갔을 때, 그 뷰컨트롤러에서 데이터를 패치하고 있다면 SRP위반이다.
패치형 데이터를 관리하고 있어도 SRP위반이고, 
뷰컨트롤러에서 뷰의 레이아웃을 밀접하게 관리하고 있다면 그것도 SRP위반이다.
더욱이 레이아웃이 여러가지 형태가 될 가능성이 있다면 그것도 SRP위반이다.

우리가 알고있는 것은 MVC에서 컨트롤러는 뷰와 모델을 이어주는 존재라고 알고 있다. 하지만 애플의 정의에 따르면 뷰컨트롤러는 뷰를 `관리`하는 역할을 하고 있어야 한다. 하지만 거기에 model을 처리하는 로직이 추가되고 있다. 예를들면, 테이블뷰의 델리게이트/데이터소스 라던지. 이것이 뷰를 관리하는 역할일까? 아니다. 

MVP, MVVM, VIPER 모두 공통적으로 이 문제를 해결하기 위해 제시되었다. 즉, 이녀석들은 모두 뷰컨트롤러를 뷰라고 생각한다. 

원래 mac OS에서는 뷰컨트롤러라는 것이 존재하지 않았다. iOS가 나오면서 1) 메모리 부족 또는 2) 뷰의 라이프싸이클을 관리해줘야 하는 것을 이유로 뷰컨트롤러라는 것이 나오게 되었는데, 문제는 이름이 뷰'컨트롤러'라는 것이다. 

실제 이 뷰컨트롤러의 본연의 역할은 위에 정의에서 나와있는것처럼 뷰를 관리하는 것이다. 

만약에 뷰컨트롤러에서 아래처럼 서브클래스로 관리되었으면 어땠을까. 
1. 뷰매니저클래스 : 뷰라이프싸이클 관리 
2. 레이아웃매니저클래스 : 뷰레이아웃관리 
3. 뷰컨트롤러 : 뷰컨트롤러 본연의 업무인 뷰와 모델간의 바인딩

실제로 기존의 코코아프레임워크는 레이아웃은 nib을 주로 이용했고, 뷰라이프싸이클 관리는 하지 않았기 때문에 뷰라이프싸이클과 레이아웃까지 관리하는 녀석이 아니었다. NSObject를 상속받은 컨트롤러는 MVC에서 말하는 본연의 역할에 충실했었다. 


### MVP 

MVC와 유사하나 관점이 다르다. MVP에서 ViewController는 View로 취급된다. 
기존의 컨트롤러의 역할은 Presenter가 한다. 대신 MVP는 뷰와의 관계를 끊는 형태로 구현하게 된다. 뷰와의 관계가 끊어진만큼 더욱 독립적이고 테스트하기 쉬워지는 형태이다. 
![](https://images.velog.io/images/dev_kickbell/post/afb34830-0557-470e-ba63-a23799cbc711/image.png)

### MVVM 

MVP처럼 뷰컨트롤러를 뷰로 다룬다. 뷰모델이라는 것이 있고 커맨드패턴와 데이터바인딩패턴을 적극적으로 활용한다. 
![](https://images.velog.io/images/dev_kickbell/post/d97fddcc-07e9-4ce6-945d-dd4d75a3ceed/image.png)

### VIPER

바이퍼 역시 뷰컨트롤러를 뷰로 대한다. 추가로 인터랙션과 뷰간의 라우팅(화면전환담당) 역시 철저하게 SRP관점에서 분해한다. 복잡한 앱에서는 자유도가 높지만, 단순한 앱에서는 오히려 복잡해질 수 있다. 
![](https://images.velog.io/images/dev_kickbell/post/a6d87705-a028-4a0d-a61c-cb65473d0840/image.png)


### 결론

결론적으로 보면 기본적인 개념은 동일하다. `각 역할을 기본으로 분리` 한다는 점이다. SRP를 어떻게 더 지킬 수 있을까의 결과물이고, 각 요소간의 관계를 어떻게 정의하는지 또는 역할의 분리를 어떻게 할 것인지에 따라 다른 이름을 가질 뿐이다. 

패턴들을 외우기보다 왜 그런 패턴이 나왔는지 이해하는 것이 중요하다. 

- 10-200룰은 SRP를 연습하는데 효과적인 도구 (단일과 책임에 따라 코드를 나눌 수 밖에 없으니까) 
- 단일과 책임에 대해서 고민하면서 코드를 분리하는 습관만 가져도 코드의 퀄리티가 나아진다. 


### 가장 기본적인 SRP의 적용 

ViewController. 
- View를 관리하는 부분을 제외하고 모두 별도의 class로 분리할 수 있음. 
- model을 핸들링하고 어떻게 표현할지 결정하는 presenter 
- view간에 어떻게 전환이 이루어질지 관리하는 router 
- layout을 전담해서 관리하는 layout manager 
- 등등 



### 아래 뷰컨트롤러를 보면서 생각해보자. 

- viewwillapper, viewdidapper처럼 뷰라이프 싸이클을 관리하는 부분
- present, dismiss 와 같이 이동을 관리하는 부분도 있고 
- preferredContentSize, prefersStatusBarHidden처럼 레이아웃 관리도 있다. 
- 근데 지금 이게 하나에 때려박아져있다. 그래서 문제라는 거다. 

```swift 
@available(iOS 2.0, *)
open class UIViewController : UIResponder, NSCoding, UIAppearanceContainer, UITraitEnvironment, UIContentContainer, UIFocusEnvironment {

    public init(nibName nibNameOrNil: String?, bundle nibBundleOrNil: Bundle?)

    public init?(coder: NSCoder)

    open var view: UIView!

    open func loadView()

    @available(iOS 9.0, *)
    open func loadViewIfNeeded()

    @available(iOS 9.0, *)
    open var viewIfLoaded: UIView? { get }

    open func viewDidLoad()

    @available(iOS 3.0, *)
    open var isViewLoaded: Bool { get }

    open var nibName: String? { get }

    open var nibBundle: Bundle? { get }

    @available(iOS 5.0, *)
    open var storyboard: UIStoryboard? { get }

    @available(iOS 5.0, *)
    open func performSegue(withIdentifier identifier: String, sender: Any?)

    @available(iOS 6.0, *)
    open func shouldPerformSegue(withIdentifier identifier: String, sender: Any?) -> Bool

    @available(iOS 5.0, *)
    open func prepare(for segue: UIStoryboardSegue, sender: Any?)

    @available(iOS 13.0, *)
    open func canPerformUnwindSegueAction(_ action: Selector, from fromViewController: UIViewController, sender: Any?) -> Bool

    @available(iOS, introduced: 6.0, deprecated: 13.0)
    open func canPerformUnwindSegueAction(_ action: Selector, from fromViewController: UIViewController, withSender sender: Any) -> Bool

    @available(iOS 9.0, *)
    open func allowedChildrenForUnwinding(from source: UIStoryboardUnwindSegueSource) -> [UIViewController]

    @available(iOS 9.0, *)
    open func childContaining(_ source: UIStoryboardUnwindSegueSource) -> UIViewController?

    @available(iOS, introduced: 6.0, deprecated: 9.0)
    open func forUnwindSegueAction(_ action: Selector, from fromViewController: UIViewController, withSender sender: Any?) -> UIViewController?

    @available(iOS 9.0, *)
    open func unwind(for unwindSegue: UIStoryboardSegue, towards subsequentVC: UIViewController)

    @available(iOS, introduced: 6.0, deprecated: 9.0)
    open func segueForUnwinding(to toViewController: UIViewController, from fromViewController: UIViewController, identifier: String?) -> UIStoryboardSegue?

    open func viewWillAppear(_ animated: Bool)

    open func viewDidAppear(_ animated: Bool)

    open func viewWillDisappear(_ animated: Bool)

    open func viewDidDisappear(_ animated: Bool)

    @available(iOS 5.0, *)
    open func viewWillLayoutSubviews()

    @available(iOS 5.0, *)
    open func viewDidLayoutSubviews()

    open var title: String?

    open func didReceiveMemoryWarning()

    weak open var parent: UIViewController? { get }

    @available(iOS 5.0, *)
    open var presentedViewController: UIViewController? { get }

    @available(iOS 5.0, *)
    open var presentingViewController: UIViewController? { get }

    @available(iOS 5.0, *)
    open var definesPresentationContext: Bool

    @available(iOS 5.0, *)
    open var providesPresentationContextTransitionStyle: Bool

    @available(iOS 10.0, *)
    open var restoresFocusAfterTransition: Bool

    @available(iOS 5.0, *)
    open var isBeingPresented: Bool { get }

    @available(iOS 5.0, *)
    open var isBeingDismissed: Bool { get }

    @available(iOS 5.0, *)
    open var isMovingToParent: Bool { get }

    @available(iOS 5.0, *)
    open var isMovingFromParent: Bool { get }

    @available(iOS 5.0, *)
    open func present(_ viewControllerToPresent: UIViewController, animated flag: Bool, completion: (() -> Void)? = nil)

    @available(iOS 5.0, *)
    open func dismiss(animated flag: Bool, completion: (() -> Void)? = nil)

    @available(iOS 3.0, *)
    open var modalTransitionStyle: UIModalTransitionStyle

    @available(iOS 3.2, *)
    open var modalPresentationStyle: UIModalPresentationStyle

    @available(iOS 7.0, *)
    open var modalPresentationCapturesStatusBarAppearance: Bool

    @available(iOS 4.3, *)
    open var disablesAutomaticKeyboardDismissal: Bool { get }

    @available(iOS 7.0, *)
    open var edgesForExtendedLayout: UIRectEdge

    @available(iOS 7.0, *)
    open var extendedLayoutIncludesOpaqueBars: Bool

    @available(iOS, introduced: 7.0, deprecated: 11.0, message: "Use UIScrollView's contentInsetAdjustmentBehavior instead")
    open var automaticallyAdjustsScrollViewInsets: Bool

    @available(iOS 7.0, *)
    open var preferredContentSize: CGSize

    @available(iOS 7.0, *)
    open var preferredStatusBarStyle: UIStatusBarStyle { get }

    @available(iOS 7.0, *)
    open var prefersStatusBarHidden: Bool { get }

    @available(iOS 7.0, *)
    open var preferredStatusBarUpdateAnimation: UIStatusBarAnimation { get }

    @available(iOS 7.0, *)
    open func setNeedsStatusBarAppearanceUpdate()

    @available(iOS 8.0, *)
    open func targetViewController(forAction action: Selector, sender: Any?) -> UIViewController?

    @available(iOS 8.0, *)
    open func show(_ vc: UIViewController, sender: Any?)

    @available(iOS 8.0, *)
    open func showDetailViewController(_ vc: UIViewController, sender: Any?)

    @available(iOS 13.0, *)
    open var overrideUserInterfaceStyle: UIUserInterfaceStyle
}
```


