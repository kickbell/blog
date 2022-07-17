보통 Github API는 개인 공부, 과제, 포트폴리오를 만들면서 많이 만나곤 하죠. 저도 같은 이유로 오랜만에 Github API를 사용해보게 되었는데요. 사용 중에 제법 익숙한? 에러를 만나게 되었어요😄. 

```swift
{
    "message": "API rate limit exceeded for 118.217.73.245. (But here's the good news: Authenticated requests get a higher rate limit. Check out the documentation for more details.)",
    "documentation_url": "https://docs.github.com/rest/overview/resources-in-the-rest-api#rate-limiting"
}
```

저같은 경우는 레포지토리 검색 API를 사용하다가 발생했는데요. 생각해보니 예전에 iOS 개발자로 취업을 준비할 때에도 만났었던 것 같아요. 그때는 워낙에 허접이라..(지금도 사실 크게 다를 건 없지만 😂) 그냥 "이거 왜 안되지?" 하고 넘어갔었던 것 같은데, 생각보다 별 게 아니니 간략하게나마 정리해볼까 합니다. 

일단, 결론적으로 저런 에러가 발생하는 이유는 저희가 인증을 받지 않고 API를 사용하고 있기 때문입니다. 글의 제목 그대로 API를 제공하는 Github 측에서 제한을 걸어놓은 것인데요. 사용자 별로 제한을 두어 전체 API의 사용성을 높이기 위해서 입니다.

예를 들어, 가장 많이 사용되는 API 중에 하나인 [레포지토리 검색 API](https://docs.github.com/en/rest/search) 같은 경우만 해도 `GET` 방식으로 아무 제한 없이 이용할 수 있다보니 브라우저의 주소창에 `https://api.github.com/search/repositories?q=hello world` 라고만 입력해도 hello world 와 관련된 레포지토리들에 대한 정보를 얻을 수 있죠. 장점은 아무런 인증없이 사용할 수 있다보니 API에 대한 `접근성`이 높다는 겁니다. 사용하기 편하죠. 쉽고. 하지만 아무런 인증을 받지 않은 사용자가 무분별하게 API를 사용할 수 있으니 어찌보면 당연히 제한을 두는게 좋겠죠? 저것도 다 돈이니까요. 또한, 적절하게 제한을 둔다면 전체 API의 환경이나 사용성도 높아질 겁니다. 

깃허브 홈페이지에서는 인증되지 않은 사용자에게는 시간당 60개, 인증된 사용자에게는 시간당 5,000개라고 명시가 되어있는데요. 사실 이건 API마다 다른 것 같아요. 제가 사용했던 검색 API 같은 경우는 인증되지 않은 사용자에게 분당 10개의 요청이 허용되었어요. 이정도면 상황에 따라서는 쓰기 힘든 수준일 수 있어요. 검색같은 경우만 하더라도 ( throttle/debounce 같은 기능이 구현되지 않았다면 ) 검색되는 글자가 바뀔 때마다 API를 요청하는 경우도 있으니까요. 일단 hello world만 10자다보니 이거 입력하면 1분간은 다른 검색 못하는 그런 상황인 겁니다..😄? 

반면에, 검색 API에서 인증된 사용자에게는 분당 30개의 요청이 허용됩니다. 아무래도 인증 받아야겠죠. 인증을 받으려면 Github에서 PAT( personal access token )를 발급 받으면 되는데요. 이것에 대한 링크는 [여기](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)에 걸어두겠습니다. 

PAT를 발급받았다면 이제 인증받았을 때와 인증받지 않았을 때를 비교해보면 좋겠죠. 이 글에서는 상대적으로 보기 쉬운 [Postman](https://www.postman.com/)으로 먼저 보여드리고, 코드를 따로 첨부할게요. 

## 인증받지 않은 사용자의 경우 

인증받지 않은 사용자의 경우는 결과값이 아래 이미지와 같습니다. 일단 GET 방식에 쿼리 파라미터로 q = "hello world" 값이 들어간 것을 볼 수 있죠. 상태값도 200으로 잘 내려왔구요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/aca67971-25eb-4bb8-b624-2063796e2f21/image.png)		

이번엔 같은 결과의 헤더값이 뭐가 들어갔는지와 응답 헤더값도 봐볼까요 ? 

![](https://velog.velcdn.com/images/dev_kickbell/post/b1abe4d8-93fd-4712-8afe-efc834c9165e/image.png)	

일단 헤더값에는 따로 아무 것도 넣어주지 않았기 때문에 비어있습니다. 다만 응답 헤더값은 잘 봐야할 부분이 있는데요. 바로 중간 아래쯤에 있는 `X-RateLimit-` 관련 녀석들이에요. 이 녀석들은 말그대로 RateLimit 관련 정보가 담겨옵니다. 아까 제가 인증받지 않은 사용자는 분당 10회 요청이 허용된다고 했죠? 그래서 `X-RateLimit-Limit` 값이 10 인 겁니다. `Remaining`은 10회 중 9회가 남았다는 거겠죠? `Reset`은 무슨 id값 같은 것 같고 `Resource`는 요청된 API 이름을 말하는 것 같아요. 그리고 `Used`는 총 10회 중 몇 회를 사용했냐를 말하는 것이겠네요. 몇 번 더 사용해보고 다시 결과를 봐볼까요 ?  
```swift
X-RateLimit-Limit : 10
X-RateLimit-Remaining : 6
X-RateLimit-Reset : 1658072791
X-RateLimit-Resource : search
X-RateLimit-Used : 4
```
몇 번 더 요청을 해보니 결과값이 위처럼 나오네요. 그리고 대망의 10회가 다 되었을 때는 당연하게도 Remaining이 0, Used이 10으로 나오고 상태값도 `403 rate limit exceeded` 라고 들어오게 됩니다. 

## 인증받은 사용자의 경우 
이번에는 인증을 받은 경우입니다. 인증을 받은 경우에는 발급받은 PAT를 헤더 값에 아래와 같이 넣어주면 됩니다. token과 PAT 사이에는 공백이 하나 필요해요. 이런 방법이 저도 정확히는 모르겠는데 Github에서 [RFC2617에 정의된 기본 인증 방법](https://www.ietf.org/rfc/rfc2617.txt)을 따르고 있기 때문인 것 같아요. 

```swift
Authorization = token "발급받은 PAT"
```

어쨋건, 다른 값들은 그대로 두고 헤더 값만 변경해서 다시 요청하면 결과는 아래와 같습니다. 쿼리 파라미터와 바디 값은 동일할테니 바로 헤더필드와 응답된 헤더값을 볼게요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/b644b771-236d-452a-88cc-9786da76c691/image.png)				

헤더필드에 Authorization 값이 들어갔고, 값으로 token "발급받은 PAT"이 들어갔죠?( 제 개인 토큰 값이라 따로 입력하진 않았어요. ) 그리고 중요한 건 아래 응답된 헤더값이겠죠. 

```swift
X-RateLimit-Limit : 30
X-RateLimit-Remaining : 29
X-RateLimit-Reset : 1658073470
X-RateLimit-Resource : search
X-RateLimit-Used : 1
```

아까 검색 API 같은 경우 인증된 사용자에게는 분당 30회라고 말씀드렸죠. 정상적으로 Limit가 늘어난 것을 볼 수 있습니다.


## 인증받은 사용자의 경우(Code)

코드도 간단합니다. 리퀘스트를 만들고 아까 말씀드린 것처럼 헤더필드에 Authorization 값으로 token 발급받은PAT를 넣어주면 되지요. 그리고 여기서는 응답 헤더값을 확인하기 위해 `httpResponse.allHeaderFields`를 출력해주고 있어요. 

```swift
private func searchRepositories(_ q: String, completion: @escaping ([Repository]) -> ()) {
    guard let url = URL(string: "https://api.github.com/search/repositories?q=\(q)") else {
        return print("invalid url...")
    }
    
    var request = URLRequest(url: url)
    request.setValue("token 발급받은PAT", forHTTPHeaderField: "Authorization")
    
    URLSession.shared.dataTask(with: request) { data, response, error in
        guard error == nil,
              let httpResponse = response as? HTTPURLResponse,
              let data = data,
              let json = try? JSONSerialization.jsonObject(with: data) as? [String: Any] else { return }
        
        guard let items = json["items"] as? [[String:Any]] else {
            self.exampleError(json)
            return
        }

        print(httpResponse.allHeaderFields.forEach { print($0) })
        
        let result = items.map {
            Repository(fullName: $0["full_name"] as? String ?? "",
                       htmlUrl: $0["html_url"] as? String ?? "")
        }
        
        completion(result)
        
    }.resume()
}
```

응답된 헤더의 출력 값을 볼까요 ? dict 타입이기 때문에 순서가 없지만, postman에서 본 것과 같은 값들이 출력된 것을 볼 수 있어요. limit error의 값은 403이니 에러 코드 중에 403만 따로 처리해주는 방법을 쓰는 것도 괜찮을 것 같습니다. 

```swift
//(key: AnyHashable("Date"), value: Sun, 17 Jul 2022 16:03:01 GMT)
(key: AnyHashable("x-ratelimit-used"), value: 1)
//(key: AnyHashable("x-content-type-options"), value: nosniff)
//(key: AnyHashable("content-security-policy"), value: default-src 'none')
//(key: AnyHashable("Content-Type"), value: application/json; charset=utf-8)
//(key: AnyHashable("Content-Encoding"), value: gzip)
//(key: AnyHashable("referrer-policy"), value: origin-when-cross-origin, strict-origin-when-cross-origin)
//(key: AnyHashable("Link"), value: <https://api.github.com/search/repositories?q=H&page=2>; rel="next", <https://api.github.com/search/repositories?q=H&page=34>; rel="last")
//(key: AnyHashable("x-github-media-type"), value: github.v3; format=json)
//(key: AnyHashable("Accept-Ranges"), value: bytes)
//(key: AnyHashable("access-control-expose-headers"), value: ETag, Link, Location, Retry-After, X-GitHub-OTP, X-RateLimit-Limit, X-RateLimit-Remaining, X-RateLimit-Used, X-RateLimit-Resource, X-RateLimit-Reset, X-OAuth-Scopes, X-Accepted-OAuth-Scopes, X-Poll-Interval, X-GitHub-Media-Type, X-GitHub-SSO, X-GitHub-Request-Id, Deprecation, Sunset)
//(key: AnyHashable("Server"), value: GitHub.com)
//(key: AnyHashable("Strict-Transport-Security"), value: max-age=31536000; includeSubdomains; preload)
(key: AnyHashable("x-ratelimit-remaining"), value: 29)
//(key: AnyHashable("Access-Control-Allow-Origin"), value: *)
(key: AnyHashable("x-ratelimit-limit"), value: 30)
//(key: AnyHashable("Vary"), value: Accept, Accept-Encoding, Accept, X-Requested-With)
//(key: AnyHashable("x-github-request-id"), value: EC0A:52E9:121407:146061:62D432B4)
//(key: AnyHashable("x-xss-protection"), value: 0)
//(key: AnyHashable("Cache-Control"), value: no-cache)
//(key: AnyHashable("x-frame-options"), value: deny)
(key: AnyHashable("x-ratelimit-reset"), value: 1658074335)
(key: AnyHashable("x-ratelimit-resource"), value: search)
```



## Reference
[https://docs.github.com/en/rest/search#rate-limit](https://docs.github.com/en/rest/search#rate-limit)			    
        
[https://docs.github.com/en/rest/overview/resources-in-the-rest-api#checking-your-rate-limit-status](https://docs.github.com/en/rest/overview/resources-in-the-rest-api#checking-your-rate-limit-status)							    
          
[https://docs.github.com/en/rest/rate-limit](https://docs.github.com/en/rest/rate-limit)								  
