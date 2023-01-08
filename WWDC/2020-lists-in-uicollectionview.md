# Lists in UICollectionView

> _이 글은 [WWDC 2020 - Lists in UICollectionView](https://developer.apple.com/videos/play/wwdc2020/10026)의 내용을 공부하고, 개인적으로 정리해놓은 글입니다._


- 아래의 다이어그램은 최신 컬렉션 뷰에 관한 것입니다. 
- iOS 13에서 Diffable Data Source, Compositional Layout이 추가됐고, iOS 14 이후로 Section Snapshots, List Configuration, List Cell과 View Configuration 이 추가되었습니다. 
- 이 글에서는 List Configuration 과 List Cell만을 다루고 있습니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/fcb48c49-b733-4553-8e9d-97dcba860d8e/image.png)

## 1. 리스트(Lists)
- iOS 14에서 추가된 리스트는 Collection View에서 UITableView와 유사한 모양을 제공합니다. iOS 13에서 도입한 Compositional Layout 위에 구축했습니다.
- 특히, 셀프 사이징 셀에 대해서 크게 개선되어 셀의 높이를 수동으로 계산하는 것에 대해 더 이상 걱정할 필요가 없습니다.
- 오토 레이아웃을 사용하여 셀을 만들면 Collection View가 나머지를 처리합니다.
- 수동적으로 크기 조정이 필요한 경우에도 셀의 하위 클래스에서 preferredLayoutAttributesFittingAttributes를 재정의하여 이 작업을 수행할 수 있습니다.
- 아래의 컬렉션뷰를 보면 알 수 있지만, 가로 스크롤링하는 섹션도 있고 스마일, 자연, 요리처럼 그룹별로 모든 이모티콘을 정렬하고 내부 데이터가 있는 섹션도 있으며 마지막에는 테이블뷰와 유사한 레이아웃의 섹션도 있습니다. 
- 이런 테이블뷰 느낌의 섹션은 테이블뷰 셀처럼 스와이프, 좋아요 같은 기능도 똑같이 작업할 수 있습니다. 하지만 여러 개의 컬렉션뷰 + 테이블뷰의 조합으로 만들어진 것이 아닌 하나의 컬렉션 뷰이죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/777237df-ae90-48ba-9501-466e403b3c68/image.png)

## 2. 리스트의 구성요소(Components of a list)
- iOS 14에서는 UICollectionLayoutListConfiguration이라는 새로운 타입을 제공합니다. 컬렉션뷰에서 리스트를 작성하기 위해 레이아웃 측에 필요한 타입입니다.
- iOS 13에는 Compositional Layout을 도입했고, 그 시스템의 두 부분이 UICollectionLayoutListConfiguration 과 NSCollectionLayoutSection 입니다. 
- UICollectionLayoutListConfiguration 은 그 위에 존재합니다. 
- Compositional Layout 에 관한 자세한 설명은 아래의 세션을 참고하세요. 
- [Advances in Collection View Layout](https://developer.apple.com/videos/play/wwdc2019/215)

![](https://velog.velcdn.com/images/dev_kickbell/post/258e5370-8cad-4a3f-818b-5b7ca9a0e4fb/image.png)


## 3. 리스트 구성(List Configuration) 
- 리스트 구성은 테이블뷰와 같은 스타일(.plain, .grouped, .insetGrouped)을 제공합니다. 그리고 iPadOS에서만 아래의 사진과 같이 "sidebar" 및 "sidebar plain"라는 새로운 스타일도 제공합니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/20694262-1573-480a-af5c-c5fd559867e9/image.png)

- 아래의 코드처럼 구현하면 테이블뷰와 똑같은 모양이 나옵니다. 하지만, 섹션별로 커스텀을 해줄 수 있습니다. 클로저 내부에 간단하게 만들기 코드를 넣고 회색 사각형으로 표시된 부분처럼 0번째 섹션이면 다른 섹션을 리턴해준다던지 하는 식입니다. 

```swift
//간단하게 만들기 
let configuration = UICollectionLayoutListConfiguration(appearance: .insetGrouped)
let layout = UICollectionViewCompositionalLayout.list(using: configuration)
```

![](https://velog.velcdn.com/images/dev_kickbell/post/64879ae6-e60a-49d6-82ec-fb41259e1a8a/image.png)

- 맨 위에서 3가지의 섹션이 있는 애플리케이션의 리스트 구성은 아래의 코드와 같습니다.

<details>
  <summary><a href=""></a>createLayout()</summary>
  <p>

```swift
func createLayout() -> UICollectionViewLayout {
    
    let sectionProvider = { [weak self] (sectionIndex: Int, layoutEnvironment: NSCollectionLayoutEnvironment) -> NSCollectionLayoutSection? in
        
        guard let sectionKind = Section(rawValue: sectionIndex) else { return nil }
        
        let section: NSCollectionLayoutSection
        
        // orthogonal scrolling section of images
        if sectionKind == .recents {
            
            let itemSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(1.0), heightDimension: .fractionalHeight(1.0))
            let item = NSCollectionLayoutItem(layoutSize: itemSize)
            item.contentInsets = NSDirectionalEdgeInsets(top: 5, leading: 5, bottom: 5, trailing: 5)
            let groupSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(0.28), heightDimension: .fractionalWidth(0.2))
            let group = NSCollectionLayoutGroup.horizontal(layoutSize: groupSize, subitems: [item])
            section = NSCollectionLayoutSection(group: group)
            section.interGroupSpacing = 10
            section.orthogonalScrollingBehavior = .continuousGroupLeadingBoundary
            section.contentInsets = NSDirectionalEdgeInsets(top: 10, leading: 10, bottom: 10, trailing: 10)
            
        // outline
        } else if sectionKind == .outline {
            section = NSCollectionLayoutSection.list(using: .init(appearance: .sidebar), layoutEnvironment: layoutEnvironment)
            section.contentInsets = NSDirectionalEdgeInsets(top: 10, leading: 10, bottom: 0, trailing: 10)

        // list
        } else if sectionKind == .list {
            var configuration = UICollectionLayoutListConfiguration(appearance: .insetGrouped)
            configuration.leadingSwipeActionsConfigurationProvider = { [weak self] (indexPath) in
                guard let self = self else { return nil }
                guard let item = self.dataSource.itemIdentifier(for: indexPath) else { return nil }
                return self.leadingSwipeActionConfigurationForListCellItem(item)
            }
            section = NSCollectionLayoutSection.list(using: configuration, layoutEnvironment: layoutEnvironment)
        } else {
            fatalError("Unknown section!")
        }
        
        return section
    }
    return UICollectionViewCompositionalLayout(sectionProvider: sectionProvider)
}
```
  </p>
</details>

## 4. 헤더와 푸터(Headers and Footers)
- 컬렉션 뷰의 목록에 있는 헤더와 푸터는 UITableView와 다른 점이 있습니다. UICollectionView 리스트의 헤더와 푸터는 명시적으로 enabled 되어야 합니다. 구현하는 방법은 2가지가 있습니다.

### 첫 번째 방법 
- 헤더와 푸터를 supplementary view로 등록합니다. 헤더에만 등록해도 푸터에도 똑같이 적용됩니다. 
- 이렇게 하고 나서, 헤더나 푸터를 화면에 보여주기 위해 렌더링 할 때 컬렉션뷰에서 supplementary view를 제공할지 묻는 메시지가 표시됩니다.

```swift
var configuration = UICollectionLayoutListConfiguration(appearance: .insetGrouped)
configuration.headerMode = .supplementary

let layout = UICollectionViewCompositionalLayout.list(using: configuration)
```

- 그러면 이제 supplementary view를 만들어주면 되는데, 가장 쉬운 방법이 dataSource.supplementaryViewProvider 입니다. 
- UICollectionView delegate에서 같은 메서드를 구현할 수도 있습니다.
- elementKindSectionHeader 또는 elementKindSectionFooter 중에 하나를 선택하고 리턴하면 됩니다.

```swift
var configuration = UICollectionLayoutListConfiguration(appearance: .insetGrouped)
configuration.headerMode = .supplementary
let layout = UICollectionViewCompositionalLayout.list(using: configuration)

dataSource.supplementaryViewProvider = { (collectionView, elementKind, indexPath) in
    if elementKind == UICollectionView.elementKindSectionHeader {
        return collectionView.dequeueConfiguredReusableSupplementary(using: header, for: indexPath)
    }
    else {
        return nil
    }
}
```

- 하지만 주의해야 될 점이 있습니다. supplementary view 는 제공되어야 하죠? 그런데 위에처럼 return nil을 하는 경우라면 오작동이 발생할 수 있습니다. 
- 따라서 레이아웃의 일부 섹션에는 헤더가 필요하고 다른 섹션에는 필요하지 않은 경우 이전에 보여드린 섹션별 구성을 사용하고 이 특정 섹션이 표시되어야 하는지 여부에 따라 모드를 "supplementary" 또는 "none"으로 설정해야 합니다. 
- 더 자세한 코드는 3번 마지막에 있는 createLayout() 코드를 참고하면 좋습니다. 

```swift
let configuration = UICollectionLayoutListConfiguration(appearance: .insetGrouped)
configuration.headerMode = sectionHasHeader ? .supplementary : .none
let section = NSCollectionLayoutSection.list(using: configuration, layoutEnvironment: layoutEnvironment)
```

### 두 번째 방법 
- 두 번째 옵션은 헤더에만 사용할 수 있으며 헤더 모드를 firstItemInSection으로 설정하면 enabled 됩니다.
- 이름 그대로 첫 번째 셀을 헤더처럼 보이도록 바꿔주는 겁니다. 그러나 주의할 점은 데이터 소스의 첫 번째 셀은 더 이상 섹션의 실제 콘텐츠를 나타내지 않고 헤더(보통은 제목)를 나타내기 때문에 그것을 주의해야 합니다. 
- 더 자세한 내용은 아래의 세션을 참고하면 됩니다. 
- [Advances in Diffable Data Source](https://developer.apple.com/videos/play/wwdc2020/10045)

```swift
var configuration = UICollectionLayoutListConfiguration(appearance: .insetGrouped)
configuration.headerMode = .firstItemInSection
let layout = UICollectionViewCompositionalLayout.list(using: configuration)
```

## 5. 리스트 셀(List Cell)
- 컬렉션뷰의 컴포지션 특성을 유지하면서 일반 컬렉션뷰 셀이 예상되는 모든 위치에서 리스트 셀을 사용할 수 있고, List Section과 같이 UICollectionView 셀을 사용할 수도 있습니다.
- 리스트 셀은 내용의 들여쓰기 뿐만 아니라 구분 기호의 inset을 위해 세분화된 기능을 제공합니다. 
- UITableView에서만 허용됐던 스와이프 동작도 사용이 가능합니다. 또 셀의 엑세서리 관련 API가 대폭 향상되었습니다. 이에 관한 내용은 아래 세션을 참고하면 됩니다. 
- [Modern cell configuration](https://developer.apple.com/videos/play/wwdc2020/10027)


## 6. 구분선(Separators)
- 구분선에 관해서 이야기해보겠습니다. 아래의 그림은 꽤 일반적인 레이아웃입니다. 그러나 이 레이아웃은 오류가 있습니다. 
- 구분선은 셀의 콘텐츠와 정렬되어야 합니다. 지금은 구름 아래까지 구분선이 그어져있죠.
		
![](https://velog.velcdn.com/images/dev_kickbell/post/368917ca-8fe1-457e-949a-9c8b20f76e12/image.png)

- 구분선은 아래의 그림과 같이 삽입되어야 합니다. 
- 테이블뷰에서는 separaotr inset 이라는 Point 기반 값을 제공하여 이 작업을 수행합니다. 여기까진 쉽습니다. 괜찮죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/4bbb769e-2b55-482c-b955-b7beeb5126b0/image.png)

- 하지만, 그러나 Safe Area Insets, Layout Margins, dynamic font size, SF symbol 및 SF 기호가 있는 오토 레이아웃에서는 더이상 쉽지 않습니다.
- 오늘날 우리는 이러한 모든 가치가 언제든지 변경될 수 있는 매우 역동적인 환경을 가지고 있습니다. 사용자가 선호하는 글꼴 크기에 따라 이미지의 크기도 변경될 수 있으며 그런 다음 레이블의 위치가 변경될 수 있습니다. 따라서 레이블이 끝나는 위치를 미리 알기는 매우 어렵습니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/006b063a-47b3-4bd1-b6bc-865424ab4875/image.png)

- 그래서 리스트 셀에 separator layout guide 라는 새로운 개념을 도입했습니다. 
- 이 레이아웃 가이드는 UIKit의 기존 레이아웃 가이드와 약간 다르게 작동합니다. 콘텐츠를 이 레이아웃 가이드로 제한하는 대신 이 레이아웃 가이드를 콘텐츠로 제한하므로 레이아웃 가이드로 작업할 때 사용했던 것과 반대입니다.
- separator layout guide를 설정하는 가장 쉬운 방법은 먼저 셀의 레이아웃을 구성하고 셀이 의도한 대로 표시되면 단일 제약 조건을 추가하기만 하면 됩니다.
- 아래의 그림처럼 separator layout guide 의 leading 을 label의 leading에 제약조건을 추가하는 것이지요. 리스트 셀은 리스트 섹션과 함께 구분선을 셀의 기본 콘텐츠에 맞춰 자동으로 유지합니다.
- 시스템에서 제공하는 Content Configuration을 사용하는 경우 이 작업이 자동으로 수행되므로 이에 대해 걱정할 필요가 없습니다. 그러나 사용자 지정 셀 레이아웃이 있는 경우 구분선이 올바르게 배치되었는지 쉽게 확인할 수 있습니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/8a929b57-f341-4ebf-9800-f3ccfb731b4e/image.png)

## 7. 스와이프 액션(Swipe Actions)

### 셀마다 선택적으로 스와이프 액션 삽입 
- 스와이프 동작은 이제 리스트 셀의 기능입니다. 그래서 셀 콘텐츠와 같이 구성합니다.
- 스와이프 동작은 리스트 구성을 사용하는 경우에만 지원됩니다.
- 신기한 점은 셀의 기능이기 때문에 셀마다 스와이프를 주거나 안주거나 할 수도 있다는 겁니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/20c79c8c-0b68-448a-a68e-03ff88293eb9/image.png)

### 주의사항
- 절대로 indexPath를 캡쳐하지 않도록 합니다. indexPath는 그 위에 콘텐츠를 삽입하거나 삭제할 때마다 변경되며, 이 특정 셀을 반드시 다시 로드하지도 않습니다.
- 따라서 indexPath를 사용하면 실제로 다른 셀에서 스와이프 액션이 발생할 수도 있습니다. 이는 잘못된 데이터를 삭제할 수도 있으므로 삭제 작업에는 특히 더 위험합니다. 
- 대신 위의 코드처럼 item의 데이터 모델을 직접 캡쳐하거나 이 셀의 콘텐츠를 식별하는 데 사용할 수 있는 안정적인 식별자를 캡처해야 합니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/6f36c17f-f5fc-4159-ab60-2412990ea94c/image.png)


## 8. 엑세서리(Accessories)

### 엑세서리 
- UITableView에서 액세서리 API는 상당히 제한적이었죠. 상호 배타적이며 셀의 trailing에만 관련된 액세서리 타입 및 액세서리뷰에 접근할 수 있었습니다.
- List cell은 많은 새로운 액세서리 타입을 제공하며 셀의 trailing와 leading 모두에 대해 액세서리를 구성 할 수 있으며 같은 쪽에 여러 개의 액세서리를 구성 할 수도 있습니다.
- 또한, UITableViewCell의 액세서리가 장식 뷰에 가까웠다면 List cell에서는 기능을 활성화할 수 있습니다. 이게 무슨 말이냐면, 음 그런 느낌입니다. 예를 들어, 아래의 그림처럼 셀 우측에 리오더, 삭제, 아웃라인이라는 3개의 엑세서리가 있습니다.
- 그럴 때, 리오더를 탭하면 나머지 삭제와 아웃라인은 비활성화가되서 보이지 않는 것이죠. 컬렉션뷰는 자동으로 재정렬 모드로 설정이 되는 거구요.  
- 삭제같은 경우는 그런 느낌입니다. 이 엑세서리는 탭하면 삭제 스와이프 액션이 오른쪽에서 스르르륵 하고 나타나는 거죠. 
- 마지막 아웃라인 엑세서리 같은 경우는 지금 우측을 가리키고 있는 엑세서리를 탭하면 방향이 아래쪽으로 내려가면서 셀의 하위 항목을 확장/축소 하겠죠. 애니메이션도 나오구요. 자세한 사용방법은 위에서도 언급했던 [Advances in Diffable Data Source](https://developer.apple.com/videos/play/wwdc2020/10045) 세션을 참고하시기 바랍니다.  
		
![](https://velog.velcdn.com/images/dev_kickbell/post/2e9050aa-65fe-4ace-b6a2-3fc42c00be06/image.png)


### 커스터마이징

- 여러 개의 엑세서리를 추가하려면 아래의 코드처럼 배열에 추가해주면 됩니다. 
- 재밌는 점은 순서를 아래와 같이 했더라도 아웃라인은 제일 우측에, 삭제는 제일 왼쪽에 있어야 한다는 것을 압니다. 따라서 UIKit은 자동으로 액세서리를 올바른 순서로 정렬하고 적절한 쪽에 표시합니다.
- 또한 시스템은 아웃라인이 항상 표시되어야 하지만 삭제버튼 같은 경우에는 셀이 편집 모드에 있을 때 보이지 않아야 겠죠? 그래서 UIKit은 편집 모드를 시작하거나 종료할 때 삭제 액세서리를 자동으로 안쪽과 밖으로 애니메이션해서 보이지 않게 합니다.
- 하지만, 커스터마이징 할 수 없는 것은 아닙니다. 거의 모든 항목을 사용자 정의할 수 있습니다. 예를 들어 편집 모드가 아닐 때만 아웃라인을 표시하려면 표시된 매개변수를 whenNotEditing으로 설정하기만 하면 됩니다.


```swift
cell.accessories = [ 
    .checkmark(), 
    .disclosureIndicator(options: .init(tintColor: .systemGray)), 
    .delete(),
    .reorder() 
]

cell.accessories = [ 
    .disclosureIndicator(displayed: .whenNotEditing), 
    .delete()
]
```

## Reference

[https://developer.apple.com/videos/play/wwdc2020/10026](https://developer.apple.com/videos/play/wwdc2020/10026)			
[https://developer.apple.com/documentation/uikit/views_and_controls/collection_views/implementing_modern_collection_views](https://developer.apple.com/documentation/uikit/views_and_controls/collection_views/implementing_modern_collection_views)


