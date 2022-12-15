
# Command Pattern(커맨드 패턴)
> - 요청 내역을 객체로 캡슐화해서 객체를 서로 다른 요청 내역에 따라 매개변수화 할 수 있습니다. 이러면 요청을 큐에 저장하거나 로그로 기록하거나 작업 취소 기능을 사용할 수 있습니다.

## 1. 만능 IOT 리모컨 만들기 
- IOT 리모컨의 API를 만들어야 합니다. 리모컨에는 7개의 슬롯과 ON/OFF 버튼, UNDO(뒤로) 버튼이 있네요. 
- 문제는 제어해야하는 객체의 클래스가 너무 많다는 거죠. 또, 앞으로 이와 비슷한 클래스가 더 추가될 수도 있을 겁니다. 버튼은 ON/OFF 2개 뿐인데 말이죠. 어떻게 해야 할까요 ? 
				
![](https://velog.velcdn.com/images/dev_kickbell/post/81df2a1b-4307-4723-b35e-298f4cd52368/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/0b66aee6-84e1-459f-9d2e-162cce19e7aa/image.png)			


## 2. 음식 주문 과정과 커맨드 패턴 
- 커맨드 패턴은 음식 주문 과정과 유사합니다. 양쪽 그림을 비교해보면서 읽어보세요. 
- 현실에서는 주문서(Command)에 주방장(Receiver)가 들어가진 않으니 그거 빼고는 유사한 것 같습니다. 
- Command(명령), Invorker(호출자), Execute(실행하다), Receiver(수신자)
- 음식 주문 과정 
    - createOrder(): 고객(Client)는 테이블에 놓여있는 주문서(Command)에 먹을 메뉴를 적습니다.
    - takeOrder(): 고객(클라이언트)는 "저기요~ 메뉴 다적었어요~!"하며 주문서(Command)를 종업원(Invorker)에게 전달합니다. 
    - orderUp(): 주문이 들어왔으니 알려야겠죠? 종업원(Invorker)은 카운터에가서 "주문 들어왔어요~!" 하고 알립니다. 실제와 다르게 무슨 주문이 들어왔는진 몰라도 됩니다. 전달만 하면 되니까요. 
    - makeBurger(), makeShake(): 주방장(Receiver)은 아무것도 모르고 있다가 주문이 들어온 것을 확인하고 주문서대로 메뉴를 만듭니다. 
- 커맨드 패턴
    - createCommandObject(): 클라이언트는 Command 객체를 생성합니다. Command에는 Receiver가 있어요. 
    - setCommand(): 클라이언트는 Invorker 객체의 setCommand()를 호출하며, Command 객체를 넘겨줍니다. 인보커 객체는 Command 객체를 보관합니다. 
    - execute(): Invorker는 Command 객체의 execute()를 실행합니다. 뭐가 실행되는지는 몰라도 됩니다. 
    - action1(), action2(): Command 객체의 execute()가 실행되면 커맨드 안에 있는 Receiver의 특정 동작이 실행됩니다. TVon()/TVoff()/Lighton()/Lightoff() 같은 것들이죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/b0d08da2-68b7-4427-b935-37a5d4f475ad/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/9cffb3b7-1b8d-43dc-92de-1eef90bcb6f3/image.png)


## 3. 조명만 켜는 리모컨API 만들어보기 
- 가장 간단하게 조명만 켜는 딱 한가지 일을 하는 API를 만들어볼게요. 
- 먼저 Command 인터페이스를 만듭니다. execute() 만 있으면 될거에요. 
- 그리고, 조명만 켜는 일만 하는 LightOnCommand를 만들겁니다. 다음에 나오겠지만, 조명을 꺼야하는 LightOffCommand는 따로 만들어야 해요. 위에 그림에서 뭐랬죠? 커맨드는 Receiver를 받는다고 했죠. Light 객체가 Receiver 입니다. Command의 execute()가 실행되면 Receiver에 있는 on() 메소드가 실행되죠. 
- RemoteControl(리모컨)을 만들어 줄 건데요. RemoteControl에는 Command를 매개변수로 받아서 보관하는 setCommand가 있네요. 위에 그림에서는 종업원이 주문서를 takeOrder()하면서 받아갔었죠. 
- 그러면 이제 실행을 해볼건데, 클라이언트는 LightOnCommand를 만들고 SimpleRemoteControl에게 LightOnCommand를 넘겨줍니다. 그러면 SimpleRemoteControl는 그 LightOnCommand를 잘 보관하고 있다가, buttonWasPressed() 하게 되면 Command의 execute()가 실행되죠.

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>    

```swift
protocol Command {
    func execute()
}

class LightOnCommand: Command {
    let light: Light
    
    init(_ light: Light) {
        self.light = light
    }
    
    func execute() {
        light.on()
    }
}

class SimpleRemoteControl {
    var slot: Command?
    
    func setCommand(_ command: Command) {
        slot = command
    }
    
    func buttonWasPressed() {
        slot?.execute()
    }
}
    
class Light {
    func on() {
        print("조명이 켜졌습니다.")
    }
    
    func off() {
        print("조명이 꺼졌습니다.")
    }
}
    
let remote = SimpleRemoteControl()
let light = Light()
let lightOnCommand = LightOnCommand(light)

remote.setCommand(lightOnCommand)
remote.buttonWasPressed()
//조명이 켜졌습니다.
```
  </p>
</details>

## 4. 커맨드 패턴
- 커맨드 객체는 일련의 행동을 특정 리시버가 연결함으로써 요청을 캡슐화합니다. 그러기 위해서 커맨드에 receiver.action()과 receiver를 넣고, execute()라는 메소드 하나만 외부에 공개합니다. 밖에서 볼 때는 어떤 객체가 receiver 역할을 하는지, 그 receiver가 무슨 일을 하는지 알 수 없습니다. 그냥 execute()가 실행되면 해당 요청이 처리된다는 사실만 알죠. 
- 명령으로 객체를 매개변수화 할 수도 있습니다. 아래의 코드를 보면 알 수 있는데요. 위의 코드에서 GarageDoorOpenCommand, GarageDoor 클래스를 추가했고 기존에 있던 remote에 새로운 커맨드를 할당하고 실행하면 "차고 문이 열렸습니다."라는 출력을 볼 수 있죠. GarageDoorOpenCommand 라는 객체가 마치 매개변수처럼 된 것이죠. 

![](https://velog.velcdn.com/images/dev_kickbell/post/ed11c493-5ea8-48b4-b405-cb8280e2c87f/image.png)

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class GarageDoor {
    func open() {
        print("차고 문이 열렸습니다.")
    }
    
    func close() {
        print("차고 문이 닫혔습니다.")
    }
}
    
class GarageDoorOpenCommand: Command {
    let garageDoor: GarageDoor
    
    init(_ garageDoor: GarageDoor) {
        self.garageDoor = garageDoor
    }
    
    func execute() {
        garageDoor.open()
    }
}
    
var remote = SimpleRemoteControl()

let light = Light()
let lightOnCommand = LightOnCommand(light)

let garageDoor = GarageDoor()
let garageDoorOnCommand = GarageDoorOpenCommand(garageDoor)

remote.setCommand(lightOnCommand)
remote.buttonWasPressed()
//조명이 켜졌습니다.

remote.setCommand(garageDoorOnCommand)
remote.buttonWasPressed()
//차고 문이 열렸습니다.
```
  </p>
</details>


## 5. 나머지 기능도 API에 넣기 
- 나머지 기능도 넣어봅시다. 차이점은 RemoteControl을 새로 만들 건데, Command 객체를 담을 배열을 하나 만들겁니다. 그리고 매개변수로 몇번째 요소를 execute()한 것인지 알아야하기 때문에 index를 받을거에요. 
- 또 NoCommand() 라는 애를 하나 받을건데, 얘는 safety code 입니다. 커맨드에 아무것도 없을 때 배열의 없는 index 번호로 접근하면 crash가 발생하니까 그걸 방지하는 용도죠. 
- 마지막으로는 예쁘게 출력하기 위해서 toString() 이라는 메소드를 하나 만들겁니다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/72cd09da-1c02-4d68-acea-bd3c42124985/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/463752d1-b023-45ce-8c36-b6276193da66/image.png)

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class RemoteControl {
    var onCommands: [Command] = []
    var offCommands: [Command] = []
    
    init() {
        let noCommand = NoCommand()
        for _ in 0...6 {
            onCommands.append(noCommand)
            offCommands.append(noCommand)
        }
    }
    
    func setCommand(_ slot: Int,
                    _ onCommand: Command,
                    _ offCommand: Command) {
        onCommands[slot] = onCommand
        offCommands[slot] = offCommand
    }
    
    func onButtonWasPushed(_ slot: Int) {
        onCommands[slot].execute()
    }
    
    func offButtonWasPushed(_ slot: Int) {
        offCommands[slot].execute()
    }
    
    func toString() -> [String] {
        var stringBuffer: [String] = []
        stringBuffer.append("------ 리모컨 ------")
        for i in 0...onCommands.count-1 {
            let str = "[slot \(i)] \(onCommands[i].getName()) \(offCommands[i].getName())"
            stringBuffer.append(str)
        }
        stringBuffer.append("")
        return stringBuffer
    }
}
```
```swift
protocol Command {
    func execute()
}

extension Command {
    func getName() -> String {
        var str = String(describing: self).components(separatedBy: ".").last ?? ""
        while str.count < 30 {
            str += " "
        }
        return str
    }
}

class NoCommand: Command {
    func execute() { }
}

class LightOnCommand: Command {
    let light: Light
    
    init(_ light: Light) {
        self.light = light
    }
    
    func execute() {
        light.on()
    }
}

class LightOffCommand: Command {
    let light: Light
    
    init(_ light: Light) {
        self.light = light
    }
    
    func execute() {
        light.off()
    }
}

class GarageDoorOpenCommand: Command {
    let garageDoor: GarageDoor
    
    init(_ garageDoor: GarageDoor) {
        self.garageDoor = garageDoor
    }
    
    func execute() {
        garageDoor.open()
    }
}

class GarageDoorCloseCommand: Command {
    let garageDoor: GarageDoor
    
    init(_ garageDoor: GarageDoor) {
        self.garageDoor = garageDoor
    }
    
    func execute() {
        garageDoor.close()
    }
}

class StereoOnWithCDCommand: Command {
    let stereo: Stereo
    
    init(_ stereo: Stereo) {
        self.stereo = stereo
    }
    
    func execute() {
        stereo.on()
        stereo.setCD()
        stereo.setVolume(11)
    }
}

class StereoOffCommand: Command {
    let stereo: Stereo
    
    init(_ stereo: Stereo) {
        self.stereo = stereo
    }
    
    func execute() {
        stereo.off()
    }
}

class CeillingFanOnCommand: Command {
    let ceillingFan: CeilingFan
    
    init(_ ceillingFan: CeilingFan) {
        self.ceillingFan = ceillingFan
    }
    
    func execute() {
        ceillingFan.on()
    }
}

class CeillingFanOffCommand: Command {
    let ceillingFan: CeilingFan
    
    init(_ ceillingFan: CeilingFan) {
        self.ceillingFan = ceillingFan
    }
    
    func execute() {
        ceillingFan.off()
    }
}
```
```swift

class Light {
    func on() {
        print("조명이 켜졌습니다.")
    }
    
    func off() {
        print("조명이 꺼졌습니다.")
    }
}

class GarageDoor {
    func open() {
        print("차고 문이 열렸습니다.")
    }
    
    func close() {
        print("차고 문이 닫혔습니다.")
    }
}

class Stereo {
    func on() {
       print("거실 오디오가 켜졌습니다.")
    }
    
    func off() {
       print("거실 오디오가 꺼졌습니다.")
    }
    
    func setCD() {
        print("거실 오디오에서 CD가 재생됩니다.")
    }
    
    func setDvd() {
        print("거실 오디오에서 DVD가 재생됩니다.")
    }
    
    func setRadio() {
        print("거실 오디오에서 Radio가 재생됩니다.")
    }
    
    func setVolume(_ num: Int){
        print("거실 오디오의 볼륨이 \(num)으로 설정되었습니다.")
    }
}

class CeilingFan {
    func high() {
        print("거실 선풍기 속도가 HIGH로 설정되었습니다.")
    }
    
    func medium() {
        print("거실 선풍기 속도가 MEDIUM으로 설정되었습니다.")
    }
    
    func low() {
        print("거실 선풍기 속도가 LOW로 설정되었습니다.")
    }

    func on() {
        print("거실 선풍기가 켜졌습니다.")
    }
    
    func off() {
        print("거실 선풍기가 꺼졌습니다.")
    }
    
    func getSpeed(_ speed: String) {
        print("거실 선풍기 속도는 \(speed)입니다.")
    }
} 
```
```swift

var remote = RemoteControl()

let light = Light()
let ceilingFan = CeilingFan()
let garageDoor = GarageDoor()
let stereo = Stereo()

let lightOnCommand = LightOnCommand(light)
let lightOffCommand = LightOffCommand(light)
let ceillingFanOnCommand = CeillingFanOnCommand(ceilingFan)
let ceillingFanOffCommand = CeillingFanOffCommand(ceilingFan)
let garageDoorOpenCommand = GarageDoorOpenCommand(garageDoor)
let garageDoorCloseCommand = GarageDoorCloseCommand(garageDoor)
let stereoOnWithCDCommand = StereoOnWithCDCommand(stereo)
let stereoOffCommand = StereoOffCommand(stereo)


remote.setCommand(0, lightOnCommand, lightOffCommand)
remote.setCommand(1, ceillingFanOnCommand, ceillingFanOffCommand)
remote.setCommand(2, garageDoorOpenCommand, garageDoorCloseCommand)
remote.setCommand(3, stereoOnWithCDCommand, stereoOffCommand)

remote.toString().forEach { print($0) }

remote.onButtonWasPushed(0)
remote.onButtonWasPushed(1)
remote.onButtonWasPushed(2)
remote.onButtonWasPushed(3)

/*
 ------ 리모컨 ------
 [slot 0] LightOnCommand                 LightOffCommand
 [slot 1] CeillingFanOnCommand           CeillingFanOffCommand
 [slot 2] GarageDoorOpenCommand          GarageDoorCloseCommand
 [slot 3] StereoOnWithCDCommand          StereoOffCommand
 [slot 4] NoCommand                      NoCommand
 [slot 5] NoCommand                      NoCommand
 [slot 6] NoCommand                      NoCommand

 조명이 켜졌습니다.
 거실 선풍기가 켜졌습니다.
 차고 문이 열렸습니다.
 거실 오디오가 켜졌습니다.
 거실 오디오에서 CD가 재생됩니다.
 거실 오디오의 볼륨이 11으로 설정되었습니다.
 */
```
  </p>
</details>


## 6. 작업 취소 기능 추가하기 
- 작업 취소 기능은 생각보다는 간단한데요. 
- 먼저 Commmand 인터페이스에 undo()를 추가합니다. 그리고 각 서브클래스에 undo()를 구현해주어야 해요. 예를 들어, LightOnCommand 이면 undo() 내부에는 light.off() 를 구현해야 겠죠. 
- 그리고, RemoteControl로 와서 undoCommmand 라는 변수를 생성합니다. 그리고 on/offButtonWasPushed 할 때마다 해당 커맨드를 undoCommand에 넣어주는 거에요. 
- 마지막으로 undoButtonWasPushed() 라는 메소드를 만들어서 execute()를 실행시켜줬던 것처럼 undoCommand.undo()를 실행시켜 주면 되겠죠. 

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
protocol Command {
    func execute()
    func undo()
}
    
class LightOnCommand: Command {
    let light: Light
    
    init(_ light: Light) {
        self.light = light
    }
    
    func execute() {
        light.on()
    }
    
    func undo() {
        light.off()
    }
}
    
class RemoteControl {
    var onCommands: [Command] = []
    var offCommands: [Command] = []
    var undoCommand: Command = NoCommand()
    
    func onButtonWasPushed(_ slot: Int) {
        onCommands[slot].execute()
        undoCommand = onCommands[slot]
    }
    
    func offButtonWasPushed(_ slot: Int) {
        offCommands[slot].execute()
        undoCommand = offCommands[slot]
    }
    
    func undoButtonWasPushed() {
        undoCommand.undo()
    }
    //...
}
   
//...
remote.setCommand(0, lightOnCommand, lightOffCommand)
remote.setCommand(1, ceillingFanOnCommand, ceillingFanOffCommand)
remote.setCommand(2, garageDoorOpenCommand, garageDoorCloseCommand)
remote.setCommand(3, stereoOnWithCDCommand, stereoOffCommand)

remote.toString().forEach { print($0) }

remote.undoButtonWasPushed() //아무 일도 일어나지 않음. NoCommand 호출
remote.onButtonWasPushed(0)
remote.undoButtonWasPushed()
remote.onButtonWasPushed(1)
remote.undoButtonWasPushed()
remote.onButtonWasPushed(2)
remote.undoButtonWasPushed()
remote.onButtonWasPushed(3)
remote.undoButtonWasPushed()
remote.undoButtonWasPushed() //마지막 녀석이 저장되어있기 때문에 오디오 꺼짐 2번 호출

/*
 ------ 리모컨 ------
 [slot 0] LightOnCommand                 LightOffCommand
 [slot 1] CeillingFanOnCommand           CeillingFanOffCommand
 [slot 2] GarageDoorOpenCommand          GarageDoorCloseCommand
 [slot 3] StereoOnWithCDCommand          StereoOffCommand
 [slot 4] NoCommand                      NoCommand
 [slot 5] NoCommand                      NoCommand
 [slot 6] NoCommand                      NoCommand

 조명이 켜졌습니다.
 조명이 꺼졌습니다.
 거실 선풍기가 켜졌습니다.
 거실 선풍기가 꺼졌습니다.
 차고 문이 열렸습니다.
 차고 문이 닫혔습니다.
 거실 오디오가 켜졌습니다.
 거실 오디오에서 CD가 재생됩니다.
 거실 오디오의 볼륨이 11으로 설정되었습니다.
 거실 오디오가 꺼졌습니다.
 거실 오디오가 꺼졌습니다.
 Program ended with exit code: 0
 */
```
  </p>
</details>


## 7. 작업 취소 기능에 상태값 추가하기 
- 중요한 포인트는 excute() 에서 getSpeed()로 가져온 상태값을 prevSpeed에 할당해줘야 하는 부분입니다. 
- 그리고 Speed는 static의 int 변수로 선언하는 것보단 enum을 활용하는게 더 좋겠죠. 

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class CeilingFan {
    enum Speed: Int {
        case OFF = 0
        case LOW
        case MEDIUM
        case HIGH
    }
    
    var location: String = ""
    var speed = Speed.OFF
    
    func high() {
        speed = Speed.HIGH
        print("거실 선풍기 속도가 \(speed)로 설정되었습니다.")
    }
    
    func medium() {
        speed = Speed.MEDIUM
        print("거실 선풍기 속도가 \(speed)로 설정되었습니다.")
    }
    
    func low() {
        speed = Speed.LOW
        print("거실 선풍기 속도가 \(speed)로 설정되었습니다.")
    }

    func on() {
        speed = Speed.MEDIUM
        print("거실 선풍기 속도가 \(speed)로 설정되었습니다.")
    }
    
    func off() {
        speed = Speed.OFF
        print("거실 선풍기가 꺼졌습니다.")
    }
    
    func getSpeed(_ speed: CeilingFan.Speed) -> CeilingFan.Speed {
        print("거실 선풍기 속도는 \(speed)입니다.")
        return speed
    }
}
    
class CeillingFanHighCommand: Command {
    let ceillingFan: CeilingFan
    var prevSpeed: CeilingFan.Speed
    
    init(_ ceillingFan: CeilingFan) {
        self.ceillingFan = ceillingFan
        self.prevSpeed = ceillingFan.speed
    }
    
    func execute() {
        prevSpeed = ceillingFan.getSpeed(ceillingFan.speed)
        ceillingFan.high()
    }
    
    func undo() {
        switch prevSpeed {
        case .OFF:
            ceillingFan.off()
        case .LOW:
            ceillingFan.low()
        case .MEDIUM:
            ceillingFan.medium()
        case .HIGH:
            ceillingFan.high()
        }
    }
}

class CeillingFanMediumCommand: Command {
    let ceillingFan: CeilingFan
    var prevSpeed: CeilingFan.Speed
    
    init(_ ceillingFan: CeilingFan) {
        self.ceillingFan = ceillingFan
        self.prevSpeed = ceillingFan.speed
    }
    
    func execute() {
        prevSpeed = ceillingFan.getSpeed(ceillingFan.speed)
        ceillingFan.medium()
    }
    
    func undo() {
        switch prevSpeed {
        case .OFF:
            ceillingFan.off()
        case .LOW:
            ceillingFan.low()
        case .MEDIUM:
            ceillingFan.medium()
        case .HIGH:
            ceillingFan.high()
        }
    }
}
    
var remote = RemoteControl()

let ceilingFan = CeilingFan()
let ceillingFanOnCommand = CeillingFanOnCommand(ceilingFan)
let ceillingFanOffCommand = CeillingFanOffCommand(ceilingFan)
let ceillingFanLowCommand = CeillingFanLowCommand(ceilingFan)
let ceillingFanMediumCommand = CeillingFanMediumCommand(ceilingFan)
let ceillingFanHighCommand = CeillingFanHighCommand(ceilingFan)

remote.setCommand(0, ceillingFanMediumCommand, ceillingFanOffCommand)
remote.setCommand(1, ceillingFanHighCommand, ceillingFanOffCommand)
remote.setCommand(2, ceillingFanLowCommand, ceillingFanOffCommand)

remote.onButtonWasPushed(0)
remote.offButtonWasPushed(0)
remote.toString().forEach { print($0) }
remote.undoButtonWasPushed()
remote.onButtonWasPushed(1)
remote.toString().forEach { print($0) }
remote.undoButtonWasPushed()
/*
 거실 선풍기 속도는 OFF입니다.
 거실 선풍기 속도가 MEDIUM로 설정되었습니다.
 거실 선풍기가 꺼졌습니다.
 ------ 리모컨 ------
 [slot 0] CeillingFanMediumCommand       CeillingFanOffCommand
 [slot 1] CeillingFanHighCommand         CeillingFanOffCommand
 [slot 2] CeillingFanLowCommand          CeillingFanOffCommand
 [slot 3] NoCommand                      NoCommand
 [slot 4] NoCommand                      NoCommand
 [slot 5] NoCommand                      NoCommand
 [slot 6] NoCommand                      NoCommand
 [ undo ] CeillingFanOffCommand

 거실 선풍기 속도가 MEDIUM로 설정되었습니다.
 거실 선풍기 속도는 MEDIUM입니다.
 거실 선풍기 속도가 HIGH로 설정되었습니다.
 ------ 리모컨 ------
 [slot 0] CeillingFanMediumCommand       CeillingFanOffCommand
 [slot 1] CeillingFanHighCommand         CeillingFanOffCommand
 [slot 2] CeillingFanLowCommand          CeillingFanOffCommand
 [slot 3] NoCommand                      NoCommand
 [slot 4] NoCommand                      NoCommand
 [slot 5] NoCommand                      NoCommand
 [slot 6] NoCommand                      NoCommand
 [ undo ] CeillingFanHighCommand

 거실 선풍기 속도가 MEDIUM로 설정되었습니다.
 Program ended with exit code: 0
 */
```
  </p>
</details>

## 8. 여러 동작을 한 번에 처리하기 
- 다른 커맨드를 실행할 수 있는 새로운 종류의 커맨드를 생성합니다. 좋은 아이디어죠. 

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
class MacroCommand: Command {
    var commands: [Command]
    
    init(commands: [Command]) {
        self.commands = commands
    }
    
    func execute() {
        commands.forEach {
            $0.execute()
        }
    }
    
    func undo() {
        commands.forEach {
            $0.undo()
        }
    }    
}
    
var remote = RemoteControl()

let light = Light()
let tv = TV()
let stereo = Stereo()
let hottub = Hottub() //온수 욕조

let lightOn = LightOnCommand(light)
let stereoOn = StereoOnWithCDCommand(stereo)
let tvOn = TVOnCommand(tv)
let hottubOn = HottubOnCommand(hottub)

let lightOff = LightOffCommand(light)
let stereoOff = StereoOffCommand(stereo)
let tvOff = TVOffCommand(tv)
let hottubOff = HottubOffCommand(hottub)

let partyOn: [Command] = [ lightOn, stereoOn, tvOn, hottubOn ]
let partyOff: [Command] = [ lightOff, stereoOff, tvOff, hottubOff ]
let partyOnMacro = MacroCommand(commands: partyOn)
let partyOffMacro = MacroCommand(commands: partyOff)

remote.setCommand(0, partyOnMacro, partyOffMacro)

remote.toString().forEach { print($0) }
print("--- 매크로 ON ---")
remote.onButtonWasPushed(0)
print("--- 매크로 OFF ---")
remote.offButtonWasPushed(0)
print("--- 매크로 Undo ---")
remote.undoButtonWasPushed()

/*
 ------ 리모컨 ------
 [slot 0] MacroCommand                   MacroCommand
 [slot 1] NoCommand                      NoCommand
 [slot 2] NoCommand                      NoCommand
 [slot 3] NoCommand                      NoCommand
 [slot 4] NoCommand                      NoCommand
 [slot 5] NoCommand                      NoCommand
 [slot 6] NoCommand                      NoCommand
 [ undo ] NoCommand

 --- 매크로 ON ---
 조명이 켜졌습니다.
 거실 오디오가 켜졌습니다.
 거실 오디오에서 CD가 재생됩니다.
 거실 오디오의 볼륨이 11으로 설정되었습니다.
 거실 TV가 켜졌습니다.
 욕조가 켜졌습니다.
 욕조 온도를 40도로 설정합니다.
 --- 매크로 OFF ---
 조명이 꺼졌습니다.
 거실 오디오가 꺼졌습니다.
 거실 TV가 꺼졌습니다.
 욕조가 꺼졌습니다.
 --- 매크로 Undo ---
 조명이 켜졌습니다.
 거실 오디오가 켜졌습니다.
 거실 오디오에서 CD가 재생됩니다.
 거실 오디오의 볼륨이 11으로 설정되었습니다.
 거실 TV가 켜졌습니다.
 욕조가 켜졌습니다.
 Program ended with exit code: 0
 */
```
  </p>
</details>

## 8. 생각해보기
- 커맨드 패턴을 활용하면 커맨드를 받는 큐를 만들어서 A 작업을하다가 전혀 상관없는 B 작업을 하게 할 수 있습니다. 
- 커맨드 패턴을 활용하면 로그기록기를 생성해서 복구 시스템을 구축할 수 있습니다.
![](https://velog.velcdn.com/images/dev_kickbell/post/76cf2851-7a53-4434-bb4a-e5588fc4496e/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/1259f6e7-a415-4067-81f9-22b4e916c440/image.png)

## 9. 실무에서의 커맨드 패턴 구현하기
- 커맨드 패턴 적용 전의 코드 		
    - 하이브리드 앱을 구현 중입니다. 그리고 브릿지가 연결된 상태이죠. 
    - 웹뷰에서 어떤 이벤트가 올 때마다 SNS 공유하기, 새 창열기, 현재 창 닫기 등 많은 이벤트가 발생합니다. 
    - 예시 case는 3개지만 실제로는 20개가 넘고, 앞으로도 더 생길 수 있죠. 코드마다 최소 20줄에서 최대 100줄 이상도 갈 수가 있구요. 이러면 앱의 유지보수가 너무 힘들어집니다. 계속 기존 코드 부분을 바꿔야 하구요. 
    
<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
extension WebViewController: WKScriptMessageHandler {
    func userContentController(
        _ userContentController: WKUserContentController,
        didReceive message: WKScriptMessage
    ) {
    	//예시는 3개이지만 실제로는 case 20개+@ 
        switch message.name {
        case "destoryView":
	    	//코드마다 20 ~ 100 line.
        case "openNewWindow":
	    	//코드마다 20 ~ 100 line.
        case "shareSNS":
	    	//코드마다 20 ~ 100 line.
        default: break
        }
    }
}
```
  </p>
</details> 

- 커맨드 패턴 적용 후의 코드 
    - 해야 될 작업을 각각 커맨드 객체로 묶습니다.  그리고 dictionary를 생성해서 키값으로 해당 커맨드를 가져오죠. 마지막으로 단 1줄로 execute()를 호출합니다. 
    - 이렇게 하면 나중에 브릿지가 추가되도 WebViewController의 다른 부분은 건드리지 않아도 됩니다. 새로 커맨드를 만들고, commands에 맞는 키값으로 만든 커맨드 서브 클래스를 넣어주기만 하면 되겠죠. 코드 가독성도 훨씬 뛰어나네요. 

<details>
  <summary><a href="https://github.com/kickbell/pb"></a></summary>
  <p>

```swift
extension WebViewController: WKScriptMessageHandler {
    let commands = [
        "destoryView": DestoryViewCommand(),
        "openNewWindow": DestoryViewCommand()
        "shareSMS": DestoryViewCommand()
    ]

    // MARK: Internal
    func userContentController(
        _ userContentController: WKUserContentController,
        didReceive message: WKScriptMessage
    ) {
        commands[message.name]?.execute(vc: self, message: message)
    }
}

protocol Command {
    func excute(vc: UIViewController, message: WKScriptMessage)
}

struct DestoryViewCommand: Command {
    func excute(vc: UIViewController, message: WKScriptMessage) {
        vc.dismiss(animated: true)
    }
}

struct ShareSMSCommand: Command {
    func excute(vc: UIViewController, message: WKScriptMessage) {
        guard let body = message.body as? [String: String] else { return }
        guard let text = body["text"] else { return }
        guard MFMessageComposeViewController.canSendText() else {
            print("메세지를 전송할 수 없습니다.")
            return
        }
        let messageVC = MFMessageComposeViewController()
        messageVC.recipients = []
        messageVC.body = text
        vc.present(messageVC, animated: true)
    }
}

struct OpenNewWindowCommand: Command {
    func excute(vc: UIViewController, message: WKScriptMessage) {
        guard let body = message.body as? [String: String] else { return }
        guard let link = body["link"] else { return }
        guard let url = URL(string: link) else {
            print("유효하지않은 URL입니다.")
            return
        }
        let safari = SFSafariViewController(url: url)
        vc.present(safari, animated: true)
    }
}
```
  </p>
</details> 



## Reference 
[https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223](https://www.hanbit.co.kr/store/books/look.php?p_code=B6113501223)





