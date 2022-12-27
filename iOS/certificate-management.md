# 개발 인증서 관리하기 


## 1. 인증서 권한 부여/확인 

iOS 개발/배포를 하기 위해서는 개발용/배포용 인증서가 필요하다. 

그 첫번째로는 팀(계정 소유자)에게서 권한을 받아야 한다.

내 권한은 어떻게 확인하냐면, 아래에서 볼 수 있다. 
https://appstoreconnect.apple.com/access/users

부여받은 역할에 따라서 할 수 있는 권한이 다르다. 
아래 사진처럼 새로운 권한을 줄 사람을 추가하거나 기존 권한을 변경해줄 수 있다. 그러면 해당 사용자의 메일로 초대메일이 발송된다.

![](https://velog.velcdn.com/images/dev_kickbell/post/8d7b9df6-a448-423c-b61d-e82877844fe1/image.png)

권한별로 나와있는 자세한 역할은 [여기](https://developer.apple.com/kr/support/roles/)에서 보면 알 수 있다. 개발/배포 인증서 생성 및 취소라던지 프로비저닝 파일 권한 생성/삭제/다운로드 라던지의 역할이 구분되어 있다. 

## 2. 개발 인증서/프로비저닝 파일 

이제 내 계정의 권한이 생겼다. 그러면 인증서/프로지버닝 파일을 가지러 가보자. 아래 링크로 이동한다.  

https://developer.apple.com/account 

그리고 우상단의 Account 탭으로 이동하면 아래와 같은 화면을 볼 수 있다. 

![](https://images.velog.io/images/dev_kickbell/post/3e97cb57-86f2-4fd3-b2bd-41c6277e5a5e/image.png)

우리는 인증서를 작업할 것이므로 가운데 Certificates, Identifiers & Profiles로 이동한다. 
(유의할 점은 위에도 말했다시피, 내게 권한이 없다면 가운데 메뉴는 보이지 않고 나머지 2개의 메뉴만 보일 수 있다.) 

## 3. 인증서 생성 

인증서는 인증서 이름, 타입, 플랫폼, 만료기간 등의 정보를 가지고 있다. 또 여기 홈페이지에서도 만들 수 있고, XCode에서도 만들 수 있다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/0b23945d-2641-4d77-9509-e724ff1fcb70/image.png)

또 + 를 눌러보면 인증서를 선택할 수 있는 타입이 있는데, 푸시 관련한 인증서 같은 경우는 홈페이지에서만 만들 수 있는 것 같다. 개발/배포용은 XCode에서 바로 생성이 가능하다. 

생성은 소프트웨어 타입을 체크하고 다음을 누르면 CSR(Certificate Signing Request)를 업로드 하라고 하는데, 이건 구글링해봐야 한다. 나중에 찾아보자. 

![](https://images.velog.io/images/dev_kickbell/post/e4dfa459-b888-456b-afe3-7ca14668949e/image.png)
![](https://images.velog.io/images/dev_kickbell/post/d1c02dfa-1918-40fe-8f89-fd34dcb7fdc3/image.png)

XCode로 만드는 더 쉬운 방법이 있다. 
XCode에서 Accounts창을 열고 권한을 받은 내 메일을 클릭하고 팝업창 우하단에 Manage Certificates를 클릭한다. 

![](https://images.velog.io/images/dev_kickbell/post/a510e245-7e97-4519-8fb0-86768d7030e6/image.png)

그러면 현재 유효한 인증서와 그렇지 않은 인증서 리스트가 나온다. 

![](blob:https://velog.io/c715a962-8373-45b2-a910-6516103af638)

사실 나도 여러명이 개발을 하는 경우라면 무분별하게 인증서를 만드는 것은 좋지 않으니 기존에 있는 인증서를 쓰려고 했으나, private...궁시렁궁시렁하면서 새로 등록된 내 메일에 권한을 부여해야 한다고 나와서 그냥 새로 만들기로 했다. 

만드는 방법은 간단하다. + 버튼을 누르면 팝업창이 뜨는데 거기서 배포용인지 개발용인지 선택하면 된다. 홈페이지 보다는 훠얼씬 간단하다. 맥용도 있다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/7d70c7bb-6ece-4ac8-8f31-cc98f29062f5/image.png)

여기서는 배포용을 클릭했고, 그러고 나면 아래처럼 새로운 인증서가 생성되었다. 
실제로 아까의 홈페이지로 들어가보면 새로운 인증서가 추가된 것을 볼 수 있다. 기본 인증서 생성 만료일은 1년이다. 

## 4. 기기 등록/UDID  

인증서를 만들었으니 이제 기기등록을 해보자. 기기 등록을 하는 이유는 이제 인증서를 등록하고 실제 기기로 빌드/런 해보기 위함이다. 최대 100대까지 등록 가능하다.

기기 등록을 하기 위해서는 기기 고유의 IDENTIFIER인 UDID가 필요하다. 

> _**UDID(Unique Device Identifier)**_
iOS, tvOS, watchOS, macOS를 실행하는 Apple 기기의 기능이다.

UDID를 가져오기 위해서는 보통 아이튠즈인가 그거 연결해라 뭐해라 그러는데, 가끔 일하다보면 외부 협력처의 UDID를 등록해서 테스터로 넣어줘야 하는 경우가 있다. 그러나 아이폰를 주로 사용하시는 분이 아니라면 아이튠즈 같은 것들은 잘 모르는 경우가 많다. 

그래서 찾아보니 구글에 잘 설명이 되어있다. 아래 사이트를 활용하는 방법이다.

https://get.udid.io/

자세한 방법은 [여기](https://medium.com/@jang.wangsu/ios-iphone%EC%9D%98-udid-%EB%A5%BC-%ED%99%95%EC%9D%B8%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-a82ac8df93ea)에 친절히 설명되어 있어서 따로 추가하지 않았다. 

어찌됐건, 이제 UDID를 가져왔다. 

등록하는 방법은 간단하다. + 를 누르고 이름과 식별자를 입력해주면 끝이다.

![](https://images.velog.io/images/dev_kickbell/post/03ac2355-c9e1-476c-9bfa-6aa53f8051f8/image.png)

그러면 이렇게 리스트에 내 기기가 추가된다. 

![](https://images.velog.io/images/dev_kickbell/post/393780b3-0162-4ef5-a7ba-991dd48c62b8/image.png)

## 5. Profiles/Provisioning Profiles

프로비저닝 파일은 Certificates, Identifiers, Devices 내용을 묶어둔 파일이라고 한다. 

기기등록을 했으니 그것을 프로비저닝 프로파일에 추가해서 새로 다운받아야 한다. 

Profile 탭으로 들어간다. 그러면 거기에 인증서가 보인다. AD hoc, App Store, Development 이다. 

![](https://images.velog.io/images/dev_kickbell/post/6ee33907-c203-43a5-b6e4-6487cef46306/image.png)

App Store은 배포용, Development 개발용, AD hoc은 테스트용?이라고 해야 할까 여튼 비슷한 느낌이다. 

> _**Ad hoc, In-House**_
iOS에서 앱 배포 방식의 하나. Ad-hoc은 App developer에 기기 등록이 있는 상태에서 배포할 수 있고, 1년에 100개 제한에 매년 10만원을 지불해야한다.
In-House는 따로 기기 등록은 필요가 없지만, 매년 30만원 정도의 돈을 지불해야 사용할 수 있다. 배포할 수 있는 앱의 갯수는 제한되어 있지 않다.

어쨋건, 클릭해보면 우측에 다운로드, 삭제, 편집이 있는데 편집을 클릭해보자.  그러면 프로비저닝 파일에 등록된 기기의 리스트가 나온다. 나는 방금 새로 기기를 등록했기 때문에 기기가 체크되어 있지 않다. 새로 등록한 기기를 체크하고 저장한다. 

![](https://images.velog.io/images/dev_kickbell/post/2c92365a-e406-4687-b917-cc2f5bf02a9b/image.png)

같은 작업을 AD hoc, App Store, Development 모두 반복한다. 

유의할 점은 개발용 프로비저닝 파일을 수정할 때 인데, 아래 사진을 보면 기기리스트 위에 인증서 리스트가 존재한다. 

여기서 내가 생성한 리스트가 체크되어 있어야 정상적으로 프로젝트를 빌드할 수 있다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/cc4f5bcf-67cc-4a35-94bb-5f6607d4043e/image.png)

또 유의사항이 있다. 개발용 말고 다른 프로비저닝 파일을 편집할때도 왼쪽 아래 인증서에서 내가 생성한 인증서와 맞는 (2022년 11월 17일까지 만료기간) 애를 클릭해줘야 한다. 그렇지 않으면 빨간색 에러가 뜬다. 

![](https://images.velog.io/images/dev_kickbell/post/e75f3361-817a-4967-b1af-a7e6b4190593/image.png)

작업이 끝났으면 프로비저닝 파일을 모두 다운로드하고, 각자 더블클릭 해준다. 그리고 프로젝트를 실행해보면 에러 없이 프로비저닝 파일이 잘 적용된 것을 볼 수 있다. 

![](https://images.velog.io/images/dev_kickbell/post/3f080954-115b-45e5-813f-ca4579f8a41f/image.png)


## 5. 인증서 관련 용어 설명 
[여기](https://red-cherry-ring.tistory.com/12)에 잘 설명해 주신 것 같아 가져왔다. 



