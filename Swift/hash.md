swift hash 알고리즘 사용법 

그냥 값. 

"asdf".hash //Int

https://stackoverflow.com/questions/25388747/sha256-in-swift

![image](https://user-images.githubusercontent.com/85085822/201869077-a626d79f-e4e2-4cde-811e-59f7a86888b6.png)

다이제스트란 해시의 출력을 나타내는 타입이다.
https://developer.apple.com/documentation/cryptokit/digest
해시를 통과한 이후의 데이터를 다이제스트 라고 부른다.



SHA256 

```swift
import CommonCrypto

func sha256(data : Data) -> Data {
    var hash = [UInt8](repeating: 0,  count: Int(CC_SHA256_DIGEST_LENGTH))
    data.withUnsafeBytes {
        _ = CC_SHA256($0.baseAddress, CC_LONG(data.count), &hash)
    }
    return Data(hash)
}

let s = readLine()!
let data = s.data(using: .utf8)!
let shaData = sha256(data: data)
let result = shaData.map { String(format:"%02x", $0) }.joined()
print(result)


//iOS 13 After
import CryptoKit

let s = readLine()!
let data = s.data(using: .utf8)
let digest = SHA256.hash(data: data!)
let digestString = digest.map { String(format: "%02x", $0) }.joined() //16진수를 숫자로 바꾸는듯?
print(digestString)

```
