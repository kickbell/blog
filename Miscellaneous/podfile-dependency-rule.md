업무 중에 가끔 별게 아닌데 헷갈릴 때가 있습니다. Podfile과 SPM(Swift Package Manager)의 버전에 관한 부분이에요. 

## Podfile 
Swift 프로젝트를 확장하기 위한 CocoaPods이라는 의존성 관리자가 있습니다. 그리고 Podfile은 프로젝트 타겟의 의존성을 설명해주는 일종의 명세서입니다. Podfile에 `pod` 이라는 키워드로 원하는 라이브러리를 지정하고 `pod install` 라는 명령어로 pod을 설치하면 `Podfile.lock` 이라는 파일이 생성됩니다. 이 파일에는 현재 설치된 pod에 대한 여러가지 정보가 들어있죠. ( 사실 설치하기 전에도 `Project Folder` -> `Pods` -> `Manifest.lock` 에 들어가보면 같은 정보가 들어있긴 합니다😄. ) 

`Podfile.lock` 이 별게 아닌 것 처럼 보여도 꽤 자주 쓰입니다. 예를 들어 하나의 프로젝트를 여러 명이 작업하는 경우, 각자의 프로젝트에서 일부 라이브러리의 버전을 업데이트 했다면 충돌하거나 컴파일 에러를 발생시킬 수 있겠죠. 이런 경우엔 먼저 `Podfile.lock` 을 통해 현재 설치된 라이브러리의 버전을 확인해보고 수정하던지, 또는 해당 라이브러리의 github으로 들어가서 우측 중간쯤에 있는 `Releases` 정보나 프로젝트 내의 `.podspec` 을 확인해 볼 수 있어요. ( `.podspec` 말그대로 해당 pod의 spec을 정의해놓은 문서입니다. `.podspec` 예시는 아래에서 나옵니다😀. ) 



```swift
//Podfile
# Uncomment the next line to define a global platform for your project
# platform :ios, '9.0'

target 'Dependencies' do
  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!

  # Pods for Dependencies

  pod 'Moya/RxSwift', '~> 14.0'

end
```
```swift
//Podfile.lock
PODS:
  - Alamofire (5.6.1)
  - Moya/Core (14.0.0):
    - Alamofire (~> 5.0)
  - Moya/RxSwift (14.0.0):
    - Moya/Core
    - RxSwift (~> 5.0)
  - RxSwift (5.1.3)

DEPENDENCIES:
  - Moya/RxSwift (~> 14.0)

SPEC REPOS:
  trunk:
    - Alamofire
    - Moya
    - RxSwift

SPEC CHECKSUMS:
  Alamofire: 87bd8c952f9a4454320fce00d9cc3de57bcadaf5
  Moya: 5b45dacb75adb009f97fde91c204c1e565d31916
  RxSwift: 915abbdfb62214aa89ccd0b194d44fb478019b27

PODFILE CHECKSUM: 2c330e20c2a0718e68c1e9fc5fce91f57c2a5b65

COCOAPODS: 1.10.2
```

실제로 Moya 라이브러리를 Podfile을 통해 설치하고, Podfile.lock을 보면 위와 같이 문서를 볼 수 있어요. Moya는 라이브러리 내부에서 Alamofire는 기본으로 사용하고 있고, 선택에 따라서는 RxMoya를 지원하기 때문에 RxSwift도 필요합니다. 그래서 저는 Moya만 설치했다고 생각했지만, Alamofire와 RxSwift가 같이 설치되었던 것이지요. 

음, 좀 더 쉽게 설명하기 위해 예전에 있었던 이슈를 하나 이야기해볼게요. 

저희 사내에서는 앱에서 네트워크 관련 추상화를 더 쉽게 해주는 [Moya](https://github.com/Moya/Moya) 라이브러리 14.0.0 버전을 사용 중이었습니다. 그리고 또 팀 내에서 RxSwift를 사용하고 있었는데, RxSwift 6.0이 릴리즈 되어 도입하기로 했죠. 그런데, RxSwift의 버전을 6.0으로 업데이트 하는 순간 컴파일 에러가 발생하는 거에요. 당장 일은 해야 하는데 빌드는 안되고, 프로젝트가 워낙 크다보니 빌드 시간도 오래 걸리고, 원인도 모르겠고, 그냥 짜증만 나는 겁니다. 하핳...😱 그렇게 끙끙대다가 마침내 Podfile, Podfile.lock, Moya.podspec 을 보고 원인을 찾았습니다. 하나하나 차근차근 설명해볼게요. 

일단 Podfile에서는 `pod 'Moya/RxSwift', '~> 14.0'`라고 정의하고 있죠. 여기서 중요한 부분은 2가지에요. 

첫째는 `'~> 14.0'` 인데요. 이 뜻은 `14.0.0 부터 15.0.0 미만` 까지의 버전을 커버하겠다는 뜻입니다. 앞에서 부터 차례차례 Major, Minor, Patch 라고 부르는 버저닝 방식인데 이런 버저닝 방식을 [Semantic Versioning](https://semver.org/) 이라고 합니다. 어쨋건 결론적으로 중요한건 Moya의 버전이 `15.0.0` 으로 업데이트 되더라도 커버는 `< 15.0.0` 이기 때문에 라이브러리를 업데이트 하지 않는다는 거죠. 

두번째는 `pod 'Moya'`가 아니라 `pod 'Moya/RxSwift'` 라는 겁니다. 음 이게 무슨 의미가 있는지는 [Moya.podspec](https://github.com/Moya/Moya/blob/master/Moya.podspec) 을 봐야해요. 

```swift
//Moya.podspec
Pod::Spec.new do |s|
  s.name         = "Moya"
  s.version      = "14.0.0"
  s.summary      = "Network abstraction layer written in Swift"
  s.homepage     = "https://github.com/Moya/Moya"
  s.source       = { :git => "https://github.com/Moya/Moya.git", :tag => s.version }
  s.default_subspec = "Core"
  s.swift_version = '5.3'

  s.subspec "Core" do |ss|
    ss.source_files  = "Sources/Moya/", "Sources/Moya/Plugins/"
    ss.dependency "Alamofire", "~> 5.0"
    ss.framework  = "Foundation"
  end

  s.subspec "Combine" do |ss|
    ...
  end

  s.subspec "ReactiveSwift" do |ss|
    ...
  end

  s.subspec "RxSwift" do |ss|
    ss.source_files = "Sources/RxMoya/"
    ss.dependency "Moya/Core"
    ss.dependency "RxSwift", "~> 5.0"
  end
end
```

위의 파일에서는 복잡한 부분은 다 삭제를 한 것인데요. 잘보면 `subspec` 이라는 부분이 나옵니다. 이건 cocoapods의 기능 중에 하나인데요. 번역하면 하위 사양? 정도가 되겠네요. 이게 왜 필요할까요 ? 이건 라이브러리를 선택적으로 사용하기 위해서 필요해요. 위에 보시면 subspec이 `Core`, `Combine`, `ReactiveSwift`, `RxSwift` 총 4개 있죠. 그리고 또 `default_subspec` 이라고도 되어 있어요. 이게 무슨 말이냐면 Moya는 네트워크 추상화를 더 편하게 하기 위해 사용되는 라이브러리죠. 그리고 Moya의 최소 기능을 다운받기 위해 `pod 'Moya'`를 하게되면 `default_subspec` 인 `Core`만 다운로드가 됩니다. 그리고 `Core`에서는 dependency로 Alamofire를 사용하고 있죠? 그래서 Alamofire가 같이 다운로드 되죠. 

그런데, 나는 Moya에서 어떤 이벤트를 반응형으로 받을 수 있는 subspec인 RxSwift의 아래와 같은 기능을 사용하고 싶다? 그러면 `pod 'Moya/RxSwift'`를 하는 거죠. 이러면 기본 `default_subspec` 에 지정한 subspec까지 추가로 다운로드가 됩니다. 마찬가지로 Combine이 필요하다면 `pod 'Moya/Combine'`, ReactiveSwift가 필요하다면 `pod 'Moya/ReactiveSwift'`를 해주면 되겠죠. 

```swift
provider = MoyaProvider<GitHub>()
provider.rx.request(.userProfile("ashfurrow")).subscribe { event in
    switch event {
    case let .success(response):
        image = UIImage(data: response.data)
    case let .error(error):
        print(error)
    }
}
```

아 너무 돌아왔네요.. 😆 그래서 문제의 원인이 뭔데 ? 라고 물으신다면 정답은 이렇습니다. 

`pod 'Moya/RxSwift', '~> 14.0'` 를 했기 때문에 프로젝트 내에서는 기본 Moya만이 아니라 RxSwift도 같이 사용하고 있었고, Moya 내의 subspec인 RxSwift에서는 dependency로 RxSwift를 사용하고 있어요. 그런데, `"RxSwift", "~> 5.0"` 이니까 RxSwift 6.0.0 이상의 버전이 지원되지 않는 것이죠. ( 지금은 Moya 15.0.0 이 릴리즈 되어 RxSwift 6.0이 지원 됩니다. ) 

좀 복잡했죠 ? 하핳.. 그래도 알아두면 도움이 되실 겁니다. 하지만 그래서 결론적으로 Podfile에 대해서 말하고자 하는 것은 버전 커버가 어디까지 되는지에 관해서 입니다. 관련없는 얘기가 더 많았지만요. 더 자세한 설명이 필요하시면 [여기](https://guides.cocoapods.org/using/the-podfile.html)에 있습니다.

- `pod 'PodName', '> 0.1'` : 0.1 보다 높은 ( 0.1 미포함 )
- `pod 'PodName', '>= 0.1'` : 0.1 이상 ( 0.1 포함 )
- `pod 'PodName', '< 0.1'` : 0.1 미만 ( 0.1 미포함 )
- `pod 'PodName', '<= 0.1'` : 0.1 이하 ( 0.1 포함 )
- `pod 'PodName', '~> 0.1.2'` : 0.1.2 부터 0.2.0 미만 ( 버저닝이 3자리이기 때문에 Patch를 기준으로 합니다. 따라서 0.1.2부터 0.1.x까지 커버가 되니 0.2.0 미만 입니다. )
- `pod 'PodName', '~> 0.1'` : 0.1.0 부터 1.0.0 미만 ( 이번엔 버저닝이 2자리이기 때문에 Minor를 기준으로 합니다. 따라서 0.1.0 부터 0.x.0까지 커버를 하니 1.0.0 미만 입니다. )
- `pod 'PodName', '~> 0'` : 0.0.0 부터 1.0.0 미만 ( 버저닝 1자리 이므로 Major를 기준으로 합니다. 따라서 0.0.0 부터 0.x.x까지 커버링 하므로 1.0.0미만 입니다. 만약에 `'~> 5'` 였다면 5.0.0 부터 5.x.x(6.0.0 미만) 이겠죠. ) 


## Dependency Rule(SPM)
애초에 버저닝 커버가 어디까지 되는지만 설명하려고 적었던 글인데 너무 길어졌네요.. 😂 이쪽은 좀 줄여야겠습니다. 

Cocoapods이 의존성 관리의 Third Party라고 한다면 Swift Package Manager는 First Party이죠. 당연히 호환성이나 성능면에서 더 나을 것이고, 장기적으로는 의존성 라이브러리들이 SPM으로 대체가 될 거에요. 

위에서 제가 설명을 많이 해놓아서 지금 좀 도움이 됩니다만, Cocoapods에서 PodName.podspec이 있다면 SPM에서는 The package manifest 가 있습니다. 라이브러리 내에서는 Package.swift 라는 이름의 파일이죠. 아래에 나와있는 것처럼 플랫폼을 지정할 수 있고, 의존성도 지정할 수 있습니다. 이것도 마찬가지로 Moya의 Package.swift 예시인데요. 구성이 꽤나 비슷하죠? subspec을 products라고 생각하면, Core가 Moya 이고 product RxMoya는 pod에서 default_subspec이었던 Moya와 RxSwift를 dependencies로 가지고 있네요. 

```swift
//Package.swift
//swift-tools-version:5.0

import PackageDescription

let package = Package(
    name: "Moya",
    platforms: [
        .macOS(.v10_12),
        .iOS(.v10),
        .tvOS(.v10),
        .watchOS(.v3)
    ],
    products: [
        .library(name: "Moya", targets: ["Moya"]),
        .library(name: "ReactiveMoya", targets: ["ReactiveMoya"]),
        .library(name: "RxMoya", targets: ["RxMoya"])
    ],
    dependencies: [
        .package(url: "https://github.com/Alamofire/Alamofire.git", .upToNextMajor(from: "5.0.0")),
        .package(url: "https://github.com/Moya/ReactiveSwift.git", .upToNextMajor(from: "6.1.0")),
        .package(url: "https://github.com/ReactiveX/RxSwift.git", .upToNextMajor(from: "5.0.0")),
    ],
    targets: [
        .target(name: "Moya", dependencies: ["Alamofire"]),
        .target(name: "ReactiveMoya", dependencies: ["Moya", "ReactiveSwift"]),
        .target(name: "RxMoya", dependencies: ["Moya", "RxSwift"]),
    ]
)

#if canImport(PackageConfig)
import PackageConfig

let config = PackageConfiguration([
    "rocket": [
	"before": [
            "scripts/update_changelog.sh",
            "scripts/update_podspec.sh"
	],
	"after": [
            "rake create_release\\[\"$VERSION\"\\]"
	]
    ]
]).write()
#endif
```

그리고 pod의 Podfile.lock 과 대응될 만한 파일이 Package.resolved 인데요. 마찬가지로 다운로드 된 의존성 라이브러리들에 관한 정보를 가지고 있어요. 단순히 SPM만 다운로드 헀다면 이 파일을 다운로드 할 수는 없고, 해당 SPM 프로젝트 내에서 확인할 수 있어요. 그래도 상관없는게 Xcode의 Project navigator에서 현재의 Package Dependencies 들의 버전 정보를 볼 수 있기 때문에 괜찮아요. 

```swift
//Package.resolved
{
  "object": {
    "pins": [
      {
        "package": "Alamofire",
        "repositoryURL": "https://github.com/Alamofire/Alamofire.git",
        "state": {
          "branch": null,
          "revision": "0c8cb78d05b6d067ee331c05058ff4dedcb45ffa",
          "version": "5.0.0"
        }
      },
      {
        "package": "ReactiveSwift",
        "repositoryURL": "https://github.com/Moya/ReactiveSwift.git",
        "state": {
          "branch": null,
          "revision": "f195d82bb30e412e70446e2b4a77e1b514099e88",
          "version": "6.1.0"
        }
      },
      {
        "package": "RxSwift",
        "repositoryURL": "https://github.com/ReactiveX/RxSwift.git",
        "state": {
          "branch": null,
          "revision": "b3e888b4972d9bc76495dd74d30a8c7fad4b9395",
          "version": "5.0.1"
        }
      }
    ]
  },
  "version": 1
}
```

어쨋건 SPM 이야기는 여기까지만 하고, 버저닝을 볼까요 ? 정확한 표현은 Dependency Rule 이라고 합니다. 위의 예시의 Package.swift 파일에서는 의존성 라이브러리를 가져올 때 매개변수로 url과 range를 받고 있는데요. 종류는 아래와 같습니다. SPM 에서도 버저닝에 Semantic Versioning를 따르고 있기 때문에 표현 방식이 유사해요.

- `static func package(name: String, path: String)` 
	- 파일 시스템의 지정된 경로를 통해서 의존성을 추가합니다.
- `static func package(url: String, from: Version)` : 
	- `.package(url: "https://example.com/example-package.git", from: "1.2.3")`
	- from 버전 부터 다음 Major전까지 ( 1.2.3 이상 2.0.0 미만 ) 
- `static func package(url: String, Range<Version>)` 			
	- `.package(url: "https://example.com/example-package.git", "1.2.3"..<"1.2.6")`
	- 지정된 버전부터 지정된 최대버전을 포함하지 않는 이전버전까지 ( 1.2.3 부터 1.2.6 미만 )
	- `..<` 를 사용하는 것을 Swift에서는 Range의 타입 중 하나로 `Half-Open Range` 라고 부릅니다.
- `static func package(url: String, ClosedRange<Version>)`
	- `.package(url: "https://example.com/example-package.git", "1.2.3"..."1.2.6")`
	- 지정된 버전부터 지정된 최대버전을 포함하는 버전까지 ( 1.2.3 부터 1.2.6 이하 )
	- `...` 를 사용하는 것을 Swift에서는 Range의 타입 중 하나로 `Closed Range` 라고 부릅니다.
- `static func package(url: String, branch: String)`
	- `.package(url: "https://example.com/example-package.git", branch: "main")`
	- 지정된 특정 브랜치에서 의존성을 가져옵니다. 변경된 사항이 있으면 업데이트 합니다. 
- `static func package(url: String, revision: String)` 
	- `.package(url: "https://example.com/example-package.git", revision: "aa681bd6c61e22df0fd808044a886fc4a7ed3a65")`
	- 지정된 특정 커밋에서 의존성을 추가합니다.
- `static func package(url: String, exact: Version)` 
	- `.package(url: "https://example.com/example-package.git", exact: "1.2.3")`
	- 정확한 버전을 지정합니다. ( 1.2.3 만 가능 ) 
- `static func package(path: String)` 
	- 지정된 경로에 있는 패키지에 의존성을 추가합니다.

그리고 매개변수로 Range<Version>와 ClosedRange<Version>를 받는 녀석들 같은 경우는 타입메서드도 지원합니다. 문서에서는 Deprecated 됐다고 하는데, 일단은 사용 가능 합니다. 아래처럼 사용할 수 있어요.  

- `.package(url: "https://example.com/example-package.git", .upToNextMajor(from: "1.2.3"))`
- `.package(url: "https://example.com/example-package.git", .upToNextMinor(from: "1.2.3"))`
- `.package(url: "https://example.com/example-package.git", .exact("1.2.3"))`
- `.package(url: "https://example.com/example-package.git", .branch("main"))`
- `.package(url: "https://example.com/example-package.git", .revision("aa681bd6c61e22df0fd808044a886fc4a7ed3a65"))`

그리고 Package.swift 파일로 의존성을 추가하지 않고 Xcode로 SPM을 추가할 수도 있는데요. 이 방법 같은 경우엔 이 타입메서드가 뜻이 뭐였더라? 혹은 어느버전까지 지원했더라? 하고 헷갈릴 때 확인하는 용도로도 좋습니다. `File` -> `Add Packages...` 을 해볼게요. 그러면 아래의 팝업창이 뜹니다. 그리고 원하는 라이브러리를 검색해주면 되는데, 여기서는 RxSwift를 입력해볼게요. git 주소로도 가능합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/04b72cbb-9e91-4154-84e4-e71cf0aacc3a/image.png)

그러면 Dependuncy Rule이라는 메뉴가 있고, 위에서 적었던 것들이 나오죠 ? 그리고 재밌는건 지금 6.0.0 으로 입력이 되어있는데 그럼 최대 커버 가능한 버전이 7.0.0 이라고 자동으로 찍혀서 나옵니다. 현재 RxSwift의 릴리즈된 버전이 6.5.0 이라서 그래요. 당연히 저 최소버전은 지정할 수 있습니다. 저걸 Minor로 바꾸면 어떻게 될까요 ? 
  
![](https://velog.velcdn.com/images/dev_kickbell/post/b75f2e35-76e6-4e86-bb41-74ec8f615956/image.png)
  
그럼 이렇게 나옵니다. Minor가 가운데 자릿수니까 6.6.0 미만까지 커버가 되는거죠. 마찬가지 방법으로 Range of Version은 버전을 지정할 수 있게 나오고, Exact는 특정 버전 지정, Branch와 Commit도 특정 브랜치와 커밋으로 똑같습니다. 
  
    
## Reference 
[https://guides.cocoapods.org/using/the-podfile.html](https://guides.cocoapods.org/using/the-podfile.html)	
  
[https://guides.cocoapods.org/making/specs-and-specs-repo.html](https://guides.cocoapods.org/making/specs-and-specs-repo.html)		        	
  
[https://semver.org/](https://semver.org/)				        
  
[https://developer.apple.com/documentation/packagedescription/package/dependency
](https://developer.apple.com/documentation/packagedescription/package/dependency)		        		
