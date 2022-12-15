# Proxy Pattern(프록시 패턴)
> - 특정 객체로의 접근을 제어하는 대리인(특정 객체를 대변하는 객체)을 제공합니다.

## 1. 프록시 패턴(Proxy Pattern)
- 프록시는 대리인이라는 의미입니다. 변형이 정말 많은데, 결론적으로는 실제 작업을 처리하는 클래스가 오픈되지 않고 프록시라는 대리인을 통해 작업을 처리하는 방식이라고 볼 수 있어요. 
- RealSubject와 Proxy는 둘 다 Subject라는 인터페이스를 준수합니다. 그러면, 어떤 클라이언트에서든 Proxy를 RealSubject처럼 대할 수 있어요. 
- Proxy에는 진짜 작업을 처리하는 RealSubject의 레퍼런스가 들어있습니다. 진짜 객체가 필요하면 이 레퍼런스를 통해서 요청을 전달합니다. 
- RealSubject는 진짜 작업을 처리합니다. Proxy는 RealSubject로는 접근을 제어하는 대리인 역할을 하지요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/47f89def-f293-4280-a93e-9e8a35e0ff03/image.png)


## 2. 앨범 커버 뷰어 설계하기
- 커버 뷰 중에 하나를 선택합니다. 그러면 네트워크 API를 통해 이미지를 가져오고 그걸 화면에 보여주는 앨범 커버 뷰어를 만들려고 합니다. 
- 그런데, 이런 방법을 사용하면 네트워크 상태와 인터넷 연결 속도에 따라서 앨범 커버를 가져오는데 시간이 걸릴 수 있죠. 그래서 이미지를 불러오는 동안 화면에 다른 것을 보여주면 좋겠어요. 
- 또, 이미지를 기다리는 동안 애플리케이션 전체가 작동을 멈춰서도 안됩니다. 비동기 작업으로 처리해야 된다는 거겠죠. 
- 여기서 프록시가 백그라운드에서 이미지를 불러오는 작업을 처리하고, 이미지를 완전히 가져오기 전까지 다른 화면을 보여줍니다. 그리고 이미지를 다 가져오면 RealSubject 객체에게 이제 화면에 그려라!고 작업을 위임하게 되죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/47644ee4-38f3-49d4-994b-34ff52fe7149/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/20afaf37-1b5f-4b4f-9db6-2c5191b94a5d/image.png)


## 3. 앨범 커버 뷰어 구현하기 
- 예제가 마땅치 않아서 Swift로 하나 그냥 만들어보았습니다. 
- 음, 일단 Subject 인터페이스를 하나 만듭니다. 그리고 ImageProxy에서 Subject 인터페이스를 준수합니다. 
- ImageProxy는 위에 나와있는 것처럼 이미지 로딩뷰와 실제 작업을 진행하는 UIImageView를 가지고 있습니다. 또 백그라운드에서 이미지를 가져오는 메소드도 있어요. 
- 그래서, url을 생성자로 받아서 draw()를 하게되면 일단은 이미지 로딩뷰를 띄워주다가 이미지가 네트워크를 통해 다운로드가 되면 다운로드 된 이미지를 실제 일을 하는 UIImageView.image = downloadedImage 해줘서 이미지를 바꿔주는 작업을 하지요. 
- 다만 여기서는 ViewController 특성상 이미지로딩뷰와 UIImageView를 먼저 addSubView 해주었습니다. 
- 어떻게 보면 간단하죠? 위에서 계속 설명한대로 프록시는 말그대로 대리인 역할을 하는겁니다. 그리고 UIImageView에 대한 접근 제어의 역할도 하고 있죠. 


![](https://velog.velcdn.com/images/dev_kickbell/post/668ec785-d5a5-4a7d-8e23-2d030030c66f/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/aeb9ad37-8fe1-46ad-8c16-7fe0d6f30f31/image.png)



<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
protocol Subject {
    func draw()
}
    
class ImageProxy: Subject {
    let url: URL
    
    init(url: URL) {
        self.url = url
    }
    
    let realView: UIView = {
        let imageView = UIImageView()
        imageView.contentMode = .scaleAspectFit
        imageView.backgroundColor = .orange
        imageView.frame = CGRect(x: 0, y: 0, width: 0, height: 0)
        return imageView
    }()
    
    let placeholderView: UIView = {
        let imageNotFoundLabel = UILabel()
        imageNotFoundLabel.numberOfLines = 0
        imageNotFoundLabel.textAlignment = .center
        imageNotFoundLabel.text = "이미지를 로드하고 있습니다. 잠시만 기다려주세요."
        imageNotFoundLabel.textColor = .black
        imageNotFoundLabel.backgroundColor = .lightGray
        imageNotFoundLabel.frame = CGRect(x: 0, y: 100, width: UIScreen.main.bounds.width, height: UIScreen.main.bounds.height/2)
        return imageNotFoundLabel
    }()
    
    func draw() {
        loadImage()
    }
    
    func loadImage() {
        guard let data = try? Data(contentsOf: url),
              let image = UIImage(data: data),
              let imageView = realView as? UIImageView else { return }
        
        DispatchQueue.global().async {
            DispatchQueue.main.async {
                imageView.frame = CGRect(x: 0, y: 100, width: UIScreen.main.bounds.width, height: UIScreen.main.bounds.height/2)
                imageView.image = image
            }
        }
    }
}
    
class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        let imageProxy = ImageProxy(url: URL(string: "https://picsum.photos/5000")!)
        
        view.addSubview(imageProxy.placeholderView)
        view.addSubview(imageProxy.realView)
        
        imageProxy.draw()
    }
}
```
  </p>
</details>


## 4. 생각해보기
- 프록시를 데코레이터와 헷갈리기도 합니다. 데코레이터는 클래스에 새로운 행동을 추가하는 용도로 쓰이죠. 하지만 프록시는 어떤 클래스로의 접근을 제어하는 용도로 쓰입니다. 프록시가 UIImageView로의 접근을 제어하고 있는 것이죠. 
- 어댑터와도 비슷해보일 수 있습니다. 프록시와 어댑터 모두 클라이언트와 다른 객체 사이에서 클라이언트의 요청을 다른 객체에게 전달하는 역할을 합니다. 어댑터는 객체의 인터페이스를 바꿔주지만, 프록시는 똑같은 인터페이스를 사용한다는 차이점이 있죠. 


## 5. 다양한 프록시 
- 프록시는 변형이 아주 많습니다. 기본적으로 원격 프록시, 가상 프록시, 보호 프록시가 있어요. 
- 종류는 많지만 결국에 구조는 유사합니다. 서서히 차근차근 공부해서 익숙해지는게 좋겠습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/67f03f09-5c65-4af4-b025-f4fc71443b3c/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/501358d9-e3ef-481e-96bb-6f8a221894e1/image.png)


## Reference 
[https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223](https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223)

