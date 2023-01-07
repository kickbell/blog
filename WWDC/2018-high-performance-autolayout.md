# High Performance Auto Layout


> _이 글은 [WWDC 2018 - High Performance Auto Layout](https://developer.apple.com/videos/play/wwdc2018/220)의 내용을 공부하고, 개인적으로 정리해놓은 글입니다._


## 1. 내면과 직관
- 단순하게 오토레이아웃으로 Button과의 거리를 20으로 설정한다면 이것이 어떤 과정을 통해서 적용되는지 이해하기 어렵습니다. 설정한 오토레이아웃으로 인한 성능의 기대치가 뭐가 더 좋은 것인지도 판단하기 어렵죠. 그래서 이번 세션의 목표는 오토레이아웃이 어떻게 동작하는지에 대한 내면과 직관을 기르는 것입니다. 
- iOS12에서는 오토레이아웃 관련한 성능이 많이 향상되었습니다. 그렇게 할 수 있었던 이유는 오토레이아웃이 어떻게 구성되고 작동하는지, 어떻게 작동하는지에 대한 좋은 정신적 모델을 가지고 있기 때문이라고 생각합니다.
- 우리는 당신이 그 정신적 모델을 개발하는 것을 돕고 싶고, 그래서 가장 일반적이라고 생각되는 문제들을 선택했습니다. 

## 2. Render Loop
- 우리는 아래의 사진처럼 심플 레이아웃을 구현할 겁니다. 그리고 인터페이스 빌드가 아닌 코드로 작성했다고 해보죠. 아래의 코드처럼요.
- 아래의 코드는 지금 총 5개의 작업을 하고 있습니다. 
    - updateConstraints 라는 메서드를 재정의(override)
    - myConstraints라는 제약조건모음을 비활성화하고 삭제 
    - myConstraints에 새로운 제약조건 생성 
    - myConstraints의 제약조건을 활성화
    - super.updateConstraints 호출 
- 그러면 이 5개의 작업에서 잘못된게 뭘까요? 그러려면 각 코드의 역할이 무엇인지 알아야 될 겁니다. 하나하나씩 자세히 살펴보죠.

![](https://velog.velcdn.com/images/dev_kickbell/post/62a2f5a1-5280-4f6d-a92c-51c40d472e2b/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/d4d1d1ff-bbe3-4448-968a-c7e1bf2d35d9/image.png)


### Render Loop
- 렌더 루프는 잠재적으로 초당 120번 실행되는 프로세스입니다.
- 렌더 루프가 완료되면 모든 콘텐츠가 각 프레임에 사용할 준비가 되는 것이죠. 
- Update Constraints, Layout, Display라는 3개의 단계로 구성되어 있습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/fdad5671-b68a-420f-87b0-46d1bd035c77/image.png)

-  Update Constraints
    - 첫번째로, 모든 뷰는 updateConstraints를 수신합니다. 뷰는 View Hierarchy에서 보면 알 수 있듯이 스택구조로 겹겹이 쌓이죠? 그래서 이 updateConstraints 메서드는 ¹leaf를 시작으로 스택의 제일 첫번째인 window까지 역방향으로 호출됩니다. 
- Layout
    - 두번째로, 모든 뷰는 이번엔 window부터 leaf까지 순방향으로 호출되면서 layoutSubviews()를 수신합니다. 
- Display
    - 세번째로, 모든 뷰는 마찬가지로 window부터 leaf까지 필요하다면, draw 합니다.
    
    
### 3단계 메서드 자세히 살펴보기 

- 이 3단계 작업은 궁극적으로 작업을 낭비하지 않기위해 존재합니다. 낭비되는 작업을 피하고, 또는 그 작업을 연기하고, 건너뛰기 위한 것입니다.
- 무슨 말이냐면, 어떤 UILabel이 있다고 합시다. 그러면 그 레이블에는 텍스트의 크기를 설명하는 제약 조건이 있어야 합니다. 그리고 그 크기에 기여하는 많은 속성이 있습니다. 텍스트 속성, 글꼴, 텍스트 크기 등이 있습니다.
- 그리고 이 제약 조건을 측정하는 방법 중의 하나는 이러한 속성 중 하나가 변경될 때마다 텍스트를 다시 측정하는 것입니다. 하지만 매우 비효율적입니다.
- 레이블을 처음 설정할 때 아마도 이러한 속성 설정자를 여러 개 호출할 것이고 각각의 텍스트를 다시 측정하는 경우 모든 중간 항목이 낭비되기 때문이죠.
- 대신에 우리가 할 수 있는 것은 설정된 글꼴 내에서 setNeedsUpdateConstraints를 호출하는 겁니다. 그러면, 프레임이 스크린으로 이동하기 전에 끝에서 updateConstraints을 호출하게 됩니다. 초당 120번씩 updateConstraints를 실행하지 않고 마지막에만 딱 1번 호출하는 것이죠.
- 추가적으로 설명하면, 위에서 설명한 3가지 메소드말고도 밑에 더 있죠? setNeeds-, -IfNeeded 가 있는데 이런 녀석들은 글자 그대로의 뜻입니다. 
- setNeeds- 는 위에서 말했던 것처럼, 이것을 실행하면 초당 120번씩 실행하지 않고 스크린으로 돌아가기 전에 딱 1번만 호출됩니다.
- -IfNeeded 는 반대로 당장 필요하다는 겁니다. 뭐가? updateConstraints(), layoutSubviews()가요. 저 메소드가 실행되는 즉시 updateConstraintsIfNeeded()라면 updateConstraints()가 호출될 것이고, layoutIfNeeded()라면 layoutSubviews()가 호출되겠죠.

![](https://velog.velcdn.com/images/dev_kickbell/post/6f4b8de8-e77d-4dbe-ae1f-12917f631d5f/image.png)


### 다시 코드로 돌아와서 
- 이제 다시 코드를 볼까요. 뭔가 이상하지 않나요? 
- updateConstraints 메서드 내에서 우리는 지금 myConstraints을 비활성화하고, 다시 myConstraints에 새로운 제약조건을 생성하고, 그것을 활성화합니다.
- 방금 위에서 말한 텍스트의 사례를 보는 것 같지 않나요?  
- 이것은 마치 layoutSubviews()가 호출될 때마다 모든 Subview를 파괴하고, 처음부터 만든 다음 다시 추가하는 것과 똑같습니다. 첫번째 그림처럼 말이죠. 
- 코드를 어떻게 개선해야 할까요? 계속 말했듯이 한 번 이상 하지 말아야 합니다. nil 체크 한번 이면 됩니다. 두번째 그림처럼요. 이런 것들은 우리가 코딩하면서 볼 수 있는 가장 일반적인 오류입니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/a5e4b87e-fb26-4571-a36e-a87f71bf85c4/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/aed1ab34-8022-4d54-9f45-82b0c8786260/image.png)

### 결론 
- 렌더 루프는 실제로 필요한 경우에는 유용합니다. 특히, 중복 작업을 피하는 데 정말 유용합니다. 하지만 초당 120회나 실행되기 때문에 위험하기도 합니다. 매우 민감한 코드이죠. 
- 따라서 이와 같은 경우 일반적으로 민감한 코드에 대해 수행하려는 작업을 작성하는 경우 주의를 기울여야 하지만, 가장 좋은 방법은 역시 민감한 코드를 작성하는 빈도를 최소화해야 합니다. 
- 즉, 위에서 개선한 코드처럼 딱 한번만 실행될 수 있도록 잘 생각해야 합니다. 그리고 이를 수행하는 좋은 방법은 Interface Builder를 사용하는 것입니다. 
- 애초에 Interface Builder를 사용하면 비활성화하고 제거하고 다시 활성화하고 그런 작업이 불가능하기 때문이죠. 사용할 수 있는 상황이라면 사용해야 합니다. 


## 3. Engine 
- 오토레이아웃 프로세스에 대해 더 자세히 살펴보겠습니다. 실제로 제약 조건을 활성화할 때, 그리고 제약 조건을 추가할 때 발생하는 프로세스 말이죠. 
- 프로세스 별로 그림과 같이 최대한 자세하게 설명해볼게요. 

### Constraint가 추가되면, 방정식(Equation)을 만들고 엔진에 추가
- 아래의 그림이 간략한 다이어그램 입니다. View에서 제약 조건을 추가한다면 이 View는 Window에 달려있죠. 그리고 Window에 매달려 있는 것이 Engine이라는 내부의 개체입니다. Engine은 Auto Layout의 컴퓨팅 코어이죠. 그리고 이제 다음으로 Constraint가 추가되면, Constraint에 해당하는 방정식(Equation)을 만들고 그것을 엔진에 추가합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/47bb8dac-f232-4463-a43c-71375cc5096f/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/0fd44f7c-bbcc-4de8-bb02-ab3fd71ac4e3/image.png)

- 방정식(Equation)이 무엇일까요? 아래의 그림과 같은 것들입니다. 그리고 여기서 이해해야할 것은 방정식이 변수들로 이루어졌다는 것입니다. 
- 이 경우에서 변수는 View의 frame 데이터입니다. 최소한 4개겠죠. minX, minY, width, height 이렇게요. 시작좌표와 너비, 높이만 있으면 최소한 그릴 수 있는 조건은 달성할테니까요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/f1522e51-7375-4140-9ec7-1924a2c31311/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/3801a1ae-03a2-4f31-838c-63bf4354694d/image.png)

- 그리고 나서 이제 엔진에서 연산을 시작할 겁니다. 첫번째는 text.minX가 들어옵니다(8이 아니라 20). 다음으로 text1.width = 100이 들어오고, 이어서 text2.minX가 들어옵니다.  
- 그런데 여기서 최적화가 일어납니다. 바로 text1.minX를 8로 text1.width를 100으로 대체하는 것이죠. 128이 됩니다. 마지막으로 text2.width = 100이 들어오고 계산이 끝이납니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/ee3cc6c2-b4c2-459a-b387-7dee4f1696e8/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/a716716f-be64-40ff-b9fd-c47cd9a7ee03/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/25afe3bd-f6ea-45f9-8997-cb60041753c3/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/b93e7e4a-f707-4d88-bfcd-f5d364064cdc/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/f9513efd-04d2-4fb6-8059-0be1f2558aaa/image.png)



### Engine에 Variable이 할당될 때마다 setNeedLayout 호출
- 그런 다음에는 UIView가 레이아웃을 수신하고 Subview가 할 일은 엔진에서 프레임으로 해당 데이터를 복사하는 것입니다.
- 지금 방정식에 text1.minX = 8 같은식으로 할당이 되잖아요. 그러면 minX 이라는 변수에 값이 변했으니 엔진은 뷰에게 변수의 값이 바뀌었다고 알려줍니다. 
- 그러면 뷰는 엔진이 값을 알려줬으니 해당 뷰의 superview를 호출하고 해당 하위 뷰의 setCenter에서 setBounds를 호출합니다.
- 이게 프로세스의 끝입니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/f501cd58-0214-4e09-8cc1-abf5f98806d9/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/c4615054-225d-4e71-bae0-ac8975f77e48/image.png)

### 완성된 다이어그램
![](https://velog.velcdn.com/images/dev_kickbell/post/c843d29a-61d1-48f3-b1ae-2f642bce1dd3/image.png)

### 결론 
- 프로세스를 다 살펴보았습니다. 이제 배운 것을 기반으로 아까의 코드를 다시 보죠. 
- 만약에 우리의 코드가 저 상태라면 엔진은 아래의 gif처럼 계속 비활성화 -> 제거 -> 방정식 새로 계산 -> 활성화 -> 뷰에 프레임 넘기기를 초당 120회씩 반복하고 말겁니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/d4d1d1ff-bbe3-4448-968a-c7e1bf2d35d9/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/2f23bd39-afc5-47cd-bf62-7dd24cd36edd/image.gif)


## 4. Local vs Global 레이아웃

### 단순한 것이 가장 빠른 것
- 정말 좋은 성능을 원한다면 오토레이아웃으로 사용한 만큼만 비용을 지불하면 됩니다. 이게 무슨 말이냐면, 아래 그림과 같은 겁니다.
- 레이블이 2개있고 그걸 감싸는 슈퍼뷰가 있는 UI가 있다고 해보죠. 여기서 text3이 text1의 leading으로 정렬을 해버렸습니다. 
- 보통 이렇게 많이 하죠? 근데, 만약에 저렇게 오토레이아웃을 잡지 않고 각각 잡았다면 어떨까요? 사람들은 왠지 그렇게하면 성능이 느려질 것 같다고 생각합니다. 
- 하지만, 실제로는 다릅니다. 두번째 사진을 보면 엔진에서는 top view와 bottom view의 방정식이 각각 따로 계산됩니다. 독립적인 방정식이 2개 있는거죠. 
- 이렇게 되면 1개의 방정식의 계산시간이 2.5초라면 2개면 5초고 4개면 10초가 되는 겁니다. 얻을 수 있는 최고의 선형 성능(linear time)을 볼 수 있겠죠. 
- 하지만, 만약에 이렇게 하지 않았다면 bottom view는 top view를 기다려야 합니다. 독립적으로 계산할 수 없죠. text1의 leading으로 정렬해놓았으니까요. 단순한 것이 가장 빠른 겁니다.

![](https://velog.velcdn.com/images/dev_kickbell/post/1ae121e6-1b37-441e-a50b-db1b9aaa82e4/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/15f229d1-7865-4e64-b5d8-bb273b48c916/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/2cd31167-1662-40f7-8f3c-54e212fc3d69/image.png)


### 자연스럽게 사용하세요
- 아래의 첫번째 그림을 보죠. 오토레이아웃이 뭐가 많죠? 저건 사실 2개를 겹쳐놓은 겁니다.  유사한 레이아웃이니 나름대로 뭔가 최적화를 해서 하나의 뷰만 사용하도록 한 것이죠. 저것을 원래대로 나누면 두번째 그림이 되는 겁니다. 
- 그건 좋은 생각이 아닙니다. 이렇게 해버리면 엄청나게 많은 종속성을 생성합니다. 디버깅도 엄청나게 어렵죠. 그냥 자연스럽게 사용하면 됩니다. 성능도 개발자가 직접 이해하는 것에도 더 좋습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/2f9395e7-cf59-4c61-94d1-0cf96fe065f9/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/25f2ea61-7614-4a24-b867-3c5b04778854/image.png)

### 추가적인 기능들 
- Inequality
    - 일부 뷰에 대해서는 width >= 100 을 표현할 수도 있습니다. 인터페이스 빌더에서는 greater/less then equal 같은 거겠죠. 이 기능의 비용은 width == 100과 비교하면 아주 저렴합니다.
- NSLayoutConstraint set constant 
    - 또 NSLayoutConstraint.constant = 100 과 같이 해줄 수도 있습니다. IBOutlet으로도 되죠? 실제로 제스쳐 레코나이저에서 드래그를 할 때마다 x,y 좌표가 변경되면 저런식으로 .constant 에 넣어줘서 변경이 되는 구조입니다. 
- Priority 
    - 아까 뷰의 너비가 이상적으로는 width == 100 인 것이 좋다고 해볼게요. 상대적으로 == 가 더 비용이 많이 든다고 했었죠. 
    - 이럴 때, == 에서 priority를 주면 이것은 오류를 최소화하는 작업입니다. 
    - 그러니까 이상적으로는 100이지만, 100+약간의 오차값이 있어도 같다고 하는거죠. 이런식으로 오류를 최소화 하는 겁니다. 


## 5. 효율적인 레이아웃 그리기 
- 이번에는 이론적인 이야기보다는 실제 사례로 예시를 들어보겠습니다. 
- 이것은 소셜 미디어 타입의 앱입니다. 테이블뷰 셀로 구성되어 있구요. 아바타가 있고, 공유 중인 사람을 보여주기 위한 아바타가 하나 더 있습니다. 공유 중인 사람이 없다면 보이지 않겠죠. 
- 레이블로는 제목, 날짜, 본문이 있으며 본문은 아래의 그림과 같이 길이가 유동적일 수 있습니다. 또 사진을 공유하는 기능도 있는데요. 공유 아바타와 마찬가지로 당연히 사진도 있을 수도 있고, 없을 수도 있습니다. 
- 자, 그러면 이 앱을 가장 효율적인 레이아웃으로 만들어 보도록 하죠. 문제가 뭘까요? 지금 이 앱의 문제는 보통 소셜미디어 앱들이 그렇지만 굉장히 빠른 스크롤링을 요구합니다. 하지만 지금 이 앱을 빠르게 스크롤링 할 경우, 심한 버벅거림(hiccups)가 있죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/a1be37f6-4049-4721-bb0b-d73a8bf25fc9/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/5edb9114-4a90-4322-85bb-37d2cc29b0b8/image.png)


### 인스트루먼츠로 문제 찾기
- 일단 이 인스트루먼츠는 바로 위의 앱에서 스크롤링을 했을 때의 데이터입니다. 그리고 인스트루먼츠에서 주황색 상자가 있는 부분이 CPU 사용량입니다. 여기에 변동 크게 있으면 레이아웃 쪽에서 문제가 있을 확률이 높죠. 
- 같은 칸 바로 밑의 보라색 막대는 제약조건 churning이 발생하는 뷰의 개수에 해당합니다. 뭔가 막대가 되게 길죠. 
- 이 부분의 상세보기를 확인해보면 두번째, 세번째 사진과 같습니다. 여기서는 어떤 것들에 대해서 영향을 받는지 관련된 뷰의 목록이 표시됩니다. UITableViewCellContentView 안에 4개의 뷰가 있고 그 4개의 뷰인 AvaterView, Title label, Date label, Log entry label이 있습니다. 여기서 뭔가 이슈가 있나봐요.  
- 그리고 아래 항목을 보면 Add, Remove, Change Constraint가 보이는데요. 막대 그래프를 보면 Add도 높고, Remove도 높고, Add와 Remove가 끝날즈음 Change가 높죠? 이거 뭔가 익숙하지 않나요? 
- 그렇습니다. 아까 우리가 했던 것하고 똑같아요. UpdateConstraints 메소드가 계속 재정의되고 있었던 거죠. 제약조건을 삭제하고, 다시 생성하고, 활성화하고, 다시 삭제하고, 생성하고... 네번째 gif처럼 말이죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/84746d50-6719-4785-b160-a63a181992b0/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/74405040-535f-4361-9619-ba612e8e1f53/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/0b99c180-c24c-4778-a1ad-1db4d0bbd580/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/678e3d96-7719-4433-abfa-ca6952caf7e4/image.gif)


### 문제 해결하기 1 (setHidden)
- 일단 문제를 찾았으니 하나씩 해결해보죠. 안없애고 다시 하려면 어떻게 해야할까요? 
- 일단 왼쪽의 아바타뷰를 볼게요. 얘는 잘생각해보면, 공유 중인 사람이 있으면 보여주고 그렇지 않으면 안보여주면 되는거겠죠? 
- 그러면 굳이 지웠다가 새로 생성해줄 필요가 없습니다. 공유 중인 사람이 있으면 보여주고, 없으면 숨겨주면 되죠. 
- setHidden은 제약조건을 제거하는 대신에 뷰를 숨겼다가 보여줬다가 할 수 있는 매우, 매우, 매우 저렴한 방법입니다.

### 문제 해결하기 2 (imageView)
- 이제 이미지뷰에 대해서 이야기해보겠습니다. 제약 조건을 잘 보면 아래 초록색과 주황색으로 나뉠 수 있습니다. 
- 초록색은 변하지 않는 제약조건입니다. 항상 있죠. 그렇죠? 그리고 주황색은 선택적입니다. 이미지가 있으면 주황색이 있고 없으면 본문 레이블과 전체 컨텐츠뷰의 거리의 제약조건만 존재하겠죠. 
- 이럴 때는 어떻게 해야 제약조건을 다시 삭제, 생성하지 않을 수 있을까요 ? 
- 답은 생각보다 간단합니다. 제약조건을 2개 만들어버립니다. 이미지가 있을 때의 경우의 제약조건과 없을 때의 제약조건 2개를 설정하는 겁니다. 
- 그리고 제약조건을 담을 imageContraints와 noImageContraints 배열을 만들고 해당 제약조건을 넣습니다.
- 만약에 이미지가 있다면, noImageContraints를 비활성화하고 imageContraints을 활성화하면 되겠죠? 반대로 이미지가 없다면 imageContraints를 비활성화하고 noImageContraints를 활성화하면 될 것입니다. 
				
![](https://velog.velcdn.com/images/dev_kickbell/post/01887968-59c3-4d40-8183-d96ee8ea851f/image.png)

 
### Intrinsic Content Size
- 모든 뷰가 Intrinsic Size를 갖는 것은 아닙니다. 여기서도 UILabel, UIImageView만 그렇죠. 
- 이미지뷰는 이미지 크기를 사용해서 고유 콘텐츠 크기를 측정하고, 레이블은 텍스트를 사용해서 고유 콘텐츠 크기를 측정합니다. 
- 보통은 intrinsicContentSize를 오버라이딩을 하지 않지만, 오버라이딩을 하면 성능에 도움되는 상황도 있습니다. 텍스트 측정이 비용이 크기 때문이죠. 
- 만약에 텍스트에 집약적인 앱이 있고 UILabel의 텍스트 측정에서 많은 시간이 발생하는 경우라고 해보죠. 이럴 때, 추가 정보가 있는 경우에는 도움을 받을 수 있습니다.
- 무슨 말이냐면, 텍스트 측정을 하지 않고도 텍스트에 필요한 크기를 알고 있는 경우가 있을겁니다. 텍스트의 높이는 뭐다라고 height 제약조건을 줘버리는거죠. 이런 경우라면 텍스트를 측정할 필요가 없잖아요? 
- 그러면 아래의 코드처럼 intrinsicContentSize를 오버라이드합니다. 이 말은 내 부모뷰에게 "이봐, 나는 이미 내 사이즈를 가지고 있으니 텍스트 측정을 하지 않아도 된다"라고 말하는 거겠죠.
- 이 방법은 직접 텍스트 측정을 하지 않는 경우에만 동작하지만, 일부 앱에서는 성능을 개선하는데 도움을 줄 수 있겠죠. 
- 예를 들어, 이런 경우면 뭐 그런 느낌이겠죠? 레이블이 최대 3줄이고 그걸 넘어가니까 Label...으로 끝나는 느낌이랄까. 
			
![](https://velog.velcdn.com/images/dev_kickbell/post/b7d4b6c7-ab09-442d-82f6-3111943a6ed7/image.png)




### systemLayoutSizeFitting(_ targetSize:)
- 사람들은 intrinsicContentSize와  System Layout Size Fitting Size를 자주 혼동합니다. 
- intrinsicContentSize는 엔진에 넣을 사이즈 정보를 전달하는 방법입니다.
- System Layout Size Fitting Size는 엔진에서 사이즈 정보를 다시 가져오는 방법입니다. 
- 완전 정반대이죠. 
- systemLayoutSizeFitting은 오토레이아웃을 사용하여 하위 뷰를 관리하는 뷰에서 프레임 정보가 필요한 때, 혼합 레이아웃에서 사용됩니다.
- 자주 사용하진 않지만, 생각보다는 비용이 비쌉니다. 이 메서드를 호출할 때마다 엔진이 생성되고 폐기됩니다. 작은 용도로는 괜찮지만 많이 사용하는 경우엔 호출할 때 주의해야 합니다.
- 자주하는 실수 중 하나는 self-sizing cell에서 콘텐츠 뷰로 호출을 전달하는 것입니다. 그렇게 하면 실제로 스크롤을 만들기 위해 만든 일부 최적화를 재정의하고 해당 뷰에서 더 빠르게 스크롤하고 엔진을 추가하게 됩니다. 따라서 현재 그렇게 하고 있고 스크롤이 좋지 않다면 확인해봐야 합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/b3a7b54b-14c1-48f1-9fd6-bcf5b8e63bdc/image.png)

### Unsatisfiable Constraints 
- 이런 경우가 있습니다. 하나의 뷰인데 width = 50, width = 200을 설정한 경우죠. 
- 그러면 아래와 유사한 에러가 콘솔에 띄워집니다. 이것은 때때로 성능에 직접적인 영향을 미칠 수 있으며 다른 문제를 숨길 수도 있습니다.

```swift
Unable to simultaneously satisfy constraints.
Probably at least one of the constraints in the following list is one you don't want. 
Try this: 
    (1) look at each constraint and try to figure out which you don't expect; 
    (2) find the code that added the unwanted constraint or constraints and fix it. 
(
"<NSLayoutConstraint:0x282ae4ff0 UIView:0x109f11110.bottom == UIView:0x109f13240.bottom   (active)>",
"<NSLayoutConstraint:0x282ae8050 UIView:0x109f11110.bottom == UIView:0x109f13240.bottom - 291   (active)>"
 )
```

### 정리하기 
- 모든 제약조건을 삭제하지 마세요. 
- 공통적으로 적용되는 제약조건이 있는 경우 한번만 추가하세요. 
- 변경이 필요한 제약조건만 변경하세요. 
- 제약조건을 삭제하기보다는 숨기는게 낫습니다. 
- Intrinsic Content Size는 대체로 오버라이드하지 않지만, 텍스트의 크기가 고정되어 있어 텍스트 특정이 필요하지 않은 경우에만 오버라이드 합니다. 
- intrinsicContentSize와 systemLayoutSizeFitting는 정반대입니다.
- systemLayoutSizeFitting를 호출할 때마다 엔진이 생성되고 폐기되므로 작은 용도로는 괜찮지만, 많이 사용하는 경우엔 주의해야 합니다. (특히 테이블/컬렉션뷰의 self-sizing cell의 경우)
- 정상동작한다고 해서 같은 뷰에 width 제약 조건을 2개씩 넣는다던지 하면 안됩니다. 성능에도 영향이 있고, 다른 문제를 숨길 수도 있습니다. 


## Reference

[https://developer.apple.com/videos/play/wwdc2018/220/](https://developer.apple.com/videos/play/wwdc2018/220/)

## Endnotes 

### ¹leaf
- 사전 그대로의 뜻은 나뭇잎의 잎이지만, 프로그래밍 적으로는 트리tree 자료구조에서 자식이 없는 노드. 즉, 제일 하단의 노드를 말한다. 
- 여기서는 뷰 계층이 스택구조로 밑에서부터 차근차근 쌓이므로 제일 마지막인 최상단 뷰를 뜻한다. 


### ²방정식(Equation)
- 방정식(方程式, 영어: equation)은 미지수가 포함된 식에서 그 미지수에 특정한 값을 주었을 때만 성립하는 등식이다. 이때, 방정식을 참이 되게 하는(성립하게 하는) 특정 문자의 값을 해 또는 근이라 한다.
- x^2 - 5x + 6 = 0 은 방정식이고, 해는 2와 3이다. 

