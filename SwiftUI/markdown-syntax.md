---
layout: post
title: "[SwiftUI] Markdown syntax"
tags: [SwiftUI, Markdown syntax]
comments: true
---

음, 그거 아십니까? SwiftUI에서 마크다운 문법을 지원합니다. 전 몰랐어요:) 어쩌다가 우연히 알게 됐는데, 꽤나 신기해서 글로 남겨봅니다. 

사용할 수 있는 것은 iOS15부터 인데요. [iOS15 릴리즈 노트](https://developer.apple.com/documentation/ios-ipados-release-notes/ios-ipados-15-release-notes)에 아래와 같이 추가되어 있습니다. 

> ### SwiftUI
#### New Features
LocalizedStringKey can now contain Markdown syntax. The system parses Markdown strings when you create a Text view from a LocalizedStringKey, including Text views created with a string literal. The system styles Text according to Markdown constructs. (74515884) 

하나씩 차근차근 설명해볼게요. 일단 해석해보면, `LocalizedStringKey`가 마크다운 문법을 포함할 수 있다고 되어있죠? 여기서 `LocalizedStringKey` 부터 알아볼게요. 

공식문서는 [여기](https://developer.apple.com/documentation/SwiftUI/LocalizedStringKey)있습니다. iOS13부터 사용되었고, 얘는 이름도 `현지화문자열키`이긴 하지만 밑에 오버뷰를 보면 바로 의미를 이해할 수 있어요.

> Initializers for several SwiftUI types – such as Text, Toggle, Picker and others – implicitly look up a localized string when you provide a string literal.

즉, 우리가 SwiftUI에서 가장 흔하게 쓰는 타입들 Text, Toggle, Picker들이 암시적으로 현지화된 문자열을 조회한다는 거죠. 이게 무슨 말이냐 ? 

일단 우리가 `Text("Hello")`라는 뷰를 SwiftUI에서 만들다고 하면, 그건 실제로는 이 생성자를 사용하고 있는 겁니다. 

```swift
init(_ key: LocalizedStringKey, 
	   tableName: String? = nil, 
       bundle: Bundle? = nil, 
       comment: StaticString? = nil)
```

즉, 우리는 그냥 string을 넣는다고 생각하고 있지만 실제로는 아래와 같이 `LocalizedStringKey`를 생성해서 넣어주고 있는 거에요.

```swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
@frozen public struct LocalizedStringKey : Equatable, ExpressibleByStringInterpolation {

    /// Creates a localized string key from the given string value.
    ///
    /// - Parameter value: The string to use as a localization key.
    public init(_ value: String)
    ///...
}
```

그리고 아까 암시적으로 현지화된 문자열을 조회한다고 했죠? 그러면 저 `LocalizedStringKey`은 Text.init()의 예시대로 저 키값에 맞는 LocalizedStringKey가 있는지 확인을 해보는 겁니다. 그리고 키값이 있다면 현지화해서 뷰를 그려주는 거죠. 

더 구체화해서 설명하면, 아래와 같습니다. 😄

1. 현지화를 위해 Localizable.strings(English) 파일을 만든다. 
2. "안녕" = "Hello"; 를 입력해놓는다. 
3. SwiftUI에서 뷰를 그린다. Text("Hello") 
4. "Hello"는 LocalizedStringKey를 통해 현지화키값을 생성하고, 해당 키값에 맞는 value가 있는지 확인한다. 
5. 있다. Hello -> 안녕 
6. SwiftUI는 `안녕`이라는 Text 뷰를 그린다. 

그래서 그게 어쨌다는..? 

아, 약간 좀 돌아왔죠? ^^; LocalizedStringKey를 이해하기 위해서 약간 설명을 좀 하느라 돌아왔네요. 

그래서 결론은, 이 _**LocalizedStringKey가 실제로 우리가 SwiftUI에서 Text, Toggle, Picker 등을 만들 때 사용된다는 것이고 iOS 15부터는 이 LocalizedStringKey에 마크다운 문법이 적용된다는 것**_이죠. 

즉, 이게 된다는 겁니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/f3acfcc8-b2f6-4a2d-88f4-debe6cce5d4e/image.png)
```swift
import SwiftUI

struct MarkDownSwiftVIew: View {
    
    var body: some View {
        Text("""
             # Hello, world
             **This is bold text**
             *This text is italicized*
             ~~This was mistaken text~~
             **This text is _extremely_ important**
             ***All this text is important***
             [apple.com](https://www.apple.com)
             """
        )
    }
    
}

struct MarkDownSwiftVIew_Previews: PreviewProvider {
    static var previews: some View {
        MarkDownSwiftVIew()
    }
}
```
신기하죠 ?😀 

물론 전부 다 지원되는 것은 아닙니다. 아직 몇 가지 지원되지 않는 것들이 있어보여요. 

그리고 많은 마크다운의 형제? 중에서도 [GFM(Github Flavored Markdown)](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)을 지원하고 있습니다. 이건 오피셜로 애플 개발자 포럼에서 [답변](https://developer.apple.com/forums/thread/682711)을 주었어요. 

그래도 취소선, 하이퍼링크 등이 되니까 꽤나 신기한 것 같습니다. 앞으로 더 지원이 많이 되겠죠. 

아 그리고, 원한다면 현지화를 포함하지 않고 생성하는 방법도 당연히 가능한데요. 

아래의 생성자를 사용하면 가능합니다. 공식 문서는 [여기](https://developer.apple.com/documentation/swiftui/text/init(verbatim:))에 있어요. 

```swift
Text(verbatim: "pencil") // Displays the string "pencil" in any locale.
```

그리고 또 다른 예외로 이런 것도 있는데요. 아래와 같은 코드가 있고, `Localizable.strings`의 일본어 현지화 파일도 있다고 해볼게요. 

```swift
"Today" = "今日";
```

```swift
List {
    Section(header: Text("Today")) {
        ForEach(messageStore.today) { message in
            Text(message.title)
        }
    }
}
```

그럼 뷰는 어떻게 그려질까요 ? 아래와 같이 그려집니다. 
![](https://velog.velcdn.com/images/dev_kickbell/post/bc99e39d-2437-42fb-8fc2-572502650c76/image.png)

이유는 위에서 설명한 것과 같아요. Text("Today")는 `LocalizedStringKey`값을 생성해서 로컬라이징키에 따른 아웃풋이 있는지 암시적으로 조회하지만, ForEach 문을 통해 생성되는 Text(message.title)은 변수 이므로 뷰는 문자열 값을 그대로 사용하기 때문입니다. 이 내용도 [공식문서](https://developer.apple.com/documentation/swiftui/localizedstringkey)에 있는 내용이니 읽어보셔도 좋겠네요.




#### 참고자료 
https://developer.apple.com/documentation/swiftui/localizedstringkey/init(_:)
https://developer.apple.com/documentation/SwiftUI/LocalizedStringKey
https://developer.apple.com/documentation/ios-ipados-release-notes/ios-ipados-15-release-notes
https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax
https://developer.apple.com/forums/thread/682711
https://developer.apple.com/documentation/swiftui/text/init(verbatim:)
