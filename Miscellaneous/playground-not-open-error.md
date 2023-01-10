# Playground 가 열리지 않는 오류 해결하기

### 문제 발생 

- 프로그래밍을 하다가 예전에 작성해놓은 코드를 열어보고 싶어서 플레이그라운드를 열었는데 에러가 발생했던 적이 있습니다. 
- 이런 것은 파일을 압축 또는 압축 해제하고 Mac이 설정에 따라 일부 파일을 .xml 파일로 변환할 때 발생합니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/991578a9-eaff-4c67-8952-c71c0f76fabd/image.png)


### 해결하기 1 (xml 확장자 제거해주기) 
 
- 먼저 플레이그라운드를 우측 마우스 클릭하고 패키지 내용 보기를 선택합니다. 그러면 아래와 같이 파인더가 이동됩니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/0631b285-a45d-4b31-9450-135a94dda0b5/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/c3080d79-8a04-4fdc-a51d-7edf836ab156/image.png)

- 여기서 contents.xcplayground.xml 파일을 우클릭하고 정보 가져오기를 선택합니다. 
- 그러면 중간즈음에 `이름 및 확장자`가 있을겁니다. 거기서 .xml 확장자를 삭제합니다.
- 엔터키를 누르면 확장자 변경에 대한 팝업창이 뜰겁니다. 여기서는 .xml를 삭제하려고 한 것이니 .xcplayground 사용을 눌러줍니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/00d0ab45-7aba-4408-aa33-93012583225a/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/6d7b9bfb-9cd5-4532-bbf0-151ca995bc6e/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/084beea2-f950-47b3-a4d0-e1413c8015b4/image.png)

### 해결하기 2 (playground.xcworkspace)
- playground.xcworkspace 에도 같은 작업을 반복해줍니다. 
- 먼저 패키지 내용보기를 선택하고, 똑같이 내부에 있는 contents.xcworkspacedata.xml 파일을 우클릭해서 정보 가져오기를 선택합니다. .xml 확장자를 제거합니다. 
- 이제 다시 상위 폴더로 이동해서 `Part 3.playground` 를 열어줍니다. 정상적으로 열리는 것을 확인할 수가 있습니다. 
 
 




