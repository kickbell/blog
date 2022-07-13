## _1. 랜선을 젠더에 꽂아서 맥에 연결_

## _2. [ 시스템환경설정 ] - [ 네트워크 ]_
![](https://velog.velcdn.com/images/dev_kickbell/post/2147aeaa-3108-4be9-a18d-20c27869f2dc/image.png)

## _3. Wi-FI 끔 - USB 10/100LAN - [ 고급 ]_
아래 사진은 이미 설정이 되어있는 상태라 수동으로 설정되어 있지만, 연결 전이면 저렇게 나오지 않습니다. 
![](https://velog.velcdn.com/images/dev_kickbell/post/96e6db32-2604-43f3-a8d2-7be80fab2449/image.png)

## _4. [ IPv4 구성 ] - [ 수동 ] - [ TCP/IP ]_

![](https://velog.velcdn.com/images/dev_kickbell/post/4e7a51d8-bce3-47b6-a757-ea478fffe3fe/image.png)

사진에 나와있는 설정값을 아래에 나와있는(또는 담당자에게 받은) 내용대로 입력합니다. 

- _IPv4 구성: 수동_
- _IPv4 주소: 118.130.25.[ 개인 별로 주어진 자리번호 ]_
- _서브넷 마스크: 255.255.255.0 _
- _라우터(게이트웨이): 118.130.25.1_

IPv6 구성은 사용하지 않으므로 그냥 자동으로 두면 됩니다. 다 적었으면 일단 [ 확인 ]을 탭합니다. 

## _5. [ IPv4 구성 ] - [ 수동 ] - [ DNS ]_

다시 [ 고급 ]으로 들어와서 이번엔 DNS 탭으로 들어갑니다. 그리고 담당자로부터 제공받은 [ 기본 설정 DNS 서버 주소 ] 를 입력합니다. 보조 DNS 주소는 입력하지 않아도 괜찮습니다. 좌측 아래의 [ + ] 을 탭하고 그대로 입력해주면 됩니다. 그리고 다시 [ 확인 ]을 탭합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/85f81312-d30d-4be7-bbed-e1cf4feb9bd5/image.png)

## 6. _[ 적용 ]_

![](https://velog.velcdn.com/images/dev_kickbell/post/fcc90875-7b25-4284-b9d4-105f8eb3d179/image.png)

다시 이전 화면으로 돌아오면 아래 사진처럼 잡혀있어야 합니다. 그리고 우측 하단에 [ 적용 ] 을 탭하면 인터넷 상태 가 업데이트 된 것을 볼 수 있죠. 가끔 입력한 내용이 적용안되고 그냥 날아갈 수 있는데, 그때는 그냥 다시 시도하시면 됩니다. Wi-Fi가 끔인 것을 확인하세요. 

## 7. _속도테스트_ 

고정IP로 인터넷을 연결했으면, [fast.com](https://fast.com/) 에서 속도를 테스트해봅니다. 저같은 경우는 빠르면 900mb, 느려도 90mb 정도 나오는 것을 확인했습니다. 








