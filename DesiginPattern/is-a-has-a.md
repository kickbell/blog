
## IS-A 
- 객체지향 프로그래밍에서 Inheritance(상속)에 주요 적용되는 개념이다.
- "A는 B이다."와 같이 사용된다. 
- 사람은 동물이다/소는 동물이다/새는 동물이다.					
- <img width="" alt="image" src="https://user-images.githubusercontent.com/85085822/203355718-d517574f-c4f3-4b87-9a20-0bd763400a71.png">		
-  
```swift
class Animal {} 
class Human: Animal {}
class Cow: Animal {}
class Bird: Animal {}
```

## HAS-A
- 객체지향 프로그래밍에서 Composition(구성)에 주로 적용되는 개념이다. 				
- "A에는 B가 있다."와 같이 사용된다. 					
- <img width="" alt="image" src="https://user-images.githubusercontent.com/85085822/203355839-ffeaba44-e761-4800-bcd8-13db4680883d.png">		
- 
```swift
class CPU {}
class RAM {}
class MainBoard {}

class Computer {
    let cpu: CPU
    let mainBoard: MainBoard
    let ran: RAM

    init(cpu: CPU, mainBoard: MainBoard, ram: RAM) {
        self.cpu = cpu
        self.mainBoard = mainBoard
        self.ram = ram
    }
}
```
