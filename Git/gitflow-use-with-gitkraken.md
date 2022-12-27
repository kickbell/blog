# GitKraken으로 Git-flow 활용하기 

`GitKraken`은 기본적으로 `Git-flow 브랜치 전략`을 제공합니다. 
![](https://images.velog.io/images/dev_kickbell/post/988a8a49-70c1-47db-85eb-c95ab86cb659/image.png)

먼저 우측 상단 설정버튼을 클릭합니다.
![](https://images.velog.io/images/dev_kickbell/post/9de3a60f-f71d-4688-a6d7-62ca134b9f6c/image.png)

그러면 설정화면이 나와요. 
설정에서 하단으로 스크롤해서 사이드 메뉴중에 `GitFlow` 탭 해봅시다.
![](https://images.velog.io/images/dev_kickbell/post/576abb41-4ca6-4d2e-a7d9-93285732cd42/image.png)

그럼 기본적으로 원격에 사용되는 `master`, `develop` 브랜치와 로컬에서 사용되는 `feature`, `release`, `hotfix` 가 입력되어 있습니다. 

특별히 따로 지정할 것이 없으니 아래 `Initialize Gitflow` 버튼을 눌러줍니다. 

> _에러가 나는 경우가 있는데, 나같은 경우는 로컬에 `develop` 브랜치 하나만 있어서 에러가 났다. 로컬 아래`REMOTE` 메뉴에서 `master` 브랜치를 더블탭 해줘서 로컬에도 똑같이 `develop`,`master`브랜치가 생기고 다시 버튼을 눌러주니 정상동작 했다._

### 1. Feature 브랜치 새로 생성해서 작업하기  

자, 이제 뭔가 작업을 시작할거다. 

`GitFlow` 브랜치 전략에서 `master` 브랜치는 QA가 완전히 끝나고 정상적으로 배포가능한 버전에서만 사용하니 제외하고 `develop` 브랜치를 더블탭해서 체크아웃 한다. 

그리고 새로생긴 `GITFLOW` 라는 메뉴 우측 끝에 마우스를 올리니 초록색 버튼이 활성화 된다. 눌러보자.

![](https://images.velog.io/images/dev_kickbell/post/bafb5061-1534-4c66-93d6-3f989bc8e220/image.png)

그러면 메뉴가 나오는데, 우리는 지금 1.4.4 버전의 토픽브랜치를 새로 만들어서 작업해줅거니까 `Feature`를 클릭한다. 

![](https://images.velog.io/images/dev_kickbell/post/24830ef7-67c8-44fe-9a32-9e6ae240f4b8/image.png)

그리고 원하는 이름을 적고 `Start Feature` 버튼을 클릭해주자. 

![](https://images.velog.io/images/dev_kickbell/post/1f6b4194-f315-4946-aa16-62bb4210b26b/image.png)

그러면 1.4.4 라는 이름으로 브랜치가 생성되었고, 체크 아웃 되어있는 것을 볼 수가 있다. 

이제 뭔가 작업을 해줘야 한다. 

일단은 간단하게 README.md 파일을 열고 일부 수정해주자. 그리고 커밋. 

![](https://images.velog.io/images/dev_kickbell/post/1f1b9b34-096f-4c9b-80f3-7d15fd0f73bc/image.png)

작업이 끝났다고 치자. 그러면 `feature/1.4.4` 브랜치는 필요가 없어졌으니 삭제하고, 해당 브랜치를 `develop` 브랜치에 병합해주는 작업을 진행해야 한다. 
`GitKraken`에서는 이걸 아주 쉽고 직관적으로 해주는 메뉴를 지원한다. `feature/1.4.4` 를 우클릭하고 `Finish feature/1.4.4` 를 클릭해준다.

![](https://images.velog.io/images/dev_kickbell/post/891f2d99-a471-455c-91c3-53d19d9f5cb4/image.png)

그러면 아래 사진처럼 브랜치를 삭제할건지, 리베이스 할건지 선택이 나온다. 삭제하겠다. 

![](https://images.velog.io/images/dev_kickbell/post/82f7b21b-5386-46d8-b2f8-e93b05ab67a0/image.png)

그러면 로컬, 깃플로우에서도 자동적으로 `feature/1.4.4` 브랜치가 삭제되고 다시 `develop` 브랜치로 체크아웃이 되어있는 것을 볼 수 있다. 

또한, GRAPH를 보면 `feature/1.4.4`에서 `develop` 브랜치로 머지가 된 것도 확인할 수 있다.

![](https://images.velog.io/images/dev_kickbell/post/7aa2b181-2f7e-44e5-9b11-a4a825e13a1b/image.png)

### 2. 작업이 끝났으니 Release 브랜치 생성해서 QA넘기기 

자, 1.4.4 버전의 기능이 완성됐다. 이제 이것을 QA로 넘겨야 한다. 

git flow대로 develop에서 release 브랜치를 하나 생성하자.

1번과 똑같이 GITFLOW 에서 우측 버튼을 클릭하고 같은 방법으로 release 브랜치를 생성해준다. 

> _release, hotfix는 관례적으로 브랜치명 앞에 release-, hotfix- 의 접미사를 붙여준다._

![](https://images.velog.io/images/dev_kickbell/post/dbf2a79a-78ba-4f5d-a1d9-8678008f26e1/image.png)

그러면 아래 사진처럼 브랜치가 생성된다. 

![](https://images.velog.io/images/dev_kickbell/post/6b04e207-f57e-479c-aff0-3694d5cc91d9/image.png)

자 그러면 이제 QA팀에 앱을 넘겨줘야 할 것이다. 각자의 방식으로 firebase든, testflight든 배포를 했다. 

여기서 git flow 라면 2가지 선택지가 나온다. 

1. 버그가 나오지 않은 경우 
2. 버그가 발생한 경우 

`버그가 나오지 않은 경우`라면 gitflow 대로 작업하던 release 브랜치는 삭제하고 해당 브랜치를 각각 master, develop 브랜치에 병합해주면 된다. 

`버그가 발생한 경우`라면, release 브랜치 내에서 버그를 수정해주고 release 브랜치 내에서 커밋을 한 후에 다시QA 를 요청한다. 그리고 QA가 끝나면, 위와 똑같이 release 브랜치는 삭제하고 해당 브랜치를 각각 master, develop 브랜치에 병합해주면 된다. 

사실상 버그가 나오지 않은 경우는 버그가 발생한 경우와 작업 수정하는 부분을 제외하고 동일하므로 지금은 버그가 발생했다고 가정해보겠다. 

버그를 수정하자. 역시나 또 README.md 를 수정했다. 

![](https://images.velog.io/images/dev_kickbell/post/eb4e8481-30eb-45a7-9d88-689256978832/image.png)

그리고 커밋. 

![](https://images.velog.io/images/dev_kickbell/post/81319f3c-cf2c-4e9a-a5ba-a62492990059/image.png)

다시 QA를 넘겨야겠지? 넘겼다. 그리고 버그가 발견되지 않았다. 

이제 심사요청을 하면 되겠다. 그러면 위에서 말한대로 release 브랜치는 삭제하고 해당 브랜치를 각각 master, develop 브랜치에 병합해주면 된다.  

> _릴리즈 브랜치에서는 릴리즈를 위한 최종적인 버그 수정 등의 개발을 수행합니다. 모든 준비를 마치고 배포 가능한 상태가 되면 'master' 브랜치로 병합시키고, 병합한 커밋에 릴리즈 번호 태그를 추가합니다.
릴리즈 브랜치에서 기능을 점검하며 발견한 버그 수정 사항은 'develop' 브랜치에도 적용해 주어야 합니다. 그러므로 배포 완료 후 'develop' 브랜치에 대해서도 병합 작업을 수행합니다._

그러나, 깃크라켄을 사용하면 이 까먹을 수 있는 작업을 한방에 해준다. 

다시 아까처럼 릴리즈 브랜치에가서 우클릭 하고 Finish 브랜치를 클릭해준다. 

![](https://images.velog.io/images/dev_kickbell/post/263f9ea3-3366-4fb8-939c-f643242fdf03/image.png)


그러면 아까와 유사한 포맷으로 메뉴가 나온다. 

뭐겠나? 충분히 추측가능하겠죠? gitflow는 master브랜치에 병합할때마다 새로운 버전 예를들어 1.4.4 -> 1.4.5 와 같은 식으로 버전을 태깅해주므로 그 태그 번호를 입력하라는 거다. 

![](https://images.velog.io/images/dev_kickbell/post/9e74af4b-42ba-48ea-8566-cbed8d7893c0/image.png)

그리고 finish를 해주면 release-1.4.4 브랜치는 사라지고 각각 master, feature 브랜치에 병합이 된 것을 볼 수 있다. 


### 3. hotfix를 생성하고 수정해주기 

심사가 끝나고 정상 배포했는데 bug가 발견되었다. 슬프지만 hotfix 브랜치를 생성하고 수정해줘야 한다. 

하는 방법은 똑같다. 다른 점은 hotfix이므로 develop이 아닌 master에서 브랜치를 생성해야 된다는 것이다. 

master 브랜치로 체크아웃을 해주고 똑같은 방법으로 hotfix 브랜치를 생성한다. 

아까처럼 hotfix 또한 통상적으로 hotfix- 라는 접미사를 붙이기 때문에 hotfix-1.4.4 라고 붙여주겠다. 

![](https://images.velog.io/images/dev_kickbell/post/b70d01e5-586d-4142-8a47-6fe5b07d5cd6/image.png)

지금은 master 브랜치로 체크아웃 한 다음, 진행해주긴 했는데 아마 깃크라켄이 똑똑?해서 develop 브랜치에서 진행해도 자동으로 되는 것 같기도 하다. 

어쨋건 hotfix 브랜치를 생성하니, 해당 브랜치로 자동으로 체크아웃이 되었다. 

![](https://images.velog.io/images/dev_kickbell/post/5bd4e803-107d-4dc1-b4c9-572241b1b79f/image.png)

여기서 이제 다시 버그를 수정하는 작업을 진행해준다. 이것도 2번과 작업은 동일하다. 

지금은 버그가 있어서 당연히 hotfix 를 진행하는 거니까 2번의 `버그가 발생한 경우`에 해당된다. 

버그를 고쳐주는 작업을 진행해주고, 브랜치에서 바로 커밋을 해준다. 

![](https://images.velog.io/images/dev_kickbell/post/f5846744-2775-4681-b306-67ca97843a04/image.png)
![](https://images.velog.io/images/dev_kickbell/post/e7423208-12d8-439e-973e-7192e016dd6d/image.png)

그리고 이제 버그를 수정했으니 QA를 넘겨야 된다. 잠시후... QA가 통과했다! 

이제 다시 master와 병합하고 배포해야 한다. 그리고 버그가 수정되었으니 develop 에도 병합해줘야한다. 

git flow의 이점이 여기서도 나온다. 즉 master 에서 앱스토어에 배포를 했는데 버그가 나왔다. 근데 그 사이에 당연히 놀고있진 않았을거니까 develop 에서 새로운 브랜치를 따던지 해서 작업을 해줬을 거다. 근데? 그렇게 작업을 해준 부분이 이전에 배포나간것의 버전의 일부를 건드려서 side effect가 날수도 있는거지. 그래서 hotfix라는 브랜치를 새로 만드는 거다. 그러면 안전하잖아. hotfix는 master에서 만든거니까. 만약에 겹치는 부분이있어도 develop과 병합할때 컨플릭이 나겠지. 그럼 그거 잘 봐서 해결해주면 된다. 

> _예를 들어 'develop' 브랜치에서 개발을 한창 진행하고 있는 도중에 이전에 배포한 소스코드에 아주 큰 버그가 발견되는 경우를 생각해 보세요. 문제가 되는 부분을 빠르게 수정해서 안정적으로 다시 배포해야 하는 상황입니다. 'develop' 브랜치에서 문제가 되는 부분을 수정하여 배포 가능한 버전을 만들기에는 시간도 많이 소요되고 안정성을 보장하기도 어렵습니다. 그렇기 때문에 바로 배포가 가능한 'master' 브랜치에서 직접 브랜치를 만들어 필요한 부분 만을 수정한 후 다시 'master'브랜치에 병합하여 이를 배포하려고 하는 것이죠.
이 때 만든 핫픽스 브랜치에서의 변경 사항은 'develop' 브랜치에도 병합하여 문제가 되는 부분을 처리해 주어야 합니다._

어쨋건, 이제 다시 병합할 차례다. 똑같이 우클릭 후 Finish 브랜치를 탭해준다. 

![](https://images.velog.io/images/dev_kickbell/post/1904765c-fa06-4d84-8baf-1bf1d4941c0a/image.png)

그러면 마스터에 병합되는 것이므로 아까처럼 tag message를 입력하라고 나온다. 입력하고 finish hotfix를 클릭해준다. 

그러면 hotfix 브랜치는 삭제되고 master, develop 브랜치와 병합된다. 

원래는 일일히 다해줘야되는 작업을 깃크라켄이 뚝딱뚝딱해준다. 태그도 달리고. 

겁나좋다 ㅠㅠ 



