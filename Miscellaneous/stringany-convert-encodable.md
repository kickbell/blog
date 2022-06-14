때는 바야흐로 얼마전, `SwiftUI + Firebase` 관련한 사내스터디를 진행하던 중 회원가입한 User의 데이터값을 Firebase로 보내야 하는 상황이었어요.

Firebase에서 요구되는 메소드의 파라미터 타입은 아래와 같은`[String: Any]` 였죠. 

```swift
  var userData: [String: Any] = [
    "email": email,
    "uid": uid,
    "profileImgURL": profileImgURL
  ]
```

그런데, 본능?적이게도 당연히 아래와 같이 바꾸고 싶었어요. 

```swift
  struct User {
    let email: String
    let uid: String
    let profileImgURL: String
  }
  
  var userData = User(email: email,
                      uid: uid,
                      profileImgURL: profileImgURL)
```

그리고 **_"그러면 User 구조체를 만들고 요구되는 타입이 `[String: Any]`이므로 Dictionary로 바꿔주면 되겠네."_** 하면서 동료가 하나의 코드를 첨부했죠. 

```swift
extension Encodable {
  func toDictionary() -> [String: Any]? {
    guard let object = try? JSONEncoder().encode(self) else { return nil }
    guard let dict = try? JSONSerialization.jsonObject(with: object) as? [String: Any] else { return nil }
    return dict
  }
}
```

그리고 사용은 이렇게요. 

```swift
struct User: Encodable {
  let email: String
  let uid: String
  let profileImgURL: String
}

userData.toDictionary()
```

그런데 여기서 의문이 들었습니다. 

"음.. 저거 꼭 Encodable에 확장해야 되나? 그냥 struct -> dictionary로 변환만 해주면 되는데 저렇게 해야 되나?" 라는 생각이 들었어요. 

뭔가 아무 생각없이 기계적으로 사용만 했어서 그런지, Codable은 서버에서 받아오는 json 값을 decode 하는데만 사용한다라는 인식이 있었던 것 같아요. 

그리고 한편으로는 `Codable = Decodable & Encodable` 인데 굳이 Encodable에 메소드를 확장하는 것은 뭔가 ISP(Interface Segregation Principle)를 위반하는 것 같기도 하고... 그런 생각이 들었습니다. 

그래서 대안을 찾아보기로 했지요. 

#### 1. 하드코딩 

```swift
struct User: Encodable {
  let email: String
  let uid: String
  let profileImgURL: String
  
  func toDictionary() -> [String: Any] {
    return ["email": email,
            "uid": uid,
            "profileImgURL": profileImgURL]
  }
}
```

음, 근데 이건 정상적으로 동작하나 확장성 또는 내부 프로퍼티의 변경에 취약하다는 문제가 있어요. 

#### 2. Mirror 

두번째 대안은 [Mirror](https://developer.apple.com/documentation/swift/mirror) 입니다. 저같은 경우는 iOS를 하면서 한번도 사용해보지 않은 구조체였어요. 

이 녀석의 용도에 대해 결론부터 말씀드리면, `init(reflecting:)`을 사용하면 타입에 있는 저장 프로퍼티, 튜플, 컬렉션, 활성 열거형의 값을 가져올 수 있습니다.  

```swift
struct User {
  let email: String
  let uid: String
  let profileImgURL: String
  
  let userArrays: [Int]
  let userTuple: (String, String)
  let phone: Device
  
  enum Device {
    case iPad
    case iPhone
  }
}


let user = User(email: "abc@gmail.com",
                uid: "abc",
                profileImgURL: "http://image.url",
                userArrays: [1,2,3,4,5],
                userTuple: ("t", "p"),
                phone: .iPad)

let mirror = Mirror(reflecting: user)

mirror.children.forEach {
  print($0)
}


/*
(label: Optional("email"), value: "abc@gmail.com")
(label: Optional("uid"), value: "abc")
(label: Optional("profileImgURL"), value: "http://image.url")
(label: Optional("userArrays"), value: [1, 2, 3, 4, 5])
(label: Optional("userTuple"), value: ("t", "p"))
(label: Optional("phone"), value: Note.User.Device.iPad)
*/
```

신기하죠?😄

상황에 따라서 요긴하게 써먹을 수 있을 것 같습니다. 

어쨋건 이 녀석을 사용하면, 이게 되겠죠. 

```swift
struct User {
  let email: String
  let uid: String
  let profileImgURL: String
  
  var asDictionary : [String:Any] {
    let mirror = Mirror(reflecting: self)
    let dict = Dictionary(uniqueKeysWithValues: mirror.children.lazy.map({ (label:String?, value:Any) -> (String, Any)? in
      guard let label = label else { return nil }
      return (label, value)
    }).compactMap { $0 })
    return dict
  }
}

print(user.asDictionary)
//["profileImgURL": "http://image.url", "uid": "abc", "email": "abc@gmail.com"]
```

음~ 근데 우리가 `asDictionary`를 `User`에만 사용할 건 아니잖아요? 어떤 protocol을 하나 만들고 그것을 확장해서 저 기능을 넣어야 하나? 고민을 좀 해봤습니다. 이렇게요. 

```swift
protocol Reflectable { }

extension Reflectable {
  var asDictionary : [String:Any] {
    let mirror = Mirror(reflecting: self)
    let dict = Dictionary(uniqueKeysWithValues: mirror.children.lazy.map({ (label:String?, value:Any) -> (String, Any)? in
      guard let label = label else { return nil }
      return (label, value)
    }).compactMap { $0 })
    return dict
  }
}

struct User: Reflectable {
  let email: String
  let uid: String
  let profileImgURL: String
}

print(user.asDictionary)
//["profileImgURL": "http://image.url", "uid": "abc", "email": "abc@gmail.com"]
```

동작은 같습니다. 꽤? 괜찮은 방법인 것 같아요.🥹


#### 3. NSCoding, NSKeyedArchiver

고전이 나왔습니다. 
일단 이 녀석들을 보기 전에 글 처음의 Encodable을 다시 보죠. 

```swift
extension Encodable {
  func toDictionary() -> [String: Any]? {
    guard let object = try? JSONEncoder().encode(self) else { return nil }
    guard let dict = try? JSONSerialization.jsonObject(with: object) as? [String: Any] else { return nil }
    return dict
  }
}
```

우리가 하고 싶은 건 `Strurt -> [String: Any]` 입니다. 그런데 위 코드에서는 작업을 나눠서 진행해주고 있지요. 

1. Object를 Data 타입으로 변환 
2. Data 타입을 JSONSerialization을 통해 Foundation 객체로 변환 후 [String: Any]로 타입캐스팅  

기존에 1,2 번에서는 Struct를 Data 타입으로 변환하진 않았었죠. 그 부분이 약간 다릅니다. 

그런데 저기서 Encodable에 Extension을 하는 이유가 바로 아래에 `Object를 Data 타입으로 변환`하기 위해서 `JSONEncoder()` 클래스가 쓰이기 때문이겠죠. 우리는 이 부분을 `JSONEncoder()`을 사용하지 않고, `NSKeyedArchiver()`를 사용하는 대안을 살펴볼 겁니다. 

[NSKeyedArchiver](https://developer.apple.com/documentation/foundation/nskeyedarchiver)는 추상 클래스인 [NSCoder](https://developer.apple.com/documentation/foundation/nscoder)를 기반으로 한 클래스 입니다. 공식 문서 상으로는 `키값을 가지고 아카이브에 객체로 저장되는 인코더`라고 표현하고 있네요. 

비슷한 녀석으로 [NSArchiver](https://developer.apple.com/documentation/foundation/nsarchiver)가 있는데 차이점은 키값의 유무 정도인 것 같아요. 이미 Deprecated 되었기 때문에 이제는 사용하지 않습니다. 

`NSKeyedArchiver`는 iOS 2.0부터 사용되었고, Swift 3까지는 원활히 사용되다가 Codable이 나온 Swift 4, iOS 8.0 이후로는 잘 사용되고 있지 않습니다. 

그래도 어찌됐건 역할은 인코더의 역할을 하고 있어서 위의 코드를 사용한다면 아래처럼 될 수 있겠지요. 

```swift
protocol NSCoderEncodable { }

extension NSCoderEncodable {
  func toDictionary() -> [String: Any]? {
    guard let object = try? NSKeyedArchiver.archivedData(withRootObject: self, requiringSecureCoding: false) else { return nil }
    guard let dict = try? JSONSerialization.jsonObject(with: object) as? [String: Any] else { return nil }
    return dict
  }
}

struct User: NSCoderEncodable {
  let email: String
  let uid: String
  let profileImgURL: String
}

let user = User(email: "abc@gmail.com",
                uid: "abc",
                profileImgURL: "http://image.url")
                                
print(user.toDictionary())
//["profileImgURL": "http://image.url", "uid": "abc", "email": "abc@gmail.com"]
```

엄청나게 크게 바뀌는 부분은 없습니다. 단지 `Object를 Data 타입으로 변환`하기 위해서 `JSONEncoder()` 대신에 `NSKeyedArchiver`를 사용했을 뿐이죠. 

아 그리고, 이미 워낙에 자주 쓰여서 아시겠지만 [JSONSerialization](https://developer.apple.com/documentation/foundation/jsonserialization)는 Codable로 치면 디코더의 역할을 하는 녀석입니다. iOS 5.0 부터 사용되었고 8.0에 Codable이 나오기 전까지 잘 사용되었죠. 지금도 간간히 사용되고 있구요. 


#### 4. Encodable

돌고 돌아 다시 Encodable로 와버렸습니다. 튜닝의 끝은 순정이랬던가요. 결국에 전 다시 이쪽으로 돌아와버렸어요. 

```swift 
extension Encodable {
  func toDictionary() -> [String: Any]? {
    guard let object = try? JSONEncoder().encode(self) else { return nil }
    guard let dict = try? JSONSerialization.jsonObject(with: object) as? [String: Any] else { return nil }
    return dict
  }
}
```

결론적으로 이 방법은 나쁘지 않은 방법인 것 같습니다. 

1번도 있고, 2번도 있고, 3번도 있지만 변화에 유연하고 확장성에도 대응하기 좋죠. 따로 프로토콜을 정의할 필요도 없구요. 

겸사겸사 정리를 해보자면, 이 정도가 되겠네요.

- NSCoder : 인코딩, 디코딩에 관련한 추상클래스 
- NSKeyedArchiver : NSCoder를 상속받아 실제로 인코딩, 디코딩에 관한 기능을 지원하는 클래스
- JSONSerialization : Data 또는 json 타입을 Foundation 객체로 변환하는 기능을 지원하는 클래스
- Encodable : 인코딩과 관련한 프로토콜
- Decodable : 디코딩과 관련한 프로토콜
- Codable : Decodable & Encodable
- JSONEncoder : Object 타입을 Data 타입으로 변환하는 기능을 지원하는 클래스
- JSONDecoder : Data 또는 json 타입을 Object 타입으로 변환하는 기능을 지원하는 클래스 

결론은 삽질?..을 한 것이라고 볼 수도 있겠습니다만은.. 알고 쓰는거랑 모르고 쓰는 건 차이가 있겠죠? :) 

그걸로 오늘의 위안을 삼아봅니다. 20000.


#### 5. 참고
- https://ios-development.tistory.com/720 
- https://developer.apple.com/documentation/foundation/archives_and_serialization/encoding_and_decoding_custom_types
- https://developer.apple.com/documentation/swift/mirror
- https://medium.com/@OutOfBedlam/%EC%8A%A4%EC%9C%84%ED%94%84%ED%8A%B8-json-encoder%EC%99%80-encodable-e61e55f9e535
