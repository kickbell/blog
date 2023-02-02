# UIFontMetrics 와 UIFont.preferredFont

앱을 개발할 때, 접근성(Accessibility) 기능은 매우 중요하죠. 접근성 기능은 다양한 사람들이 장치와 상호 작용하는 데 도움이 됩니다. 접근성의 범위는 꽤나 방대하지만, 눈이 안좋거나 40~50대의 고령자를 위한 Dynamic Type 또한 접근성을 지원하는 것 중에 하나입니다. 

Dynamic Type을 지원하는 앱들은 [ 설정 ]-[ 일반 ]-[ 손쉬운 사용 ]-[ 더 큰 텍스트 ] 에서 글자 크기를 변경하게 되면, 앱에서도 각 레이블이나 버튼의 Text 크기가 변하게 되죠. 

오늘은 간단하게 Dynamic Type을 설정할 때 가장 많이 쓰는 API인 `UIFont.preferredFont(forTextStyle:)`와 `UIFontMetrics.default.scaledFont(for:)`에 대해서 비교해보겠습니다. 

일단 둘 다 사용은 아래와 같은 식으로 하게 됩니다. 레이블을 생성하고 API를 사용하죠. 

```swift
import UIKit

class FeaturedCell: UICollectionViewCell {
    static var reuseIdentifier: String = "FeaturedCell"
    
    let tagline = UILabel()
    let name = UILabel()
    
    override init(frame: CGRect) {
        super.init(frame: frame)
        tagline.font = UIFontMetrics.default.scaledFont(for: UIFont.systemFont(ofSize: 12, weight: .bold))
        tagline.textColor = .systemBlue 
        
        name.font = UIFont.preferredFont(forTextStyle: .title2)
        name.textColor = .label
    }
    
    required init?(coder: NSCoder) {
		fatalError("init(coder:) has not been implemented")
    }
}
```
    
하지만 차이점이 있어요. `UIFont.preferredFont(forTextStyle:)` 같은 경우는 미리 지정된 크기 또는 양식이 있습니다. 애플의 human-interface-guidelines 에 나와있는 그림인데요. 

Style이 title1, title2, title3, body 와 같은 식으로 지정되어 있고 그에 맞는 Size 표가 있습니다. 만약에 실무에서 디자인이 넘어왔을 때, 해당 폰트 크기와 Size가 같다면 그에 맞는 스타일을 지정해서 사용할 수 있을거에요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/0889de93-6551-4684-a68a-bc21f2286d40/image.png)

반면에, `UIFontMetrics.default.scaledFont(for:)` 같은 경우는 좀 더 디테일한 요구사항이 있을 때 사용해야 합니다. 예를 들어, 이런 상황인거죠. 

위의 그림에서 Body 스타일은 Regular의 Weight를 가지고 있고 Size는 17입니다. 그런데, 디자이너의 요구사항은 Size 17에 Weight를 Bold로 하고 싶은 것이죠. 이런 상황이라면 `UIFontMetrics.default.scaledFont(for:)`를 사용할 수 있습니다. 

그래서 위에 코드에서도 아래처럼 사용한 것이죠. 매개변수로는 UIFont 타입이 필요한데, weight가 Bold인 12 Size의 폰트를 생성해서 넣어준 것이죠. 

```swift
tagline.font = UIFontMetrics.default.scaledFont(for: UIFont.systemFont(ofSize: 12, weight: .bold))
```

실제 예시 그림을 보면, 아래의 그림은 앱스토어의 게임 탭인데요. 글씨가 좀 작아서 티가 나는지 모르겠습니다만 `지금 참여 가능` 이라는 파란색 글자의 Weight가 Regular가 아니라 Bold 입니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/b8480f9c-ffd6-4c45-8a0d-ce1cc1e8f4c8/image.png)

즉, 이렇게 디자인의 요구사항에 맞게 각 API들을 사용하면 되는 겁니다. 아래에 HIG 링크를 첨부해놓도록 하겠습니다. 

## Reference

[https://developer.apple.com/design/human-interface-guidelines/foundations/typography/](https://developer.apple.com/design/human-interface-guidelines/foundations/typography/)




