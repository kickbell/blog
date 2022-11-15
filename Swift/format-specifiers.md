

https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Strings/Articles/formatSpecifiers.html

뭐하다가 발견했냐면. 

해시값 하다가.  발견. 

02x는 뭐지?. 뭐이렇게. 


```swift
import Foundation
import CryptoKit

// CryptoKit.Digest utils
extension Digest {
    var bytes: [UInt8] { Array(makeIterator()) }
    var data: Data { Data(bytes) }

    var hexStr: String {
        bytes.map { String(format: "%02X", $0) }.joined()
    }
}

func example() {
    guard let data = "hello world".data(using: .utf8) else { return }
    let digest = SHA256.hash(data: data)
    print(digest.data) // 32 bytes
    print(digest.hexStr) // B94D27B9934D3E08A52E52D7DA7DABFAC484EFE37A5380EE9088F7ACE2EFCDE9
}
```

