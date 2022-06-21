업무를 하다보면, 일정이나 또는 특정 기능에 따라서 네이티브 앱이 아닌 하이브리드 앱을 개발해야 하는 경우도 많이 있죠. 그러면 프론트 개발자의 요청이나 또는 개인의 필요에 따라 쿠키/세션 정보를 확인해서 디버깅해야 하는 경우도 있는데요. 오늘은 사파리를 통해서 웹의 세션/쿠키정보를 확인하는 방법에 대해서 적어볼까 합니다. 

저같은 경우는 주로 하이브리드 앱을 개발할 때, 브릿지가 연결되어있는데 내가 보내주는 토큰 값이나 설정 값들이 정확히 전달되고 있는지 확인하기 위해 주로 디버깅 용도로 사용하고 있어요. 물론 코드로도 당연히 확인은 가능합니다. 보통 아래처럼 확인하죠. 

```swift
    WKWebsiteDataStore.default().httpCookieStore.getAllCookies({ (cookies) in
        for cookie in cookies{
        	print("WKWebsiteDataStore cookie : \(cookie.name) // \(cookie.value)")
        }
    })
```

하지만 Safari를 이용해서 더 쉽게 보는 방법도 있습니다.😄


## 1. Safari 설정하기

먼저 `Safari` -> `환경설정` 으로 들어갑니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/1d92d987-061f-455c-a9b2-b26b1dda0d3e/image.png)

그리고 `고급` 탭으로 가서 `메뉴 막대에서 개발자용 메뉴 보기`를 체크해주세요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/a7f13f33-7afd-4d45-b0ad-3e99c6f1035a/image.png)

## 2. Device 설정하기

이제 Device 설정을 해야해요. 

`설정` -> `Safari` -> `고급`으로 들어갑니다. 그러면 아래 사진처럼 `JavaScript` 스위치가 있는데, 이것을 활성화 시켜줍니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/a4dcd112-2e32-493f-9451-5205a2361069/image.png)

시뮬레이터 말고, 실제 Device의 경우에는 아래처럼 `웹 속성`이라는 스위치도 있는데 이것도 같이 활성화 시켜주면 되요. 여기까지 하면 설정은 모두 끝입니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/efc490dd-371d-4ceb-a9ca-301984cafd19/image.png)

## 3. 실행해서 확인하기 

이제 직접 실행해서 확인해보면 되는데요. Device 또는 시뮬레이터에서 하이브리드 앱 화면으로 이동합니다. 

그리고 `Safari` -> `개발자용` 메뉴를 클릭하면 다음과 같이 메뉴가 나와있어요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/33de2c6e-4043-47a8-9fa2-f28de554e8a2/image.png)

지금 보이는 부분은 `검사할 수 있는 응용 프로그램 없음` 이라고 나와있죠? 이건 제가 지금 하이브리드 앱을실행하지 않은 상태라서 그래요. 

만약에 Device 또는 시뮬레이터에서 웹뷰를 켜고 다시 같은 메뉴로 이동해보면 아래처럼 나옵니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/ee20890a-2f2b-4ecf-b364-9187c00fd7ae/image.png)

지금은 제가 `home-test.geniet.io` 라는 도메인에 접속해있는 것이죠. 그리고 해당 도메인에 들어가보면 아래와 같은 화면을 볼 수 있어요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/a642b71a-1d2d-472b-8b6b-b23adb428dce/image.png)

그리고 여기서 쿠키/세션 정보를 확인할 수 있습니다. 여기서 디버깅을 하고, 프론트 개발자와 커뮤니케이션을 하거나 으쌰으쌰해서 다시 열심히 개발하시면 되겠죠. 😄




