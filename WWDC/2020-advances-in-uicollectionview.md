# Advances in UICollectionView


> _이 글은 [WWDC 2020 - Advances in UICollectionView](https://developer.apple.com/videos/play/wwdc2020/10097)의 내용을 공부하고, 개인적으로 정리해놓은 글입니다._


## 1. UICollectionView APIs
- 컬렉션뷰의 API는 Data, Layout, Presentation의 세 가지 범주로 나눌 수 있습니다. 여기서 중요한 점은 데이터를 관리하는 부분과 그것을 화면에 그리는 부분이 나눠진다는 것인데, 이것이 컬렉션뷰를 유연하게 만드는 핵심이라고 할 수 있어요. 
- iOS 6
    - iOS 6에서 처음 출시되었을 때, Data는 indexPath 기반 프로토콜인 UICollectionViewDataSource를 통해 관리되었습니다.		
    - Layout의 경우 추상 클래스인 UICollectionViewLayout이 있었고 구체적인 하위 클래스인 UICollectionViewFlowLayout이 있었습니다.
    - Presentation 측면에서는 UICollectionViewCell, UICollectionReusableView라는 두 가지 보기 유형을 게시했습니다.
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/96ec5ced-0fdf-4b03-9fe0-f481c2c316ab/image.png)
- iOS 13
    - iOS 13에서 Diffable Data Source 및 Compositional Layout을 사용하여 각각 Data 및 Layout에 대한 두 가지 새로운 구성 요소를 도입했습니다.
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/a7654203-1b78-46d3-a9c5-b19d973dc579/image.png)
- iOS 14
    - iOS 14의 경우 iOS 13을 베이스로 해서 Section Snapshots, List Configuration, List Cell/View Configuration의 기능이 더해졌습니다.  
    - ![](https://velog.velcdn.com/images/dev_kickbell/post/df41e791-bf79-4363-aedd-a141760f72d5/image.png)


## 2. 섹션 ¹스냅샷(Section Snapshots)

### 섹션 스냅샷 
- iOS 13
   - iOS 13에 도입된 Diffable Data Source는 새로운 스냅샷 데이터 유형을 추가하여 UI 상태 관리를 크게 단순화하지요. 
   - 자세한 사용 방법이나 심화 영상은 아래의 다른 세션을 참고하시면 될 것 같아요. 
   - [Advances in UI Data Sources](https://developer.apple.com/videos/play/wwdc2019/220/)
   - [Advances in Diffable Data Source](https://developer.apple.com/videos/play/wwdc2020/10045)
- iOS 14 
    - 자, 14가 바뀌었다고 하니 이 부분이 중요하겠죠. 섹션 스냅샷이라는 기능이 생긴건데요. 섹션 스냅샷은 UICollectionView의 단일 섹션에 대한 데이터를 캡슐화합니다.
    - 또, iOS 14 전체에서 볼 수 있는 일반적인 시각적 디자인인 개요 스타일 UI 렌더링을 지원하는 데 필요한 계층적 데이터의 모델링을 허용합니다.
    - 무슨 말이냐면, 일단 아래의 그림을 보죠. 총 3개의 섹션으로 나누어져 있습니다. 이 섹션 하나하나의 데이터가 섹션 스냅샷으로 캡슐화되어 있다는 거에요. 
    - 그리고 아래의 코드에 보면 Emoji에 관한 데이터가 있죠. 그것을 두번째 코드의 applyInitialSnapshots() 이라는 메소드에서 작업해주고 있어요. 
    - 먼저 NSDiffableDataSourceSnapshot 이라는 것을 만들고, 다음에 recentsSnapshot 를 만들죠. 얘는 첫번째 섹션이니까 아이템만 넣어주면 됩니다.
    - 하지만 두번째 같은 경우에는 여기서는 outlineSnapshot 인데요. 레이블은 카테고리로 요리, 자연 같은 것들인데 내부는 여우, 거북이, 벌 같은 것들이에요. 즉, 내가 원하는 대로 Emoji의 데이터를 사용해서 새롭게 모델링을 해준 것이죠. 약간 RxDataSource 느낌도 나는 것 같아요.
			
![](https://velog.velcdn.com/images/dev_kickbell/post/777237df-ae90-48ba-9501-466e403b3c68/image.png)

<details>
  <summary><a href=""></a>Emoji</summary>
  <p>

```swift
struct Emoji: Hashable {

    enum Category: CaseIterable, CustomStringConvertible {
        case recents, smileys, nature, food, activities, travel, objects, symbols
    }
    
    let text: String
    let title: String
    let category: Category
    private let identifier = UUID()
}

extension Emoji.Category {
    
    var description: String {
        switch self {
        case .recents: return "Recents"
        case .smileys: return "Smileys"
        case .nature: return "Nature"
        case .food: return "Food"
        case .activities: return "Activities"
        case .travel: return "Travel"
        case .objects: return "Objects"
        case .symbols: return "Symbols"
        }
    }
    
    var emojis: [Emoji] {
        switch self {
        case .recents:
            return [
                Emoji(text: "🤣", title: "Rolling on the floor laughing", category: self),
                Emoji(text: "🥃", title: "Whiskey", category: self),
                Emoji(text: "😎", title: "Cool", category: self),
                Emoji(text: "🏔", title: "Mountains", category: self),
                Emoji(text: "⛺️", title: "Camping", category: self),
                Emoji(text: "⌚️", title: " Watch", category: self),
                Emoji(text: "💯", title: "Best", category: self),
                Emoji(text: "✅", title: "LGTM", category: self)
            ]

        case .smileys:
            return [
                Emoji(text: "😀", title: "Happy", category: self),
                Emoji(text: "😂", title: "Laughing", category: self),
                Emoji(text: "🤣", title: "Rolling on the floor laughing", category: self)
            ]
            
        case .nature:
            return [
                Emoji(text: "🦊", title: "Fox", category: self),
                Emoji(text: "🐝", title: "Bee", category: self),
                Emoji(text: "🐢", title: "Turtle", category: self)
            ]
            
        case .food:
            return [
                Emoji(text: "🥃", title: "Whiskey", category: self),
                Emoji(text: "🍎", title: "Apple", category: self),
                Emoji(text: "🍑", title: "Peach", category: self)
            ]
        case .activities:
            return [
                Emoji(text: "🏈", title: "Football", category: self),
                Emoji(text: "🚴‍♀️", title: "Cycling", category: self),
                Emoji(text: "🎤", title: "Singing", category: self)
            ]

        case .travel:
            return [
                Emoji(text: "🏔", title: "Mountains", category: self),
                Emoji(text: "⛺️", title: "Camping", category: self),
                Emoji(text: "🏖", title: "Beach", category: self)
            ]

        case .objects:
            return [
                Emoji(text: "🖥", title: "iMac", category: self),
                Emoji(text: "⌚️", title: " Watch", category: self),
                Emoji(text: "📱", title: "iPhone", category: self)
            ]

        case .symbols:
            return [
                Emoji(text: "❤️", title: "Love", category: self),
                Emoji(text: "☮️", title: "Peace", category: self),
                Emoji(text: "💯", title: "Best", category: self)
            ]

        }
    }
}
```
  </p>
</details>

<details>
  <summary><a href=""></a>applyInitialSnapshots()</summary>
  <p>

```swift
    /// - Tag: SectionSnapshot
    func applyInitialSnapshots() {

        // set the order for our sections

        let sections = Section.allCases
        var snapshot = NSDiffableDataSourceSnapshot<Section, Item>()
        snapshot.appendSections(sections)
        dataSource.apply(snapshot, animatingDifferences: false)
        
        // recents (orthogonal scroller)
        
        let recentItems = Emoji.Category.recents.emojis.map { Item(emoji: $0) }
        var recentsSnapshot = NSDiffableDataSourceSectionSnapshot<Item>()
        recentsSnapshot.append(recentItems)
        dataSource.apply(recentsSnapshot, to: .recents, animatingDifferences: false)

        // list of all + outlines
        
        var allSnapshot = NSDiffableDataSourceSectionSnapshot<Item>()
        var outlineSnapshot = NSDiffableDataSourceSectionSnapshot<Item>()
        
        for category in Emoji.Category.allCases where category != .recents {
            // append to the "all items" snapshot
            let allSnapshotItems = category.emojis.map { Item(emoji: $0) }
            allSnapshot.append(allSnapshotItems)
            
            // setup our parent/child relations
            let rootItem = Item(title: String(describing: category), hasChildren: true)
            outlineSnapshot.append([rootItem])
            let outlineItems = category.emojis.map { Item(emoji: $0) }
            outlineSnapshot.append(outlineItems, to: rootItem)
        }
        
        dataSource.apply(recentsSnapshot, to: .recents, animatingDifferences: false)
        dataSource.apply(allSnapshot, to: .list, animatingDifferences: false)
        dataSource.apply(outlineSnapshot, to: .outline, animatingDifferences: false)
        
        // prepopulate starred emojis
        
        for _ in 0..<5 {
            if let item = allSnapshot.items.randomElement() {
                self.starredEmojis.insert(item)
            }
        }
    }
```
  </p>
</details>

### 재정렬 API 
- 새로운 재정렬(reordering) API가 추가되었습니다. 
- 공식문서는 [여기](https://developer.apple.com/documentation/uikit/uicollectionviewdiffabledatasource/reorderinghandlers)이고, 관련 세션은 위에 있는 [Advances in Diffable Data Source](https://developer.apple.com/videos/play/wwdc2020/10045) 입니다. 코드도 첨부해놨으니 무슨 느낌인지 대충 알 수 있겠죠. 

<details>
  <summary><a href=""></a>reorderingHandlers</summary>
  <p>

```swift
    func configureDataSource() {
        
        // list cell
        let cellRegistration = UICollectionView.CellRegistration<UICollectionViewListCell, Emoji> { (cell, indexPath, emoji) in
            var contentConfiguration = UIListContentConfiguration.valueCell()
            contentConfiguration.text = emoji.text
            contentConfiguration.secondaryText = String(describing: emoji.category)
            cell.contentConfiguration = contentConfiguration
            
            cell.accessories = [.disclosureIndicator(), .reorder(displayed: .always)]
        }
        
        // data source
        dataSource = UICollectionViewDiffableDataSource<Section, Item>(collectionView: collectionView) {
            (collectionView, indexPath, item) -> UICollectionViewCell? in
            return collectionView.dequeueConfiguredReusableCell(using: cellRegistration, for: indexPath, item: item.emoji)
        }
        
        dataSource.reorderingHandlers.canReorderItem = { item in return true }
        dataSource.reorderingHandlers.didReorder = { [weak self] transaction in
            guard let self = self else { return }
            
            // method 1: enumerate through the section transactions and update
            //           each section's backing store via the Swift stdlib CollectionDifference API

            if self.reorderingMethod == .collectionDifference {

                for sectionTransaction in transaction.sectionTransactions {
                    let sectionIdentifier = sectionTransaction.sectionIdentifier
                    if let previousSectionItems = self.backingStore[sectionIdentifier],
                       let updatedSectionItems = previousSectionItems.applying(sectionTransaction.difference) {
                        self.backingStore[sectionIdentifier] = updatedSectionItems
                    }
                }
            
            // method 2: use the section transaction's finalSnapshot items as the new updated ordering
                
            } else if self.reorderingMethod == .finalSnapshot {

                for sectionTransaction in transaction.sectionTransactions {
                    let sectionIdentifier = sectionTransaction.sectionIdentifier
                    self.backingStore[sectionIdentifier] = sectionTransaction.finalSnapshot.items
                }
            }
        }
        
    }
```
  </p>
</details>

## 3. 리스트 구성(List Configuration)
    
### 리스트
- iOS 14에서는 Compositional Layout을 기반으로 Lists라는 새로운 기능이 추가되었습니다. 
- Lists를 사용하면 컬렉션뷰에 테이블뷰와 같은 느낌의 셀을 넣어줄 수 있습니다. 아래 그림처럼 말이죠. 
- Lists에는 테이블뷰에서 기대할 수 있는 Swipe 같은 기능도 포함되어 있습니다. 
- Compositional Layout으로 List 스타일의 레이아웃을 만드는 것도 코드 2줄이면 됩니다. 더 알고 싶다면 아래의 세션을 참고하면 됩니다.
- [Advances in Collection View Layout](https://developer.apple.com/videos/play/wwdc2019/215)
- [Lists in UICollectionView](https://developer.apple.com/videos/play/wwdc2020/10026)

    
```swift
let configuration = UICollectionLayoutListConfiguration(appearance: .insetGrouped)
let layout = UICollectionViewCompositionLayout.list(using: configuration)
```
		
![](https://velog.velcdn.com/images/dev_kickbell/post/df503f79-55b7-4326-87d0-c372736ac7c0/image.png)

    
### 셀 등록
- CellRegisteration을 사용해서 새로운 방법으로 셀을 등록할 수 있습니다. ViewModel에서 셀을 설정하는 간단하고 재사용 가능한 방법입니다.
- reuseIdentifier와 연결하기 위해 Cell 클래스와 register하는 과정이 생략됩니다.
- 제네릭 타입으로 <MyCell, ViewModel> 이 들어가는데, 샘플 코드에서는 <UICollectionViewListCell, Emoji> 입니다.
- CellRegistration 클로저 안에는 셀 컨텐츠 구성에 관한 코드가 들어갑니다. 

```swift
//before
static let reuseIdentifier = String(describing: MyCell.self)
   
tableView.register(MyCell.self, forCellReuseIdentifier: MyCell.reuseIdentifier)

    
//after
func configureDataSource() {
    // list cell
    let cellRegistration = UICollectionView.CellRegistration<UICollectionViewListCell, Emoji> { (cell, indexPath, emoji) in
        // configure cell content
        var contentConfiguration = UIListContentConfiguration.valueCell()
        contentConfiguration.text = emoji.text
        contentConfiguration.secondaryText = String(describing: emoji.category)
        cell.contentConfiguration = contentConfiguration

        cell.accessories = [.disclosureIndicator()]
    }
    
    // data source
    dataSource = UICollectionViewDiffableDataSource<Section, Item>(collectionView: collectionView) {
        (collectionView, indexPath, item) -> UICollectionViewCell? in
        return collectionView.dequeueConfiguredReusableCell(using: cellRegistration, for: indexPath, item: item.emoji)
    }
}
```


## 4. 리스트 셀/뷰 구성(List Cell/View Configuration)
    
### 셀 콘텐츠 구성 
- 셀 콘텐츠 구성은 UITableView 표준 셀 타입에 표시되는 것과 유사한 셀에 대한 표준화된 레이아웃을 제공합니다. 
- 아래의 코드 순서대로 그림 순서와 같습니다. 
- 실제의 셀 콘텐츠 구성 내용은 위의 코드 처럼 셀 등록하는 부분에 표시됩니다. 

```swift
var contentConfiguration = UIListContentConfiguration.cell()
contentConfiguration.image = UIImage(systemNamed: "hammer")
contentConfiguration.text = "Ready. Set. Code."
cell.contentConfiguration = contentConfiguration
    
var contentConfiguration = UIListContentConfiguration.valueCell()
contentConfiguration.image = UIImage(systemNamed: "hammer")
contentConfiguration.text = "Ready. Set. Code."
cell.contentConfiguration = contentConfiguration
    
var contentConfiguration = UIListContentConfiguration.subtitleCell()
contentConfiguration.image = UIImage(systemNamed: "hammer")
contentConfiguration.text = "Ready. Set. Code."
cell.contentConfiguration = contentConfiguration
```

![](https://velog.velcdn.com/images/dev_kickbell/post/540959f0-e4b7-4055-90a9-e5386fcf107a/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/087f62f6-5b43-4b54-aa5f-5aba64ed698b/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/82920df0-1bd9-4224-ac2c-b1b83422d7b7/image.png)


### 배경 구성 
- 이는 콘텐츠 구성과 매우 유사하지만 색상, 테두리 스타일 등과 같은 속성을 조정하는 기능을 통해 모든 셀의 배경에 적용됩니다.
- 이 녀석도 마찬가지로 CellRegistration 클로저 내부에서 호출됩니다. 더 자세한 사항은 아래의 세션을 참고하시면 됩니다. 
- [Modern cell configuration](https://developer.apple.com/videos/play/wwdc2020/10027)
    
    
```swift
func createGridCellRegistration() -> UICollectionView.CellRegistration<UICollectionViewCell, Emoji> {
    return UICollectionView.CellRegistration<UICollectionViewCell, Emoji> { (cell, indexPath, emoji) in
        var content = UIListContentConfiguration.cell()
        content.text = emoji.text
        content.textProperties.font = .boldSystemFont(ofSize: 38)
        content.textProperties.alignment = .center
        content.directionalLayoutMargins = .zero
        cell.contentConfiguration = content
        var background = UIBackgroundConfiguration.listPlainCell()
        background.cornerRadius = 8
        background.strokeColor = .systemGray3
        background.strokeWidth = 1.0 / cell.traitCollection.displayScale
        cell.backgroundConfiguration = background
    }
}
```
    
## Reference

[https://developer.apple.com/videos/play/wwdc2020/10097](https://developer.apple.com/videos/play/wwdc2020/10097)					
				    
[https://developer.apple.com/documentation/uikit/views_and_controls/collection_views/implementing_modern_collection_views](https://developer.apple.com/documentation/uikit/views_and_controls/collection_views/implementing_modern_collection_views)

## Endnotes 

### ¹스냅샷(Snapshot)
- 사진을 찍듯이 특정 시점에 데이터를 별도의 파일이나 이미지로 저장, 보관하는 기술을 말합니다. 그래서 스냅샷 기능을 이용하여 데이터를 저장하면 유실된 데이터 복원과 일정 시점의 상태로 데이터를 복원할 수 있습니다.



