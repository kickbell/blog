# 제약조건을 줄여주는 UIStackView


오늘은 아주 간단한 짧은 글을 하나 적으려고 합니다. UIStackView에 대해서 인데요. 여느 때처럼 좋은 코드를 찾아 기웃기웃거리다가 스택뷰를 사용하는 많은 이유 중에 하나에 대해서 알게 되어 정리해봅니다. 

아래에는 앱스토어의 게임 탭에서 가져온 이미지가 있습니다. 모양을 보건데 컬렉션 뷰 셀이겠죠. 만약 저런 모양의 UI를 스택뷰를 사용하지 않고 구현한다면 어떻게 할 수 있을까요 ? 

스토리보드 방식이든, 코드로 짜는 방식이든 비슷할 겁니다. 레이블을 만들고 오토레이아웃을 구성해서 제약조건을 설정하겠죠. 레이블이 3개에 이미지뷰가 1개 있으니(이미지 뷰내의 버튼은 생략) 총 4개에 관한 제약조건이 필요할겁니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/ff3c34fc-90f1-4445-af3a-6c2c8309c584/image.png)

하지만, 스택뷰를 사용하면 제약조건을 훨씬 더 줄일 수가 있습니다. 

코드로 예시를 들어 보죠. 위 그림의 UI를 대략적으로 구현한 FeaturedCell 이 있습니다. 레이블 3개와 이미지 뷰 1개를 생성합니다. 각 레이블과 이미지뷰의 속성 관련 코드를 생략할게요. 

그리고 스택뷰를 하나 만들어서 레이블과 이미지뷰를 다 넣어버립니다. 자 그러면 어떻게 될까요? 제약조건이 4개로 확 줄어듭니다. 물론 세부적인 디자인을 잡으려면 스택뷰의 aline, spacing 속성을 변경한다던지 추가적인 작업을 할 수도 있겠죠. 

하지만, 스택뷰를 사용하지 않고 UI를 구현하는 것보다 오토레이아웃의 제약조건이 훨씬 적어집니다. 저는 그래서 UI를 그릴 때, 스택뷰를 자주 사용하는 편이에요. 

```swift
import UIKit

class FeaturedCell: UICollectionViewCell {
    static var reuseIdentifier: String = "FeaturedCell"
    
    let tagline = UILabel()
    let name = UILabel()
    let subtitle = UILabel()
    let imageView = UIImageView()
    
    override init(frame: CGRect) {
        super.init(frame: frame)
        let stackView = UIStackView(arrangedSubviews: [tagline, name, subtitle, imageView])
        stackView.translatesAutoresizingMaskIntoConstraints = false
        stackView.axis = .vertical
        contentView.addSubview(stackView)
        
        NSLayoutConstraint.activate([
            stackView.leadingAnchor.constraint(equalTo: contentView.leadingAnchor),
            stackView.trailingAnchor.constraint(equalTo: contentView.trailingAnchor),
            stackView.topAnchor.constraint(equalTo: contentView.topAnchor),
            stackView.bottomAnchor.constraint(equalTo: contentView.bottomAnchor)
        ])
        
        stackView.setCustomSpacing(10, after: subtitle) 
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
```

조금 더 복잡한 UI를 볼까요? 마찬가지로 앱스토어의 화면을 일부 가져왔습니다. 이미지 뷰가 크게 하나 있던 위의 셀과는 다르게 여기엔 3개의 셀로 나뉘어져 있네요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/df83f961-5cfb-4694-b480-a45088106ac2/image.png)

윗 부분의 IPhone 필수 게임과 모두 보기 같은 부분은 스크롤시에 움직이지 않는 것을 보니 Header 입니다. 그 부분을 제외하고 코드로 예시를 보겠습니다. 

이번엔 스택뷰를 2개 사용했어요. `innerStackView` 와 `outerStackView` 입니다. 사실 스택뷰를 구성할 때, 너무 스택뷰가 많아지게 되면 네이밍이 정말 힘들어지기도 하거든요. 그런 부분에 있어서 스택뷰 구성을 잘 해줘야 합니다. 

여기서는 `innerStackView` 에 센터에 있는 name, subtitle을 넣어줬어요. 그리고 `outerStackView` 을 만들고, imageView, 방금 만든 innerStackView, buyButton을 넣어주었습니다. 

이렇게 하면 오토레이아웃의 제약조건은 몇개가 필요하죠? 네, 똑같이 겨우 4개 입니다. 만약에 이런 모양의 UI를 스택뷰를 사용하지 않고 만들었다고 하면 절대 4개의 제약조건으로는 끝낼 수 없었을 겁니다.  

추가적으로 셀의 이미지와 buyButton을 고정하기 위해서 2개의 setContentHuggingPriority 속성을 지정해주었습니다. 

```swift
import UIKit

class MediumTableCell: UICollectionViewCell {
    static var reuseIdentifier: String = "MediumTableCell"
    
    let name = UILabel()
    let subtitle = UILabel()
    let imageView = UIImageView()
    let buyButton = UIButton(type: .custom)
    
    override init(frame: CGRect) {
        super.init(frame: frame)
        
        imageView.setContentHuggingPriority(.defaultHigh, for: .horizontal)
        buyButton.setContentHuggingPriority(.defaultHigh, for: .horizontal)
        
        let innerStackView = UIStackView(arrangedSubviews: [name, subtitle])
        innerStackView.axis = .vertical
        
        let outerStackView = UIStackView(arrangedSubviews: [imageView, innerStackView, buyButton])
        outerStackView.translatesAutoresizingMaskIntoConstraints = false
        outerStackView.alignment = .center
        outerStackView.spacing = 10
        contentView.addSubview(outerStackView)
        
        NSLayoutConstraint.activate([
            outerStackView.leadingAnchor.constraint(equalTo: contentView.leadingAnchor),
            outerStackView.trailingAnchor.constraint(equalTo: contentView.trailingAnchor),
            outerStackView.topAnchor.constraint(equalTo: contentView.topAnchor),
            outerStackView.bottomAnchor.constraint(equalTo: contentView.bottomAnchor)
        ])
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
```

정리하면, 스택뷰를 적절하게 잘 사용하면 오토레이아웃의 제약조건을 최소화 할 수 있습니다. 추가적으로 애플 공식 오토레이아웃 가이드의 스택뷰 링크를 걸어두도록 하겠습니다. 
    
## Reference

[https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/LayoutUsingStackViews.html#//apple_ref/doc/uid/TP40010853-CH11-SW1](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/LayoutUsingStackViews.html#//apple_ref/doc/uid/TP40010853-CH11-SW1)				  




