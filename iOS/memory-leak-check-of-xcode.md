# Xcode에서 메모리 누수 확인하기


## 1. 샘플 프로젝트 생성하기 

![](https://images.velog.io/images/dev_kickbell/post/0770200f-743b-483d-93eb-b8c5cac758fb/image.png)


```swift
import UIKit

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
    }
}



import UIKit

// MARK: - 델리게이트 생성
protocol MyDelegate: AnyObject {
    var name: String { get }
}
class MyView: UIView {
    weak var delegate: MyDelegate?
//    var delegate: MyDelegate?
}

// MARK: - 델리게이트 채택
class DetailViewController: UIViewController, MyDelegate {
    var name: String = ""
    let myview = MyView()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        print(#function, #fileID)
        myview.delegate = self
        view.addSubview(myview)
    }
    
    override func viewDidDisappear(_ animated: Bool) {
        super.viewDidDisappear(animated)
        print(#function, #fileID)
    }
    
    @IBAction func previous(_ sender: Any) {
        print(#function, #fileID)
        dismiss(animated: true, completion: nil)
    }
    
    deinit {
        print(#function, #fileID)
    }
}

```


## 2. 메모리 누수 관련 설정하기 
 
 Edit Scheme에 들어가서 Malloc Scribble, Malloc Stack Logging을 체크한다. 
 
 ![](https://images.velog.io/images/dev_kickbell/post/ff02e643-c79e-416b-88d0-19e8cffb704f/image.png)
 ![](https://images.velog.io/images/dev_kickbell/post/a6151725-72f2-4d11-8d27-c6bc2aa383f2/image.png)
 
 
 
## 3. 확인하기 
 
 ### weak var delegate: MyDelegate? 일 때 (약한참조일때)
 
 프레젠드, 디스미스를 여러번해보면 콘솔에 아래와 같이 deinit이 잘 호출되는 것을 볼 수 있다. 
 
```
viewDidLoad() MemoryLeak/DetailViewController.swift
previous(_:) MemoryLeak/DetailViewController.swift
viewDidDisappear(_:) MemoryLeak/DetailViewController.swift
deinit MemoryLeak/DetailViewController.swift
viewDidLoad() MemoryLeak/DetailViewController.swift
previous(_:) MemoryLeak/DetailViewController.swift
viewDidDisappear(_:) MemoryLeak/DetailViewController.swift
deinit MemoryLeak/DetailViewController.swift
```


또한, 메모리 그래프를 클릭하고 
 
 ![](https://images.velog.io/images/dev_kickbell/post/24178bea-30e4-47bc-9144-072089843798/image.png)
 
 디버그 네비게이터를 열고 

![](https://images.velog.io/images/dev_kickbell/post/50509647-a24f-4790-9d03-51ac8c034291/image.png)
 
 하단에 `show matching content based on fillter string`을 눌러준다. 
 (이건 앱내에서 사용하는 기본 제공 프레임워크 같은 애들을 보여주느냐 마느냐의 여부이다. 얘는 클릭하고 왼쪽에 애는 클릭하지않으면 된다.)
 
 ![](https://images.velog.io/images/dev_kickbell/post/f0918f21-b89f-474c-805b-9777e1b50119/image.png)
 
 
 그러면 상태가 아래와 같다. 
 앱델리게이트와 씬델리게이트 다음엔 뷰컨트롤러만 존재한다. 
 이제 비교해보자. 
 
 ![](https://images.velog.io/images/dev_kickbell/post/71a95dfa-7171-4cf7-8ab0-526b1f73a417/image.png)
 
 
### weak delegate: MyDelegate? 일 때 (강한참조일때) 

 똑같이 프레젠트, 디스미스를 여러번 수행한다. 
 그리고 콘솔을 보면 아래와 같이 deinit이 호출되지 않는 것을 볼 수 있다. 
 
```
viewDidLoad() MemoryLeak/DetailViewController.swift
previous(_:) MemoryLeak/DetailViewController.swift
viewDidDisappear(_:) MemoryLeak/DetailViewController.swift
viewDidLoad() MemoryLeak/DetailViewController.swift
previous(_:) MemoryLeak/DetailViewController.swift
viewDidDisappear(_:) MemoryLeak/DetailViewController.swift
viewDidLoad() MemoryLeak/DetailViewController.swift
previous(_:) MemoryLeak/DetailViewController.swift
viewDidDisappear(_:) MemoryLeak/DetailViewController.swift
```

또 같은 화면을 보면 아까와 다르게 앱델리게이트와 씬델리게이트 사이에 
디테일 뷰컨트롤러와 디테일 뷰컨트롤러 위에올라간 myView가 (3)이 있는걸 볼 수있다. 아직 메모리에 남아있다는 것이다. 

![](https://images.velog.io/images/dev_kickbell/post/af80aa3f-2e43-447b-a424-7e88ec54473d/image.png)



다시 재생해서 추가로 3번 프레젠트, 디스미스를 하고 열어보자. 
 그러면 (3)이 (6)으로 올라간 것을 볼 수 있다. 메모리가 계속 누수되고 있는 것이다. 이게 쌓이고 쌓이고 또싸이면 터져서 스택오버플로우가 난다. 
 
 
 ![](https://images.velog.io/images/dev_kickbell/post/4ef02389-472d-493f-ad03-2f7e62e8b7c5/image.png)
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
