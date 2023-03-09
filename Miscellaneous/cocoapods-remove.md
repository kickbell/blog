# 코코아팟 제거하기 

업무를 하다보면 설치해놓은 코코아팟을 제거해야하는 경우가 생깁니다. 

저같은 경우는 프로젝트에서 코코아팟을 사용했는데, 그걸 SPM으로 변경하면서 코코아팟이 필요없게 되거나 하는 경우에 해당되었어요. 

프로젝트내에서 마우스로 하나하나 지우자니 답도 없습니다. 괜히 이상한 에러가 발생하기도 하지요. 

이런 경우에는 그냥 아래와 같이 workspace가 있는 내 프로젝트로 이동해서 아래의 명령어를 터미널에서 한줄한줄씩 입력해주면 깨끗하게 코코아팟 관련 파일들이 제거됩니다. 

```swift
$ sudo gem install cocoapods-deintegrate cocoapods-clean
$ pod deintegrate
$ pod clean
$ rm Podfile
```
