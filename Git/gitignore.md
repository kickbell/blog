`gitignore`는 `git`의 기능 중에 하나로 _**의도적으로 untracked한 파일을 무시**_ 하도록 지정합니다. Swift Project 에서는 굳이 git에 업로드 하지 않아도 될 파일들을 무시하도록  하는데 사용되지요. _**의존성 매니저에서 다운받은 파일인 `Pods`, 배포 자동화와 관련된 `fastlane`, ios의 앱파일인 `ipa`, crash report와 관련된 `dSYM` 파일과 같은 것들**_ 말이죠. 

`gitignore` 파일에서는 아래와 같은 패턴으로 특정 파일을 `untracked` 할 수 있습니다. 더 자세한 설명은 [여기](https://git-scm.com/docs/gitignore)를 참고해주세요. 
- `hello.*` 은 이름이 `hello`로 시작하는 모든 파일 또는 디렉토리를 `untracked` 합니다. 이것을 하위 디렉토리가 아닌 디렉토리로만 제한하려면 패턴 앞에 슬래시를 추가해서 이렇게 `/hello.*` 할 수 있습니다. 이렇게 `hello.txt, hello.c`와 추적할 수 있지만 `a/hello.java`는 추적되지 않습니다.
- `foo/`는 디렉토리 foo 및 그 아래의 경로는 추적되지만 일반 파일 또는 기호 링크 foo는 추적되지 않습니다.
- `doc/frotz` 및 `/doc/frotz`는 모든 `.gitignore` 파일에서 동일한 효과를 갖습니다. 즉, 패턴에 이미 중간 슬래시가 있는 경우 선행 슬래시는 관련이 없습니다.
- `foo/*` 패턴은 `foo/test.json(일반 파일)`, `foo/bar(디렉토리)`와 일치하지만 `foo/bar/hello.c(일반 파일)`은 추적하지 않습니다. 

내용은 어렵지 않으니 이제 실제로 만들어보면서 진행해보도록 하지요. 사실 `gitignore`는 나중에 따로 만들어도 되지만, 처음 Git 레포지토리를 만들 때 같이 만들어 줄 수도 있습니다. 아래의 그림처럼 `.gitignore` 템플릿을 지원해주고 있죠. 			

![](https://velog.velcdn.com/images/dev_kickbell/post/4ba20dfb-a887-4e3d-bfc1-fe22e6787b66/image.png)						

여기서 원하는 템플릿을 선택한 후 레포지토리를 생성하면 프로젝트에 `.gitignore`도 같이 생성됩니다. 하지만 단점도 있어요. 한번에 `하나의 템플릿 밖에 선택`되지 않고, 템플릿에 나오지 않는 것들도 있죠. 예를 들어 `Xcode`, `CocoaPods` 같은 것들이요. 이런 것들은 레포지토리를 생성한 후 직접 `.gitignore` 파일을 열어서 이미 있다면 주석을 해제해주고, 없다면 추가해주어야 합니다. 

아니면, 괜찮은 유틸리티 서비스를 이용할 수도 있는데요. 바로 [여기](https://www.toptal.com/developers/gitignore)입니다. 내가 지정한 태그를 기준으로 `.gitignore` 파일을 생성해주죠. 다중선택도 가능해서 널리 쓰이는 유틸리티 입니다😁. 아래와 같이 원하는 태그를 선택해주고 생성 버튼을 누르면 `.gitignore` 파일이 생성되지요. 그럼 그 `.gitignore` 파일을 이미 만들어놓은 `.gitignore`에 덮어씌우거나 새로 생성해서 넣어주면 됩니다. 
			
![](https://velog.velcdn.com/images/dev_kickbell/post/924c1893-d667-4370-8bf7-3570b03979cd/image.png)					

```
# Created by https://www.toptal.com/developers/gitignore/api/swift,swiftpackagemanager,cocoapods,xcode
# Edit at https://www.toptal.com/developers/gitignore?templates=swift,swiftpackagemanager,cocoapods,xcode

### CocoaPods ###
## CocoaPods GitIgnore Template

# CocoaPods - Only use to conserve bandwidth / Save time on Pushing
#           - Also handy if you have a large number of dependant pods
#           - AS PER https://guides.cocoapods.org/using/using-cocoapods.html NEVER IGNORE THE LOCK FILE
Pods/

### SwiftPackageManager ###
Packages
xcuserdata
*.xcodeproj

...생략

### Xcode Patch ###
*.xcodeproj/*
!*.xcodeproj/project.pbxproj
!*.xcodeproj/xcshareddata/
!*.xcworkspace/contents.xcworkspacedata
/*.gcno
**/xcshareddata/WorkspaceSettings.xcsettings

# End of https://www.toptal.com/developers/gitignore/api/swift,swiftpackagemanager,cocoapods,xcode
```

위의 코드를 보면 너무 길어서 일부 생략했지만 `Pods/`, `*.xcodeproj/*`, `Packages` 과 같은 것들이 `untracked` 파일로 지정되어 있지요. 간단하죠 ? 하지만 인생사 자기 맘대로 되는 것이 하나도 없다고,,,🥲 저는 잘 되지 않더군요. 꼼꼼한 성격이라면 프로젝트 생성할 때 먼저 `.gitignore` 를 만들어놓고 시작했을텐데, 저는 그러지 않았어서 발생했던 문제였어요. 아래의 예시는 그럴 때 대처하는 방법입니다.

## 작업 중에 .gitignore 추가하기 

일단 test라는 레포지토리를 만들었어요. `.gitignore` 파일을 추가했고, 템플릿은 `Swift`를 선택했지요.  그리고 Xcode 프로젝트를 생성하고 커밋을 했습니다. 그리고 다시 CocoaPods을 설치해서 RxSwift를 설치 후에 어떤 작업을 하겠죠? 그리고 다시 커밋을 하려고 보니 제가 사용하는 툴인 `Gitkraken`에서 Pods 파일을 추적하는 상황이 생겨버린 겁니다.  

![](https://velog.velcdn.com/images/dev_kickbell/post/73a85129-e650-4665-94c4-d48b13353434/image.png)					

음 그러면 `.gitignore`에 Pods 가 지정되지 않아있나? 하고 생각되니 확인을 해야겠죠. `.gitignore` 파일을 열어봅니다. 만약에 프로젝트 생성시 `.gitignore`를 생성하지 않으셨다면 위에서 말씀드린대로 파일을 추가해주시면 되겠죠. `.gitignore`을 확인해보니 CocoaPods 관련 내용은 정상적으로 추가되어있지만 `Pods/` 가 주석처리가 되어있어 동작하지 않았나봅니다. 

```swift
...
# CocoaPods
#
# We recommend against adding the Pods directory to your .gitignore. However
# you should judge for yourself, the pros and cons are mentioned at:
# https://guides.cocoapods.org/using/using-cocoapods.html#should-i-check-the-pods-directory-into-source-control
#
# Pods/
#
# Add this line if you want to avoid checking in source code from the Xcode workspace
# *.xcworkspace
...
```

그런데 주석으로 설명이 적혀져 있네요. `Pods` 디렉토리를 추가하는 것은 내 맘이지만, 장단점이 있다고 하네요. 한 번 들어가볼까요? `Pods` 디렉토리를 추적한다면 `장점`은 당연하게도 `Pods`의 파일들이 업로드되지 않아도 되니 레포지토리는 더 작아지고 공간을 덜 차지하지하게 됩니다. 라이브러리를 몇 개만 사용한다면 상관없겠지만, 프로젝트의 크기에 따라 `Pod`의 개수는 수십개가 될 수도 있습니다. 이러면 좀 문제가 될 수 있겠죠. 반대로 추적하지 않는다면 얻는 `이점`도 있습니다. 바로 `pod install` 을 하지 않아도 되는 것이죠. 또, 다른 `Pod` 버전과 브랜치를 병합하는 것과 같은 소스 제어 작업을 수행할 때 처리해야 할 충돌도 없습니다. 더 자세한 내용은 [여기](https://guides.cocoapods.org/using/using-cocoapods.html#should-i-check-the-pods-directory-into-source-control) 있으니 참고하시면 되겠습니다. 

일단, 장단점에 대해서는 알고만 계시고 저희는 `Pods`를 추적하지 않기로 했으니 `.gitignore` 파일의 주석을 해제해주겠습니다. 그리고 커밋한 뒤 푸시할게요. 

![](https://velog.velcdn.com/images/dev_kickbell/post/e9eca239-31b8-4fb0-ae43-0e438b7a5dd4/image.png)					

그러나, Pods 디렉토리가 추적해제 되지 않습니다. 결과는 똑같죠. 저장을 다시 해보고, `git pull` 을 해보고 Xcode를 종료 후 재실행해도 결과는 똑같습니다. 왜 그럴까요 ? 이유는 2가지가 있습니다. 

1. Pods 디렉토리의 경로가 잘못되었습니다. 
2. git cache를 삭제해주어야 합니다. 

1번 부터 해볼까요 ? 아까 글의 도입부에서 패턴 중에 이런 부분이 있었죠. 그리고 지금 test 레포지토리의 폴더 구조는 아래와 같습니다.

- `foo/`는 디렉토리 foo 및 그 아래의 경로는 추적되지만 일반 파일 또는 기호 링크 foo는 추적되지 않습니다.
```
test
  └── .DS_Store
  └── .git
        └── ...
  └── .gitignore
  └── README.md
  └── testProject
      	  └── .DS_Store
          └── Podfile
      	  └── Podfile.lock
          └── Pods
                └── ...
 ```
 
 따라서 `.gitignore` 파일에서 `Pods/` 가 아니라 `testProject/Pods` 로 경로를 변경해주어야 하죠.
 
다음은 `git cache`를 삭제해주어야 합니다. `git rm -r --cached .` 명령어로 캐시를 삭제해줍니다. 순서가 중요해요. 

1. `.gitignore` 을 변경합니다. 
2. 그 다음 `git rm -r --cached .` 를 실행합니다.
3. 그리고 `git add .` 를 실행합니다

이렇게 하면 Pods 파일들이 `UnStaged Files`에 보이지 않는 것을 볼 수 있어요. 경로도 변경했고, 캐시도 삭제해주었기 때문이죠. 

## Conclusion
- Git에 업로드 하지 않고 싶은 파일은 `.gitignore` 에 정의합니다. 
- `.gitignore` 를 추가했음에도 계속 추적이 된다면 디렉토리의 경로를 다시 확인해보고, `git rm -r --cached .` 를 통해 캐시를 삭제해야 합니다.
- Pods 를 업로드하는 것에는 각각의 장단점이 있습니다. 

## Reference

[https://github.com/github/gitignore/blob/main/Swift.gitignore
](https://github.com/github/gitignore/blob/main/Swift.gitignore)		

[https://git-scm.com/docs/gitignore](https://git-scm.com/docs/gitignore)			
					
[https://guides.cocoapods.org/using/using-cocoapods.html#should-i-check-the-pods-directory-into-source-control](https://guides.cocoapods.org/using/using-cocoapods.html#should-i-check-the-pods-directory-into-source-control)						
				
[https://medium.com/codex/create-git-ignore-file-for-swift-and-xcode-development-d7faaf58893c
](https://medium.com/codex/create-git-ignore-file-for-swift-and-xcode-development-d7faaf58893c)

[https://sigalambigha.home.blog/2020/03/11/how-to-refresh-gitignore/](https://sigalambigha.home.blog/2020/03/11/how-to-refresh-gitignore/)




