얼마전에 평소에 앱을 사용하다가 구독형 유료버전으로 전환하니 앱의 아이콘을 유료버전 전용으로 바꿔줬던 일이 있었습니다. 

"어 이게 바뀌네?"라는 생각을 했었는데, 이번엔 출석을 n일 연속을 하니 또 다른 걸로 바꿔주더군요😆. 별 것 아니었지만, 꽤 재밌는 요소의 사용자 경험이었어요. 그래서 오늘은 동적으로 앱의 아이콘을 바꾸는 법에 대해서 적어볼까 합니다. ( 혹시 무료 아이콘과 iOS의 앱 아이콘을 생성하고 싶으시다면, 아래에 첨부링크를 걸어두었습니다. )

이 기능은 `iOS 10.3+`부터 지원되고 있구요. `iPadOS 10.3+`, `Mac Catalyst 13.1+`, `tvOS 10.2+` 에도 지원되지만, WatchOS에는 지원되지 않습니다. 

[공식 문서](https://developer.apple.com/documentation/uikit/uiapplication/2806818-setalternateiconname)에는 3개 정도의 글이 있는데요. `Managing the app's icon` 전체에는 총 4개의 API가 있지만, `applicationIconBadgeNumber` 같은 경우는 오늘의 주제와는 관련이 없으니 제외하고 알아보도록 하겠습니다. 

> ### supportsAlternateIcons
> A Boolean value that indicates whether the app is allowed to change its icon.

첫 번째는 `supportsAlternateIcons` 입니다. 문서 그대로 앱에서 아이콘을 변경할 수 있는지 여부를 나타내는 Bool 값이에요. 앱 아이콘을 변경하는 비즈니스 로직에서 guard 문으로 체크해주는 용도로 사용하면 될 것 같습니다. 

여기서 하나 봐둬야 될 부분이 바로 `AlternateIcon` 인데요. 정확하게는 `Alternate App Icon` 이고, 한국어로 하면 `대체 아이콘`쯤 되겠죠? Assets 에 대체 아이콘들을 넣어주고, 이걸 알려주려면 `Info.plist`에 값을 지정해주어야 합니다. `CFBundleIcons`이라는 키 값으로 넣어주어야 해요. 이것에 관한 더 자세한 내용은 [문서](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html)에서 보시면 될 것 같습니다. 

그리고 `Alternate App Icon` 의 반대말은 `Primary App Icon` 인데요. 대체할 아이콘들이 있으면 당연히 기본값? 아이콘도 있어야겠죠. 그것을 뜻하는 말입니다. 이 정도만 일단 알아두셔도 괜찮아요. 

> ### alternateIconName
> The name of the icon the system displays for the app.

두 번째는 API는 `alternateIconName`입니다. Name 이라는 이름답게 타입은 String 이에요. 이 타입으로 앱 아이콘을 식별해서 다음에 나올 API를 통해 앱의 아이콘을 바꿔주지요. 저는 여기서 "아 그런가보다~" 하고 넘어갔는데, 그렇지 않더라구요. (이것 때문에 삽질을 좀 했습니다...) 

바로 이 부분인데요. 

> When the system is displaying your app’s primary icon, the value of this property is nil.

방금 제가 위에서 기본 아이콘을 `Primary App Icon` 이라고 말했죠? 즉, 제가 Asset에서 `Primary App Icon` 의 이름을 `홍길동`이라고 하더라도 기본 아이콘의 속성 값이 `nil`이기 때문에 동작을 안해요. 자세한 설명은 이따가 코드로 보여드리도록 하겠습니다. 

> ### setAlternateIconName(_:completionHandler:)
> Changes the icon the system displays for the app.
```swift 
func setAlternateIconName(
    _ alternateIconName: String?,
    completionHandler: ((Error?) -> Void)? = nil
)
```

마지막은 `setAlternateIconName(alternateIconName:completionHandler:)` 인데요. 이건 코드가 더 이해하기 쉬워서 같이 첨부했습니다. UIApplication에서 제공하는 싱글톤 메서드이구요. 위에서 말씀드렸던 `alternateIconName`를 매개변수로 받고, `completionHandler`가 있어서 실패하면 Error를 넘겨주기도 합니다. 

자, 이제 코드를 볼 차례인데요. 코드를 보기 전에 이걸 말씀드려야 할 것 같아요. 아래에 첨부된 참고된 글과 위에서 말씀드렸던 공식 문서에서도 `Info.plist`를 통해 키 값을 넣어주라고 하고 있잖아요. 그런데, 이런 글들의 공통점이 있더라구요. 

1. 대체 아이콘을 Assets 내부가 아니라 프로젝트 내부에 위치시켜야 한다. 
2. @2x 및 @3x 명명 규칙을 사용해서 iOS가 사용자 기기에 맞는 아이콘을 자동으로 선택하도록 해야 한다.

그런데, 저희는 Assets 내부에서 AppIcon 이라는 객체를 사용해왔잖아요? 그리고 그 객체는 iPhone App뿐만 아니라 iPhone Notification, iPhone Settings, iPhone Spotlight 까지 다 지원하죠. 

또 생각해보면 제가 사용했던 앱에서는 앱 아이콘이 바뀐 이후로 푸시 알림이 올때도 바뀐 아이콘으로 알림이 왔었거든요. 그래서 음, 분명히  @2x 및 @3x 명명 규칙을 사용해서 단지 iPhone의 App Icon만을 바꾸는 것만이 아닌 Assets의 AppIcon을 활용하는 방법이 있을 것이라고 생각했어요. 

결론적으로, 아래의 첫 번째 방법처럼 하지 않고 두 번째 방법을 찾아냈습니다. 둘 다 동작은 하는 것 같습니다만, 후자의 방법이 공식문서에서 제공되는 [오피셜 코드](https://developer.apple.com/documentation/xcode/asset_management/configuring_your_app_to_use_alternate_app_icons)이기 때문에 전 후자로 사용했어요. 

## 1. 첫 번째 방법 

![](https://velog.velcdn.com/images/dev_kickbell/post/35803723-e6e5-422d-8adb-a28e0810168f/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/7b9e548f-ac61-4e7f-bf60-45b60b51ee30/image.png)

## 2. 두 번째 방법(오피셜)

첫 번째 방법의 설명은 생략하고 바로 두 번째 방법으로 넘어갈게요. 첫 번째 방법에 대한 설명은 아래에 첨부된 링크들에 더 자세히 나와있어서 궁금하시다면 확인해 보시면 될 것 같아요. 

오피셜 방법의 구현은 3단계로 나뉘어 집니다. 

### 1. App Icon Assets에 넣기 

먼저 Assets에 [이 링크](https://appicon.co/)로 생성한 App Icon을 넣어주시거나, 가지고 계신 App Icon들을 넣어 줍니다. 첫 번째 방법과는 다르게 앱의 Assets에 여러 App Icon들이 들어가있는 것을 볼 수 있죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/0a157a98-ec32-496e-b9c3-0af25e9afae6/image.png)

### 2. Primary App Icon, Alternate App Icon 등록하기    

`Inpo.plist`에서는 아무 작업도 하지 않습니다. 그리고 `TARGETS`/`Bulid Setting`/`Asset Catelog Complier - Opthins`로 이동해서 Primary, Alternate App Icon Sets을 입력해 줍니다. 여기까지 했으면 해야할 설정은 끝났습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/07745bc3-80cd-48cc-be97-1989bd87e92d/image.png)

### 3. 코드 구현하기

그러면 이제 코드를 구현해야 하는데요. 너무 간단해서 전체를 그냥 가져왔어요. 버튼은 뷰컨에 하나만 넣어줬고 전체 코드는 아래와 같습니다. 

```swift
import UIKit

enum Icon: String, CaseIterable {
    case primary    = "AppIcon"
    case blue       = "AppIcon-Blue"
    case green      = "AppIcon-Green"
    case orange     = "AppIcon-Orange"
    case purple     = "AppIcon-Purple"
    case pink       = "AppIcon-Pink"
    case teal       = "AppIcon-Teal"
    case yellow     = "AppIcon-Yellow"
}

class ViewController: UIViewController {
    
    @IBAction func tap(_ sender: UIButton) {
        guard let icon = Icon.allCases.randomElement() else { return }
        changeIcon(to: icon)
    }
    
    func changeIcon(to icon: Icon) {
        guard UIApplication.shared.supportsAlternateIcons else {
            return
        }
        
        let iconName: String? = (icon != .primary) ? icon.rawValue : nil
        
        UIApplication.shared.setAlternateIconName(iconName, completionHandler: { (error) in
            if let error = error {
                print("App icon failed to change due to \(error.localizedDescription)")
            } else {
                print("App icon changed successfully")
            }
        })
    }
    
}

```

enum 으로 Icon을 지정했고, 랜덤 값을 넣어주려고 `CaseIterable` 프로토콜을 채택했어요. 그리고 실질적으로 아이콘을 바꿔주는 메소드인 `changeIcon()` 에서는 아까 말씀드렸던 것처럼 `supportsAlternateIcons` 을 이용해서 대체 아이콘이 있는지 여부를 판별해주고 있습니다. 

그리고 싱글톤 메서드를 실행시켜주기위해 iconName 인스턴스를 생성하고 있는데요. 여기서 아까 위에서 말했던 중요한 부분이 나옵니다 ⭐️. 우리는 지금 이 앱에서 `Primary App Icon`을 `App Icon` 으로 지정해주고 있어요. 그리고 나머지 `Blue, Green, Orange, Pink, Teal, Yellow` 가 `Alternate App Icon` 입니다. 

이게 무슨 뜻이냐면, 저기 삼항연산자 로직에 적혀있죠. icon이 primary가 아니면 icon.rawValue 이고, 그렇지 않다면 nil 이라구요. 즉, 저 작업이 없었다면 만약에 랜덤 icon의 값이 기본 아이콘인 primary가 지정되서 실제 아이콘을 바꿔주는 메소드인 `UIApplication.shared.setAlternateIconName(iconName, completionHandler:`의 파라미터에 iconName으로 `App Icon`이라는 String 값이 들어가더라도 앱의 아이콘은 바뀌지 않는다는 말입니다. 왜? 기본 아이콘의 값은 `nil`이기 때문이죠 😀. (이거 때문에 30분은 날린 듯...) 

마지막으로 앱이 실제로 동작하는 영상보고 마무리 하도록 하겠습니다. 꽤 재밌는 기능이고, 상황에 따라서 요긴하게 사용될 수 있을 것 같으니 기억해 두시면 좋을 듯 싶어요 😄. 

![](https://velog.velcdn.com/images/dev_kickbell/post/9ed09c5f-a3c8-4135-a26f-ebd369658e6d/image.gif)

## Conclusion
- 앱의 아이콘을 동적으로 바꿀 수 있습니다. 주로 `구독형 유료회원 결제`, `30일/100일/300일 연속출석회원`, `특정 아이템 구매회원` 과 같은 마케팅에 활용할 수 있다.  
- 앱에서는 기본 아이콘 값인 `Primary App Icon` 값과 대체할 수 있는 아이콘들의 값인 `Alternate App Icon` 이 있다.
- 2x, 3x을 규칙을 따르는 첫 번째 방법도 있지만, 공식 문서에 나오는 두 번째 방법을 사용하도록 하자. 
- `UIApplication.shared.setAlternateIconName`에 사용되는`Primary App Icon`을 가져올 수 있는 기본 값은 `nil`이다. 
- 자세한 설명과 샘플코드가 제공되는 공식문서의 링크는 [여기](https://developer.apple.com/documentation/xcode/asset_management/configuring_your_app_to_use_alternate_app_icons)이다.

    
## Reference 
[https://www.flaticon.com/](https://www.flaticon.com/)          
[https://appicon.co/](https://appicon.co/)          
[https://www.youtube.com/watch?v=Wp2XJwXCnXI](https://www.youtube.com/watch?v=Wp2XJwXCnXI)          
[https://medium.com/ios-os-x-development/dynamically-change-the-app-icon-7d4bece820d2](https://medium.com/ios-os-x-development/dynamically-change-the-app-icon-7d4bece820d2)                
[https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html)            
[https://developer.apple.com/documentation/uikit/uiapplication/2806818-setalternateiconname](https://developer.apple.com/documentation/uikit/uiapplication/2806818-setalternateiconname)            
[https://developer.apple.com/documentation/xcode/asset_management/configuring_your_app_to_use_alternate_app_icons](https://developer.apple.com/documentation/xcode/asset_management/configuring_your_app_to_use_alternate_app_icons)            
[https://www.hackingwithswift.com/example-code/uikit/how-to-change-your-app-icon-dynamically-with-setalternateiconname](https://www.hackingwithswift.com/example-code/uikit/how-to-change-your-app-icon-dynamically-with-setalternateiconname)              



