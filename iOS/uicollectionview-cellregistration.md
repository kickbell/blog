# UICollectionView.CellRegistration<Cell, Item>

## 1. 개요

iOS 14에서 `UICollectionView.CellRegistration` 라는 API가 등장했지요. 이름 그대로 컬렉션뷰에서 셀을 등록하는 API 입니다. 자매품으로 `UITableView.CellRegistration` 은 없습니다. 같이 나온 `UICollectionViewListCell` 이 컬렉션뷰에서도 스와이프가 가능한 테이블뷰 비슷한 셀을 구현할 수 있게 해주기 때문이죠.  

기존의 우리가 셀을 등록할 때는 어떻게 했었나요 ? 통상적으로는 아래와 같이 작성하곤 했습니다. 셀을 만들고 `reuseIdentifier` 를 생성하고 뷰컨트롤러의 `viewDidLoad` 에서 셀을 등록하곤 하는 식이죠. 

```swift
class SmallTableCell: UICollectionViewCell {
    static let reuseIdentifier: String = "SmallTableCell"
}

class AppsViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
		collectionView.register(SmallTableCell.self, forCellReuseIdentifier: SmallTableCell.reuseIdentifier)
	}
}
```

`UICollectionView.CellRegistration` 에서는 조금 바뀌었습니다. 공식 문서를 보면 아래와 같이 셀을 등록해요. `UICollectionViewListCell`이 위의 코드로 치면 `SmallTableCell` 이 되겠네요. Int는 클로저에서 데이터 값으로 사용하는 `item` 의 타입입니다. 

```swift
let cellRegistration = UICollectionView.CellRegistration<UICollectionViewListCell, Int> { cell, indexPath, item in
    
    var contentConfiguration = cell.defaultContentConfiguration()
    
    contentConfiguration.text = "\(item)"
    contentConfiguration.textProperties.color = .lightGray
    
    cell.contentConfiguration = contentConfiguration
}

dataSource = UICollectionViewDiffableDataSource<Section, Int>(collectionView: collectionView) {
    (collectionView: UICollectionView, indexPath: IndexPath, itemIdentifier: Int) -> UICollectionViewCell? in
    
    return collectionView.dequeueConfiguredReusableCell(using: cellRegistration,
                                                        for: indexPath,
                                                        item: itemIdentifier)
}
```


`UICollectionView.CellRegistration` 내부는 크게 신경안쓰셔도 됩니다. 저 부분은 예전으로 치면 넘어오는 데이터를 셀의 뷰에 어떻게 보여주는 것에 대한 마치 `cellForItemAt` 의 역할이에요.

이어지는 `dataSource` 코드를 보면 `cellRegistration` 에서 셀을 어떻게 보여줄 것인지 지정하고나서, 그냥 셀을 `collectionView.dequeueConfiguredReusableCell` 하고 매개변수로 `cellRegistration` 를 넣어주고 리턴하죠? 저 리턴값이 `cellRegistration` 에서 지정한 `UICollectionViewListCell` 타입입니다. 

사용하는 방법은 약간 다르지만, 느낌은 비슷하죠. 다른 점이라고 하면 reuseIdentifier 같은 것을 사용하지 않는다는 것입니다. 공식문서에는 아래와 같이 말하고 있어요. 

> `register(_ cellClass:forCellWithReuseIdentifier:)` 또는 `register(_ nib:forCellWithReuseIdentifier:)`를 호출할 필요가 없습니다. `dequeueConfiguredReusableCell(using:for:item:)`에 셀 등록을 전달할 때 컬렉션뷰는 셀을 자동으로 등록합니다. 

자, 대략적인 사용방법의 차이를 알아봤으니 이제 두 방법을 비교해볼게요. 글을 작성하고 있는 저도 두 방법 중에 뭐가 더 낫다고 확언하기가 애매하네요. 

사용하는 서비스의 최소버전이 iOS 14 이상이어야 하는 부분도 있고, 익숙하다면 익숙한 코드가 더 나은 것도 같구요. 상황에 맞게 적절하게 사용하면 될 것 같습니다. 

이제 예시를 보겠습니다. 앱스토의 게임탭의 화면을 예시로 가져왔는데요. 그림을 보면 여러개의 컬렉션뷰 셀이 사용된 것을 보실 수 있어요. 

첫째로 제일 상단의 큰 이미지뷰가 있는 `FeaturedCell` 이 하나 있고, 두번째로는 3 개의 테이블뷰 셀의 느낌을 가지고 있는 `MediumTableCell` 셀이 있죠. 그런데, 이 부분은 헤더 부분은 스크롤링이 되지 않으니 헤더는 또 다른 뷰가 여기서의 이름은 `SectionHeader` 가 될 에정입니다.

마지막으로 두번째 사진을 보면 인기 카테고리라고 적힌 `SmallTableCell` 라는 테이블뷰 느낌의 셀이 또 있죠. 그래서 헤더까지 총 4개의 뷰가 있는 예시입니다. 이것을 가지고 `UICollectionView.CellRegistration` 를 사용하는 것과 사용하지 않는 것의 코드를 둘 다 구현해볼게요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/de69cec3-224c-4744-b74b-fa40b0c7ae57/image.jpeg)![](https://velog.velcdn.com/images/dev_kickbell/post/763814cd-1a03-4eb9-9693-185006c60fa0/image.jpeg)

## 2. register(_ cellClass:forCellWithReuseIdentifier:)

기존 방법을 사용하는 코드부터 볼게요. 글과 관련없는 뷰의 속성값 할당 같은 내용들은 다 삭제했습니다. 먼저 셀이 여러개이므로 공통적인 설정을 `SelfConfiguringCell` 담당하는 프로토콜을 선언했습니다. 

```swift
protocol SelfConfiguringCell {
    static var reuseIdentifier: String { get }
    func configure(with app: App)
}
```

그리고 각 셀에 해당 프로토콜을 준수하는 코드를 만듭니다. 헤더는 해당 프로토콜을 준수할 필요는 없겠지요. 

```swift
class FeaturedCell: UICollectionViewCell, SelfConfiguringCell {
    static var reuseIdentifier: String = "FeaturedCell"
    
    let tagline = UILabel()
    let name = UILabel()
    let subtitle = UILabel()
    let imageView = UIImageView()
    
    func configure(with app: App) {
        tagline.text = app.tagline.uppercased()
        name.text = app.name
        subtitle.text = app.subheading
        imageView.image = UIImage(named: app.image)
    }
}

class MediumTableCell: UICollectionViewCell, SelfConfiguringCell {
    static var reuseIdentifier: String = "MediumTableCell"
    
    let name = UILabel()
    let subtitle = UILabel()
    let imageView = UIImageView()
    let buyButton = UIButton(type: .custom)
    
    func configure(with app: App) {
        name.text = app.name
        subtitle.text = app.subheading
        imageView.image = UIImage(named: app.image)
    }
}

class SmallTableCell: UICollectionViewCell, SelfConfiguringCell {
    static let reuseIdentifier: String = "SmallTableCell"
    
    let name = UILabel()
    let imageView = UIImageView()
    
    func configure(with app: App) {
        name.text = app.name
        imageView.image = UIImage(named: app.image)
    }
}

class SectionHeader: UICollectionReusableView {
    static var reuseIdentifier: String = "SectionHeader"
    
    let title = UILabel()
    let subtitle = UILabel()
}
```

그리고 이제 만든 셀과 헤더를 사용합니다. 꽤나 익숙하죠 ? 특이한 점이라면 제네릭 타입으로 `SelfConfiguringCell` 프로토콜을 준수하는 `func configure<T: SelfConfiguringCell>` 메서드를 생성해서 내부에서 `cell.configure(with:)` 작업을 한번에 해줄 수 있게 하는 부분이 있겠네요. 

```swift

import UIKit

class AppsViewController: UIViewController {
    let sections = Bundle.main.decode([Section].self, from: "appstore.json")
    var collectionView: UICollectionView!

    var dataSource: UICollectionViewDiffableDataSource<Section, App>?

    override func viewDidLoad() {
        super.viewDidLoad()

		//...

        collectionView.register(SectionHeader.self, forSupplementaryViewOfKind: UICollectionView.elementKindSectionHeader, withReuseIdentifier: SectionHeader.reuseIdentifier)
        collectionView.register(FeaturedCell.self, forCellWithReuseIdentifier: FeaturedCell.reuseIdentifier)
        collectionView.register(MediumTableCell.self, forCellWithReuseIdentifier: MediumTableCell.reuseIdentifier)
        collectionView.register(SmallTableCell.self, forCellWithReuseIdentifier: SmallTableCell.reuseIdentifier)

		//...
    }

    func configure<T: SelfConfiguringCell>(_ cellType: T.Type, with app: App, for indexPath: IndexPath) -> T {
        guard let cell = collectionView.dequeueReusableCell(withReuseIdentifier: cellType.reuseIdentifier, for: indexPath) as? T else {
            fatalError("Unable to dequeue \(cellType)")
        }

        cell.configure(with: app)
        return cell
    }

    func createDataSource() {
        dataSource = UICollectionViewDiffableDataSource<Section, App>(collectionView: collectionView) { collectionView, indexPath, app in
            switch self.sections[indexPath.section].type {
            case "mediumTable":
                return self.configure(MediumTableCell.self, with: app, for: indexPath)
            case "smallTable":
                return self.configure(SmallTableCell.self, with: app, for: indexPath)
            default:
                return self.configure(FeaturedCell.self, with: app, for: indexPath)
            }
        }

        dataSource?.supplementaryViewProvider = { [weak self] collectionView, kind, indexPath in
            guard let sectionHeader = collectionView.dequeueReusableSupplementaryView(ofKind: kind, withReuseIdentifier: SectionHeader.reuseIdentifier, for: indexPath) as? SectionHeader else {
                return nil
            }
			//...
            return sectionHeader
        }
    }
    
    //...
{
```

## 3. UICollectionView.CellRegistration<Cell, Item>

다음은 새로나온 `CellRegistration` 를 사용하는 코드를 보겠습니다. 일단 `reuseIdentifier` 는 안쓰니까 필요없겠죠. 프로토콜에서 `reuseIdentifier` 를 삭제하고 각 셀에서도 삭제해줍니다. 다른 것은 바뀐 건 없어요. 

```swift
protocol SelfConfiguringCell {
    func configure(with app: App)
}
```
```swift
class FeaturedCell: UICollectionViewCell, SelfConfiguringCell {    
    let tagline = UILabel()
    let name = UILabel()
    let subtitle = UILabel()
    let imageView = UIImageView()
    
    func configure(with app: App) {
        tagline.text = app.tagline.uppercased()
        name.text = app.name
        subtitle.text = app.subheading
        imageView.image = UIImage(named: app.image)
    }
}

class MediumTableCell: UICollectionViewCell, SelfConfiguringCell {    
    let name = UILabel()
    let subtitle = UILabel()
    let imageView = UIImageView()
    let buyButton = UIButton(type: .custom)
    
    func configure(with app: App) {
        name.text = app.name
        subtitle.text = app.subheading
        imageView.image = UIImage(named: app.image)
    }
}

class SmallTableCell: UICollectionViewCell, SelfConfiguringCell {    
    let name = UILabel()
    let imageView = UIImageView()
    
    func configure(with app: App) {
        name.text = app.name
        imageView.image = UIImage(named: app.image)
    }
}

class SectionHeader: UICollectionReusableView {    
    let title = UILabel()
    let subtitle = UILabel()
}
```

다음으로 각 셀과 헤더에 맞는 `Registration` 메서드를 생성해줍니다. 이 메서드 내부에서 `cell.configure(with: app)` 을 사용하므로, 기존에 사용했던 제네릭 매개변수의 `func configure<T: SelfConfiguringCell>` 메서드는 필요가 없으니 삭제합니다. 

그리고 `dataSource` 에서 `dequeueConfiguredReusableCell(using:for:item:)` 메서드의 매개변수로 만든 `CellRegistration`, `SupplementaryRegistration` 를 넣어줍니다. 위에서 말했던 것처럼 `dequeueConfiguredReusableCell(using:for:item:)` 에 셀 등록을 전달할 때 셀을 자동으로 등록하므로 `reuseIdentifier` 같은 것들은 필요가 없습니다. 

```swift

import UIKit

class AppsViewController: UIViewController {
    let sections = Bundle.main.decode([Section].self, from: "appstore.json")
    var collectionView: UICollectionView!

    var dataSource: UICollectionViewDiffableDataSource<Section, App>?
    
    override func viewDidLoad() {
        super.viewDidLoad()
	    //...
    }
    
    func createFeaturedCellRegistration() -> UICollectionView.CellRegistration<FeaturedCell, App> {
        return UICollectionView.CellRegistration<FeaturedCell, App> { (cell, indexPath, app) in cell.configure(with: app) }
    }
    
    func createMediumTableCellRegistration() -> UICollectionView.CellRegistration<MediumTableCell, App>{
        return UICollectionView.CellRegistration<MediumTableCell, App> { (cell, indexPath, app) in cell.configure(with: app) }
    }
    
    func createSmallTableCellRegistration() -> UICollectionView.CellRegistration<SmallTableCell, App>{
        return UICollectionView.CellRegistration<SmallTableCell, App> { (cell, indexPath, app) in cell.configure(with: app) }
    }
    
    func createSectionHeaderRegistration() -> UICollectionView.SupplementaryRegistration<SectionHeader>{
        return UICollectionView.SupplementaryRegistration<SectionHeader>(elementKind: UICollectionView.elementKindSectionHeader) { supplementaryView,elementKind,indexPath in }
    }
    
    func createDataSource() {
        let featuredCellRegistration = createFeaturedCellRegistration()
        let smallTableCellRegistration = createSmallTableCellRegistration()
        let mediumTableCellRegistration = createMediumTableCellRegistration()
        let sectionHeaderRegistration = createSectionHeaderRegistration()
        
        dataSource = UICollectionViewDiffableDataSource<Section, App>(collectionView: collectionView) { collectionView, indexPath, app in
            switch self.sections[indexPath.section].type {
            case "mediumTable":
                return collectionView.dequeueConfiguredReusableCell(using: mediumTableCellRegistration, for: indexPath, item: app)
            case "smallTable":
                return collectionView.dequeueConfiguredReusableCell(using: smallTableCellRegistration, for: indexPath, item: app)
            case "featured":
                return collectionView.dequeueConfiguredReusableCell(using: featuredCellRegistration, for: indexPath, item: app)
            default:
                return UICollectionViewCell()
            }
        }
        
        dataSource?.supplementaryViewProvider = { [weak self] collectionView, kind, indexPath in
            let sectionHeader = collectionView.dequeueConfiguredReusableSupplementary(using: sectionHeaderRegistration, for: indexPath)
            //...
            return sectionHeader
        }
    }
    
	//...
}
```
    
## 4. 정리하기 

셀과 헤더의 등록 방법은 비슷한 듯 다릅니다. 무슨 방법이 더 낫다라고 하기도 애매한 것 같고 위에서 말했듯 iOS 14 부터 지원이 되는 API 이다보니 최소 버전 지원되는 부분도 있구요. 

아직 공부를 덜해서 모르는 부분이 많아 명확히 말하기가 애매한 점도 있는 것 같아요. 어쨌건, 새로 나왔으니 한 번 사용해보고 어떻게 사용하는지 정도는 알아야 좋겠지요. 저도 나중에 볼건데, 더 세부적인 학습을 위해 해당 세션의 WWDC 링크를 첨부해놓도록 하겠습니다. 
   
## Reference

[https://developer.apple.com/documentation/uikit/uicollectionview/cellregistration](https://developer.apple.com/documentation/uikit/uicollectionview/cellregistration)


[https://developer.apple.com/videos/play/wwdc2019/215/](https://developer.apple.com/videos/play/wwdc2019/215/)


[https://developer.apple.com/videos/play/wwdc2019/220/](https://developer.apple.com/videos/play/wwdc2019/220/)
	  

