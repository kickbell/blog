오늘은 아주 간단한 방법으로 현재 진행하고 있는 프로젝트의 `UML 클래스 다이어그램`을 그려주는 툴을 소개해보려고 합니다. [SwiftPlantUML](https://github.com/MarcoEidinger/SwiftPlantUML) 이라는 툴인데요. 저도 지인을 통해서 알게 됐는데, 너무 좋은 것 같아요.😄 사용법이 워낙에 간단해서 바로 사용해볼게요. 

## Use
 
1. 터미널에서 `brew install swiftplantuml` 을 입력해서 `swiftplantuml`를 설치합니다. 
2. 내가 UML 다이어그램을 그리고 싶은 디렉토리로 이동합니다.
3. `swiftplantuml` 를 입력합니다.

정말 간단하죠 ? 

저같은 경우는 아래의 그림처럼 폴더구조가 되어있었는데, `Pods` 같은 부분은 필요가 없기 때문에 `Views`, `AppDelegates`가 있는 디렉토리로 한번 더 들어가서 `swiftplantuml` 를 실행해주었습니다. 
      
![](https://velog.velcdn.com/images/dev_kickbell/post/9b5fb495-91ca-4694-86c2-612c8697d434/image.png)       
![](https://velog.velcdn.com/images/dev_kickbell/post/922e0136-7c07-49ee-99e4-cf60b542153b/image.png)         

그러면 브라우저가 실행되면서 페이지가 호출되는데요. 아래와 같은식으로 먼저 내 디렉토리 내의 클래스와 함수가 정의되어 있고, 더 내려보면 실제 정의된 클래스와의 관계가 정의되어 있는 것을 볼 수 있어요.       
      
![](https://velog.velcdn.com/images/dev_kickbell/post/257bd554-b392-4217-a687-815b0bec94b2/image.png)       
![](https://velog.velcdn.com/images/dev_kickbell/post/38aef347-de97-4557-a317-71deac54c5ac/image.png)         

그리고 그 밑에는 정의된 관계를 가지고 UML 다이어그램이 그려져 있는 것을 볼 수 있는데요. 너무 많아지면 보기가 힘드니 모듈 별로 보는 것이 그나마 괜찮은 것 같아요. 현재 이 상태에서 그냥 보시거나 이미지 캡쳐 후 클립보드에 복사해서 바로 가져다쓰셔도 괜찮지만, 왼쪽 하단에 4가지 타입의 메뉴를 지원합니다. 

- PNG
	- 탭하면 새로운 브라우저에서 이미지 파일이 열리고, 우클릭을 통해 이미지로 저장할 수 있어요. 다만, 브라우저 창에서 확대는 되지만 좌우 스크롤이 되지 않아서 좀 불편했습니다. 이미지로 저장 후 미리보기로 열었을 때도 같은 이슈가 있어서 따로 다른 툴을 사용해야 될 지도 모르겠네요. 
- SVG 
	- 탭하면 새로운 브라우저에서 이미지 파일이 열립니다. PNG를 열었을 때와는 다르게 좌우 스크롤이 가능해요.
- TXT
	- 탭하면 새로운 브라우저가 열리고, txt 타입으로 파일이 출력되어 있습니다. 다소 가독성이 떨어지는 단점이 있어요. 
- Edit 
	- 생성된 UML 다이어그램을 편집할 수 있습니다. 자세한 것은 바로 아래에서 다시 보도록 할게요. 

## Customizing 

여기까지만 보더라도 꽤나 훌륭한 툴인 것을 알 수 있는데요. 100% 정확한 것은 아닌 것 같더라구요. 예를 들어, 아래는 위의 프로젝트의 일부분 인데요. 

```swift
struct ToDo: Identifiable, Codable {
    var id: String
    var title: String
    var done: Bool
    let createdAt: Date
}
```
        
![](https://velog.velcdn.com/images/dev_kickbell/post/23e77a33-3eac-4c87-a46f-90b92ed1dedc/image.png)           

`ToDo` 라는 모델이 있고 iOS 개발을 하면서 흔히 마주할 수 있는 대표적인 `Identifiable`, `Codable` 프로토콜을 채택하고 있죠. 하지만 `UML 다이어그램` 상으로는 상속으로 표현이 되고 있습니다. 이런 부분을 위에서 말했던 `Edit` 모드를 통해 수정할 수 있어요. 

명령어를 입력해서 나온 페이지에 다시 돌아가 볼게요. 그러면 아까 말씀드렸듯이 디렉토리 내의 클래스와 함수가 정의된 부분 맨 밑에 실제 정의된 클래스의 관계가 정의되어 있는 코드가 있죠. 이것을 상속에서 프로토콜 채택으로 수정해줍니다. 

```swift
//Previous
Identifiable <|-- ToDo : inherits
Codable <|-- ToDo : inherits

//Next 
Identifiable <|.. ToDo : confirms to
Codable <|.. ToDo : confirms to
```

그리고 위로 올라가서 `Refresh` 버튼을 탭하거나 `Alt`+`Enter` 키를 누르면 브라우저에서 `UML 다이어그램`을 다시 그려주고 고친 부분이 수정되어 나오는 것을 볼 수 있습니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/36da8111-7cc5-4d1e-985b-d04787d0f84a/image.png)    

`SwiftPlantUML`, 어디에 사용할 수 있을까요 ? 

음.. 정말 많은 곳에 사용할 수 있을 것 같아요. 처음부터 UML 다이어그램으로 프로젝트를 완벽하게 설계하고 나서 개발하면 좋겠지만, 그렇지 못한 경우에 주기적으로 체크해주는 용도라던지 또는 이미 구현된 프로젝트를 리팩토링 하기 위한 참고 용도라던지, 또는 디자인 패턴이나 내가 포트폴리오로 만든 프로젝트를 설명하기 위한 도구로도 사용될 수 있을 것 같습니다. 


## Reference

[https://github.com/MarcoEidinger/SwiftPlantUML](https://github.com/MarcoEidinger/SwiftPlantUML)        


