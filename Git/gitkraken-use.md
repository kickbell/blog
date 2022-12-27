# GitKraken(깃크라켄) 활용하기

깃크라켄은 상대적으로 러닝커브가 높은 CLI를 사용해서 Git을 활용하는 방법을 예쁘장하고 편리한 GUI를 통해 좀 더 편하게 사용하게 해주는 툴입니다. 

유사한 툴로 [SourceTree](https://www.sourcetreeapp.com/) 가 있다. 차이점은 소스트리는 완전 무료이지만, 깃크라켄은 사용할 수 있는 기능에 따라서 무료/유료 버전이 따로 있다. 

그래도 아래와 같이 왠간한 건 다 된다. 더 정확하게 알고 싶다면 [여기](https://www.gitkraken.com/pricing)를 클릭해서 보도록 하자. 
          
![](https://images.velog.io/images/dev_kickbell/post/fe6acb21-61d6-4c6d-9833-f70404fd94b8/image.png)          

        

## 1. commit --amend

`amend 개정하다, 수정하다 `

커밋하면서 `amend`라는 옵션을 클릭하면 새로운 커밋을 생성하지 않고, 기존의 커밋을 수정하게 된다. 		

####

> 🤔 수정 전

![](https://images.velog.io/images/dev_kickbell/post/061f47f5-6566-4382-9852-9f812d1df6fe/image.png)
> 1. 내부파일 sample.txt 내용 변경 
> 2. 새롭게 수정된 커밋인 WIP(work in progress) 클릭 
> 3. `Stage all changes` 클릭 
> 4. `Amend` 옵션 체크(커밋메시지를 변경하고 싶다면 변경해도 됨)
> 5. `Amend Previous Commit` 클릭 

![](https://images.velog.io/images/dev_kickbell/post/8ca5285f-9d73-4dbb-a80a-ef850b39128c/image.png)![](https://images.velog.io/images/dev_kickbell/post/172ef3c4-f356-482f-a16e-60ec21b99225/image.png)

> 😱 수정 후 

![](https://images.velog.io/images/dev_kickbell/post/fb4a953b-47a0-4501-8ef3-b3e904202ab7/image.png)

새로운 커밋이 추가되지 않고 기존의 커밋이 수정된 것을 볼 수 있다. 


## 2. revert

`revert 되돌아가다`

기존 커밋을 그대로 두고, 기존 커밋의 수정 내용을 제외한 새로운 커밋을 생성합니다. 

> 🤔 수정 전

![](https://images.velog.io/images/dev_kickbell/post/9c208c37-c851-4d3b-aaf8-79b874589b8a/image.png)

> 1. 지우고 싶은 커밋 우클릭 `Revert commit`클릭 
> 2. Yes

![](https://images.velog.io/images/dev_kickbell/post/a71a1c76-13cc-4807-a6bb-2f5a62657f41/image.png)![](https://images.velog.io/images/dev_kickbell/post/d1df7aab-0364-43a0-be32-a031a858ff27/image.png)

> 😱 수정 후 

![](https://images.velog.io/images/dev_kickbell/post/dc63e692-ec70-4f08-8d05-ff7fdf96a58a/image.png)![](https://images.velog.io/images/dev_kickbell/post/798d0de8-5153-4963-915a-1f21678eb624/image.png)

특이한 점은 revert는 그런겁니다.  
기존에 지울 커밋이 `sample.txt` 파일에 `pull: 원격 저장소의 내용을 가져오기`라는 내용을 추가하는 커밋이었다면 revert는 아예 해당 커밋을 실행하지 않은 상태로 되돌아가는 느낌이라기보다는 기존의 커밋 기록은 그대로 두고 해당 커밋 내용을 삭제한 새로운 커밋을 만들어 생성합니다.  

## 3. reset

`reset 다시 제자리에 놓다, 다시 맞추다`

기존의 커밋을 지웁니다.

> 🤔 수정 전

![](https://images.velog.io/images/dev_kickbell/post/0a595924-ae22-497a-8ef5-d74d4d924c5b/image.png)

> 1. 2개의 커밋을 지우고 싶다면 3번째의 커밋으로 가서 클릭 
> 2. 마우스 우클릭 후 `Reset [브랜치명] to this commit`  
> 3. Reset 옵션 선택 후 클릭 

![](https://images.velog.io/images/dev_kickbell/post/8c367c5c-a3c1-46f9-b86e-21537225c800/image.png)


> 😱 수정 후

![](https://images.velog.io/images/dev_kickbell/post/5f11e2a4-1d14-498a-8a00-92c7cc1d0622/image.png)

위의 `revert`와는 다르게 `reset` 기존 커밋 기록도 같이 지워버린다. 
깃크라켄에서는 실수로 `reset`하는 것을 방지하기 위해 `Undo`와 `Redo`버튼을 제공합니다. 그래서 원래 터미널에서 `$ git reset --hard ORIG_HEAD` 와 같은 식으로 reset 실행 전의 상태로 되돌리는 것보다 훠얼씬 단순하게 `되돌리기/다시하기`를 할 수 있습니다. 

> reset option
- 커밋만 되돌리고 싶을 때 (soft)
- 변경한 인덱스의 상태를 원래대로 되돌리고 싶을 때 (mixed)
- 최근의 커밋을 완전히 버리고 이전의 상태로 되돌리고 싶을 때 (hard)        
          
![](https://images.velog.io/images/dev_kickbell/post/a7b45992-3ce4-4ac3-937c-f34832178d99/image.png)            


## 4. cherry-pick

`cherry-pick 선별하다`

다른 브랜치에 있는 커밋을 나의 브랜치로 가져와서 붙입니다. 

> 🤔 수정 전

![](https://images.velog.io/images/dev_kickbell/post/82087dee-495e-4430-9599-dd85fce8e7bb/image.png)

> 1. 커밋을 가져갈 브랜치(issue1)에서 커밋을 가져와서 넣을 브랜치(master)로 `checkout`
> 2. 센터 화면의 `GRAPH` 메뉴에서 내가 가져오고 싶은 브랜치의 커밋을 클릭 
> 3. 우클릭 후, `Chrry pick commit` 클릭 
> 4. Yes
> 5. `conflicted 발생`, 충돌이 발생하는 파일을 열어서 충돌하는 부분을 수정하고 저장한다 
> 6. 우측 `mark all resolved` 모두 해결된 것으로 표시 버튼 클릭 
> 7. `Commit changes to 1 file` 클릭(커밋 메시지 변경도 가능)

![](https://images.velog.io/images/dev_kickbell/post/50408929-3054-42e6-8df1-86c27b56f448/image.png)![](https://images.velog.io/images/dev_kickbell/post/2d3894ce-ce74-41d1-a3f4-13f4b7681c91/image.png)![](https://images.velog.io/images/dev_kickbell/post/a73a2ccf-eb25-47c7-9594-25c23ace3c7b/image.png)

> 😱 수정 후

![](https://images.velog.io/images/dev_kickbell/post/499bae75-3073-41a9-a20b-6c148e3e6926/image.png)

## 5. rebase -i 로 커밋 모두 통합하기

터미널 명령은 `git rebase -i 수정을 시작할 커밋의 이전 커밋` 이지만, 여기서 i는 `interative(대화형, 상호작용의)`의 약자이다. 왜 대화형인지는 아래에서 볼 수 있다. 

기능을 한 줄로 정의하자면 커밋을 합치는데 여러 옵션을 사용해서 커밋을 통합하는 기능이라고 볼 수 있겠다. 


> 🤔 수정 전

![](https://images.velog.io/images/dev_kickbell/post/d4238965-a8d4-4c23-9902-ed2e06c20b50/image.png)

> 1. `pull 설명을 추가` 커밋과 `commit의 설명추가` 커밋을 합칠 것이므로 그 이전커밋인 `add의 설명추가` 커밋을 클릭한다.
> 2. 우클릭 후, `Interative rebase 2 children of-` 를 클릭한다. 
> 3. 새로운 창이 생기면 `pull의 설명을 추가` 옵션을 `Squash`로 선택하고 `Start Rebase` 를 클릭한다. 

![](https://images.velog.io/images/dev_kickbell/post/8b804bf3-6aaa-40c9-be31-c5bba779c4ee/image.png)![](https://images.velog.io/images/dev_kickbell/post/2a986323-d10a-4d3c-ab9a-2018bbb32b97/image.png)![](https://images.velog.io/images/dev_kickbell/post/b0e93985-17a0-4a36-a276-c312bcc84521/image.png)![](https://images.velog.io/images/dev_kickbell/post/39918ad9-db6e-46d1-8ddb-fac1026bce37/image.png)

> 😱 수정 후

![](https://images.velog.io/images/dev_kickbell/post/4462628b-a75d-4b18-8ce1-878e5d22f4b3/image.png)

그러면 기존의 2개의 커밋이 하나로 합쳐진 것을 볼 수 있다. 그리고 옵션에 따라서 커밋메시지에는 `commit의 설명추가` 대표메시지로 보이고 해당 커밋을 클릭하면 밑에 작게 `pull의 설명을 추가` 라는 커밋메시지가 있는 것을 볼 수 있다. 

선택한 대화형 리베이스의 옵션은 6가지가 있다. 커밋메시지를 변경하는 옵션(`reword`)도 있고, `squash`처럼 해당 커밋을 이전 커밋과 합치는 명령어도 있다. 

> Commands:
  p, pick = use commit
  r, reword = use commit, but edit the commit message
  e, edit = use commit, but stop for amending
  s, squash = use commit, but meld into previous commit
  f, fixup = like "squash", but discard this commit's log message
  x, exec = run command (the rest of the line) using shell

`fixup` 또한 해당 커밋을 이전 커밋과 합치는 명령어지만 커밋 메시지는 합치지 않는다. `exec` 를 이용하면, 각각의 커밋이 적용된 후 실행할 shell 명령어를 지정할 수 있다.

`$ git rebase` 는 이전의 커밋 히스토리를 변경하기 때문에 항상 주의해서 사용하여야 한다. 만약 이미 GitHub과 같은 원격 저장소에 push까지 한 커밋이라면 변경한 커밋들은 원격 저장소에 `push`되지 않을 것이다.

이 때 `$ git push --force` 또는 `$ git push -f` 명령어로 강제로 원격 저장소에 커밋 히스토리를 덮어쓸 수도 있다. 하지만 만약 이전에 `push`한 커밋들을 다른 개발자들과 공유하고 있었다면 커밋 히스토리의 불일치가 발생해 흔히 말하는 `"git이 꼬였다."`하는 상황이 발생할 수 있다.

이렇듯 여러 옵션에 따라서 커밋을 합치거나 커밋 메시지를 변경하거나 할 수 있기 때문에 `interactive rebase`이다. 


## 6. Squash

깃크라켄에서는 커밋을 합치는 방법도 아주 쉽게 제공합니다. 물론 CLI로도 합칠 수 있긴해요. 만능 `rebase` 를 사용하면 되죠. 

> git rebase -i HEAD~2

약간 이런느낌입니다. "현재 헤드를 기준으로 2개의 커밋을 합치겠다." 라는 의미로 볼 수 있죠. 

그런데, 위에서 말씀드린 것처럼 깃크라켄에서는 훠얼씬 더 쉽습니다. 

> 1. 합칠 커밋 선택 후 우클릭 
> 2. Squash 버튼 클릭 

![](https://images.velog.io/images/dev_kickbell/post/16d4b6cb-9842-448b-b4f0-5595984b02b4/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-12-06%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%2012.44.18.png)

![](https://images.velog.io/images/dev_kickbell/post/f2b1b996-a25c-4119-be73-e23d21c6a3c1/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-12-06%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%2012.45.09.png)

이렇게 해주면 아주 간단하게 커밋을 합치는 기능인 `Squash` 작업을 할 수 있습니다. 참고할 부분은 지금 보면 커밋메세지가 두개잖아요? 제일 먼저 커밋했던 `Add Resource coin falling, flexing` 와 마지막 커밋인 `Add Resource` 이죠. 그러면 합쳐지면 어떻게 될까? 보시는 바와 같이 커밋의 `Summary` 부분에 제일 먼저 했던 커밋메시지가 들어가고 이후에 합쳐진 커밋이 `Description` 으로 지정됩니다. 참고하셔서 작업하시면 되겠습니다 :) 


