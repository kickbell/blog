# Swift 문법 정리

## Integer Types

```swift
//Int8의 8은 비트를 뜻한다. 범위를 기억할필요는 없다. 아래와같이 실행해보면 된다.
Int8.min
Int8.max
Int16.min
Int16.max
Int32.min
Int32.max
Int64.min
Int64.max

//만약에 범위가 아니라 메모리의 크기를 알아보고 싶다면 아래처럼 하면 된다.
MemoryLayout<Int8>.size // 1이 출력되므로 1바이트의 메모리 크기를 가진다는 뜻이다.
MemoryLayout<Int16>.size
MemoryLayout<Int32>.size
MemoryLayout<Int64>.size

Int8.min
Int8.max

UInt8.min
UInt8.max

//특별한 이유가 없다면 주로 Int를 사용하게 된다. 이유는 정수를 가장 빠르게 처리할 수 있기 때문이다.
//32비트 컴퓨터를 사용한다면 Int 크기는 4바이트가 되고, 64비트를 사용한다면 8바이트가 되는데 iOS나 MacOS는 모두 64비트의 환경이므로 8바이트이다.
MemoryLayout<Int>.size
Int.min
Int.max
```

## Floating-point Types

```swift
/*:
 실수자료형은 2가지만 제공되며 각각 4,8바이트로 제공되며 양수음수 따로 구분되지 않는다.
 1. 항상 정수자료형보다 큰 값을 저장할 수 있다.
 이게 무슨말이냐면 Int의 메모리크기는 8바이트이고 저장가능한 범위는 -900경 ~ +900경이다.
 */
Int.min
Int.max

/*:
 그러나 Float의 메모리크기는 4바이트지만 Int보다 더 큰 값을 저장할 수 있다. 왜? 지수와 가수로 나누어서 저장하기 때문에. 이말만 들으면 Float는 Int보다 더 적은 메모리크기를 차지하면서 더 큰 값을 저장할 수 있으니 더 좋아보인다. 하지만 그렇지 않다. 꽤나 큰 단점이 있다.
 2. 메모리의 크기에 따라서 소수점의 정확도가 달라진다.
 Float는 저장할 때 최대 소수점 6자리까지만 정확하고 나머지는 오차가 발생할 수 있다.
 마찬가지로 Double은 최대 15자리까지만 정확성을 보장하고 그 이상은 오차가 발생할 수 있다.
 */

let pi: Float = 3.141592653589793238462643383279502884197169
print(pi)

/*:
 결과를보면 우측에는 3.141593, 콘솔에는 3.1415927로 표현되어 있다. 자동으로 반올림하고 안한것의 차이이다.
 
 아래 더블은 똑같이 출력되지만, 이것만은 명확히 기억하면 된다.
 
 Float 정확성은 최대 6자리, Double은 최대 15자리이다.
 */

let dPi: Double = 3.141592653589793238462643383279502884197169
print(dPi)
```

## Type Safety

Swift는 형식 안정성을 보장하기 위해서 자료형을 엄격히 구분한다.

```swift
//let str: String = 123

//let num: Int = 12.34

//let a = 7 //타입 추론으로 Int로 적용
//let b: Int8 = a //error

//let a = 12
//let b = 34.56
//let result = a + b //사람은 계산할 수 있지만 Swift는 타입이 다르기 때문에 계산할 수 없다.

//objc 같은 경우에는 int rate = 1.94; 를 작성했을 때 아무런 경고도 출력하지 않고 정상적으로 동작한다.
//다만 int에 1.94를 저장할 수 없으니 .94를 날려버리고 1만 저장한다. 그러니 결과값에 오류가 생긴다.
//만약 프로그램이 은행이자율을 계산하는 프로그램이고 해당 적금에 2만명이 가입했다면, 그 여파는... 상상할 수 없을 정도로 크다. Swift는 이런 것들을 에러로 판단하고 미리 잡아준다.

let rate = 1.94
let intRate = Int(1.94)
let amt = 10_000_000
type(of: rate)
type(of: amt) //Int

Int(rate * Double(amt))
Int(rate) * amt // rate가 intRate가 되면 .94가 날아가서 결과값 오류가 발생한다.
```

## Type Alias

기본 자료형에 새로운 이름을 추가하는 문법, 새로운 자료형을 만드는 것은 아니라는 점을 주의 활용하기에 따라서 코드의 가독성을 높일 수 있다. Upper Camel Case를 사용한다.

```swift
typealias Coordinate = Double

let lat: Coordinate = 12.34
let lon: Coordinate = 56.78
```

```swift
typealias ArithmeticFunction = (Int, Int) -> Int

//사칙연산
func add(_ a: Int, _ b: Int) -> Int {
    return a + b
}
func subtract(_ a: Int, _ b: Int) -> Int {
    return a - b
}
func multiply(_ a: Int, _ b: Int) -> Int {
    return a * b
}
func divide(_ a: Int, _ b: Int) -> Int {
    return a / b
}

func selectFunction(from op: String) -> ArithmeticFunction? {
    switch op {
    case "+":
        return add(_:_:)
    case "-":
        return subtract(_:_:)
    case "*":
        return multiply(_:_:)
    case "/":
        return divide(_:_:)
    default:
        return nil
    }
}
```

## Operators

### truncatingRemainder(dividingBy:)

```swift
//실수의 나머지를 구하는 메소드
12.3456.truncatingRemainder(dividingBy: 4) //0.34599999...
```

## Short-circuit Evaluation

스위프트는 논리연산자를 평가할 때 아래 연산자처럼 &&일때 앞이 false면 && 뒤는 평가하지 않는다. 마찬가지로 || 연산에서도 앞이 true면 뒤는 평가하지 않는다. 이런 방식을 단락평가라고 한다.

```swift

var a = 1
var b = 1

func updateLeft() -> Bool {
    a += 1
    return true
}

func updateRight() -> Bool {
    b += 1
    return true
}

if updateLeft() || updateRight() {
    
}

a //2
b //1

//결과값을 보면 b는 1이다. 즉 updateRight는 호출되지 않았다. 단락평가때문이다.

if updateLeft() && updateRight() {
    
}

a //3
b //2

//기존 값이 2,1 이었기 때문에 각각 1씩 증가해서 3,2가 되었다.


var c = 1
var d = 1

func newUpdateLeft() -> Bool {
    a += 1
    return false
}

func newUpdateRight() -> Bool {
    b += 1
    return true
}

if newUpdateLeft() && newUpdateRight() {
    
}

c
d

//newUpdateLeft이 false이기 때문에 newUpdateRight는 호출되지 않았으므로 1,1이다.
```

## Switch - Where

```swift
//패턴에 조건 추가 where 키워드
//패턴이 케이스에 매칭되었을 때, 컨디션을 통해 다시 한 번 확인
//패턴이 일치하고, 컨디션이 true 여야만 실행된다.

let num = 1

switch num {
case let n where n <= 10:
   print(n)
default:
   print("others")
}
```

## Switch - FallThrough

case 값이 충족했다면 바로 case 문을 빠져나가는 것이 아니라, 다음 case 문의 값과 상관없이 다음 case 문의 블록을 실행하고 case 문을 빠져나간다. 만약 다음 case 조건이 없고 default가 있다면 default를 실행하고 종료된다.

```swift
let num = 2

switch num {
case 1:
   print("one")
case 2:
   print("two")
   fallthrough
//스위치 문을 빠져나가는 것이 아니라, 다음 케이스문의 블록으로 바로 이동한다.
//되게 특이한 점이 case 3:을 신경쓰지않고 그냥 바로 three를 출력해버린다.
//매우 주의할 점이다.
//또 하나는 다음 블록인 case 3:을 출력하고 난 후에 case 4, default에
//들어가지 않고 바로 케이스문이 종료된다는 점이다.
case 3:
   print("three")
case 4:
   print("four")
default:
   print("default")
   break
}


//이럴때 활용할 수 있다.
//로그인을 시도해서 실패했을 때
//1. 10번 이하 경고
//2. 10번 워닝
//3. 10번 이상이면 리셋
//인데 10번 일때, 초과를 띄워주고 처음화면으로 리턴해야하는 2개의 작업을 원래대로라면 써주어야 한다.
//10번 과는 별개로 디폴트도 적어줘야하고.
//근데 fallthrough를 하면 어차피 디폴트를 타니까 저 처음화면 코드를 없애줘도 된다는 점?
//쓸것도 같고 안쓸것도 같고..

let attempts = 10

switch attempts {
case ..<10:
   print("비밀번호가 틀렸습니다. 남은 횟수 ??")
case 10:
   print("비밀번호 틀린 횟수를 초과하였습니다.")
//   print("처음화면으로 돌아갑니다.")
   fallthrough
default:
    print("처음화면으로 돌아갑니다.")
}
```

## For-In

### stride(from:to:by)

```swift
//짝수만 출력하고 싶을 때
//from : 시작할범위
//to : 종료할범위(실제범위에는 포함되지 않음)
//by : from에서 by만큼 증가된 새로운 시퀀스가 리턴된다.
for num in stride(from: 0, to: 10, by: 2) {
    print(num) //0,2,4,6,8
}
```

### stride(from:through:by)

```swift
//through : 종료범위에 포함됨
for num in stride(from: 0, through: 10, by: 2) {
    print(num) //0,2,4,6,8,10
}

for num in stride(from: 0, through: 11, by: 2) {
    print(num) //0,2,4,6,8,10
}

for num in stride(from: 11, through: 0, by: -2) {
    print(num) //11,9,7,5,3,1
}

for num in stride(from: 0, through: 11, by: -2) {
    print(num) //not excute
}
```

### multiplication table

```swift
for i in 2 ... 9 { //i 반복문이 한 번 실행될 때마다, j반복문은 9번씩 실행된다.
    for j in 1 ... 9 {
        print("\(i) * \(j) = \(i * j)")
        // \() 이거의 이름은 문자열 보간법(string Interpolation)
    }
}
```

### star - left triangle

```swift
*
**
***
****
*****

//1
for i in 1...5 {
    for _ in 1...i {
        print("*", terminator: "")
    }
    print()
}

//2
var star = ""
for _ in 1...5 {
    star += "*"
    print(star)
}

//3
for i in 1...5 {
    print(String(repeating: "*", count: i))
}
```

```swift
*****
****
***
**
*

//1
for i in 1...5 {
    print(String(repeating: "*", count: 6-i))
}

//2
for i in 1...5 {
    for _ in 1...6-i {
        print("*", terminator: "")
    }
    print()
}
```

### star - right triangle

```swift
    *
   **
  ***
 ****
*****

for i in 1...5 {
    for _ in 1..<6-i {
        print(" ", terminator: "")
    }
    for _ in 5-i..<5 {
        print("*", terminator: "")
    }
    print()
}
```

```swift
*****
 ****
  ***
   **
    *
    
for i in 1...5 {
    for _ in 1..<i {
        print(" ", terminator: "")
    }
    for _ in i...5 {
        print("*", terminator: "")
    }
    print()
}
```

### star - center triangle

```swift
   *
  ***
 *****
*******

for i in 0...3 {
    for _ in stride(from: 2, through: i, by: -1) {
        print(" ", terminator:"")
    }
    for _ in stride(from: 0, through: 2*i, by: 1) {
        print("*", terminator:"")
    }
    print("")
}

*******
 *****
  ***
   *
   
for i in 0...3 {
    for _ in stride(from: 1, through: i, by: 1) {
        print(" ", terminator:"")
    }
    for _ in stride(from: 6, through: 2*i, by: -1) {
        print("*", terminator:"")
    }
    print("")
}
```

### star - diamond

```swift
   *
  ***
 *****
*******
 *****
  ***
   *
   
 for i in 0...3 {
        for _ in stride(from: 2, through: i, by: -1) {
            print(" ", terminator:"")
        }
        for _ in stride(from: 0, through: 2*i, by: 1) {
            print("*", terminator:"")
        }
        print("")
    }
for i in 1...3 {
        for _ in stride(from: 1, through: i, by: 1) {
            print(" ", terminator:"")
        }
        for _ in stride(from: 6, through: 2*i, by: -1) {
            print("*", terminator:"")
        }
        print("")
    }
```

### star - pinwheel

```swift
```

## Repeat-while

코드를 먼저 실행한 다음에 컨디션을 평가한다.

```swift
var num = 100
while num < 100 {
    num += 1
}
num
// 100

num = 100
repeat {
    num += 1
} while num < 100
num
// 101 repeat를 먼저 실행하기 때문에 결과값이 다르다.
```

## Control Transfer Statements

조건문, 반복문에서 일반적인 코드의 흐름을 바꾸기 위해 사용한다. break, continue, fallthrough, return, throw 제어를 전달한다는 것은 현재 실행중인 스코프에서 코드를 중지하고 다음에 실행할 코드를 바로 실행한다는 것이다.

### break

반복문, 스위치문에서 사용된다. 문장을 종료한다. 문장을 종료하는데 가장 인접한 문장만 종료하고 이어서 실행된다.

```swift
let num = 2

switch num {
case 1...10:
    print("begin block")
    if num == 2 {
        break //switch문을 종료한다음 문장을 제어를 이어지는 코드로 전달한다.
    }
    print("end block")
default:
    break //블록에서 어떠한 명령도 실행할 필요가 없을 때에도 사용한다.
}
print("done")

begin block
done
```

```swift
for index in 1...10{
    print(index)
    
    if index > 1 {
        break
    }
}

1
2
```

```swift
for i in 1...3{
    print("OUTER LOOP", i)
    for j in 1...10 {
        print("   inner loop", j)
        if j > 1 {
            break //문장을 종료하는데 가장 인접한 문장만 종료한다.
        }
    }
}

OUTER LOOP 1
   inner loop 1
   inner loop 2
OUTER LOOP 2
   inner loop 1
   inner loop 2
OUTER LOOP 3
   inner loop 1
   inner loop 2
```

### continue

현재 실행중인 반복을 중지하고 다음 반복으로 이동한다. 문장을 중지하지는 않는다. 가장 인접한 문장에 영향을 준다. 무엇을 종료하고 무엇을 계속하는지만 구분하면 되겠다. break는 switch, loop에서 사용했지만 continue는 loop에서만 사용된다.

```swift
for index in 1...10 {
    if index % 2 == 0 {
        continue //현재 실행중인 반복을 중지하고 다음 반복으로 이동한다. 문장을 중지하지는 않는다.
    }
    print(index)
}

1
3
5
7
9
```

```swift
for i in 1...3 {
    print("OUTER LOOP", i)
    for j in 1...5 {
        if j % 2 == 0 {
            continue //가장 인접한 문장에 영향을 준다.
        }
        print(" inner loop", j)
    }
}

OUTER LOOP 1
 inner loop 1
 inner loop 3
 inner loop 5
OUTER LOOP 2
 inner loop 1
 inner loop 3
 inner loop 5
OUTER LOOP 3
 inner loop 1
 inner loop 3
 inner loop 5
```

### labeled statement

문장에 이름을 붙이고 :(콜론)을 붙인다. 주로 제어문과 반복문이 중첩된 코드에서 가장 인접한 문장이 아니라 원하는 문장을 종료하고 싶을때 주로 사용된다. break, continue와 함께 사용되고 반복문, if문, switch문에서 주로 사용된다.

```swift
outer: for i in 1...3 {
    print("OUTER LOOP", i)
    for j in 1...3 {
        print(" inner loop", j)
//        break
//        여기에서 j 반복문이아니라 i 반복문을 종료하고 싶다면 어떻게 해야할까?
//        아래처럼 종료하고 싶은 반복문에 이름을 붙이고 break 뒤에 이름을 명시해준다.
//        이런게있었네.. 겁나신기하다..ㅋㅋ
        break outer
    }
}

OUTER LOOP 1
 inner loop 1
```

## Optionals

```swift
let num: Int? = 345
num //345
345 == num //true 

재밌는 점, 저 위에가 true라는 것. 
num의 타입은 Int?지만 현재는 num에 345라는 값이 들어가 있으므로 
345 == num 이 true가 가능하다.
```

```swift
//값을 바꿔야 한다면 변수로 바인딩 하는 것도 상관없다.
num = 123
if var num = num {
    num = 456
    print(num)
}
```

### implicitly unwrapped optionals

```swift
let num: Int! = 12

let a = num
type(of: a) // Int?, IUO는 타입추론을 사용하는 경우에는 자동으로 추출되지 않는다.

let b: Int = num
type(of: b) // Int, 타입 어노테이션을 사용하였을 때는 자동으로 추출된다.

num에 값이 있고, 할당한 변수가 타입 어노테이션이 되어있으면 자동으로 추출된다. 
타입추론인 경우에는 자동으로 추출되지 않는다. 
```

```swift
let n: Int! = nil

let c: Int = n
//Unexpectedly found nil while implicitly unwrapping an Optional value
타입 어노테이션을 사용해도 에러가 발생한다. 왜? IOU는 값에 nil이 있는지 여부를 확인하지 않고 
그냥 값이 있다는 가정하에 nil을 추출하려고 해버리는거다. 근데 그게 안되니까 에러를 발생시키는거지.
```

### optional pattern

```swift
let a: Int? = 0 

//옵셔널 패턴
if case let x? = a {
   print(x)
}

//옵셔널 바인딩
if let x = a {
   print(x)
}
//옵셔널 바인딩과 결과가 같다.
//복잡하기만 한데 왜 있고 왜 쓸까? 코드가 좀 더 단순해 진다. 어떻게? 더보자.
```

```swift
let list: [Int?] = [0, nil, nil, 3, nil, 5]

//옵셔널 바인딩
for item in list {
   guard let x = item else { continue }
   print(x)
}

//옵셔널 패턴
for case let x? in list {
   print(x)
}

0
3
5 
결과는 똑같다. 근데 한 줄이 줄었다. 뭔가 약간 코드가 더 짧아진다. 
for in 문안에서 list를 let으로 상수로 바인딩해서 출력한다. 
여기서 x?하면 [Int?]인 list가 바인딩되서 추출된다. x?는 Int 타입. 밑에 x는 Int 타입이다.
근데 ?를 빼면 x는 Int?타입이다. 이러면 밑에 x는 Int? 타입이다. 

정리하자면 두 코드의 차이는 이렇다. 일단 결과값은 똑같다. 
하지만, 옵셔널 바인딩을 쓴 코드는 nil이 들어왔을 때 else문이 실행되서 continue로가서 
다시 for-in문을 돌지만, 옵셔털 패턴같은 경우에는 
1.일단 가드문이 없어서 코드가 더 간결해졌고, 
2.값이 nil이라면(바인딩되지않았다면) 바로 그냥 다음 반복문으로 넘어간다.
```

## Functions

함수이름은 주로 동사가 포함된다. 아규먼트 레이블은 주로 to in with 와 같은 전치사가 포함된다. 그리고 파라미터 네임에는 주로 명사가 사용된다. 파라미터가 1개라면 name은 Parameter Name임과 동시에 Argument Label이다. 파라미터가 2개라면 앞에가 Argument Label이고 뒤에가 Parameter Name이다. Argument Label를 사용하는 이유는 가독성을 높이기 위해서이다. Parameter Name은 함수 바디에서 사용된다. Argument Label은 함수를 호출할 때 사용된다.

### variadic parameters

하나의 파라미터로 두 개이상의 파라미터를 전달할 수 있다. 아규먼트는 함수 내부로 전달할 때 배열형태로 전달된다. 가변파라미터는 함수마다 1개만 쓸 수 있다. 가변파라미터는 보통 처음이나 마지막에 선언한다. 중간에는 잘 선언하지 않는다. 가변파라미터는 기본값을 가질 수 없다. 전달할 수 있는 아규먼트수가 고정되어 있지 않기 때문에 가변파라미터라고 부른다.

```swift
func printSum(of nums: Int...) {
    var sum = 0
    for num in nums {
        sum += num
    }
    print(sum)
}
printSum(of: 1, 2, 3)
printSum(of: 1, 2, 3, 4, 5)

nums 타입이 [Int]라고 Int...를 [Int]로 바꾸면 에러난다. 
```

### in-out parameters

아규먼트로 전달된 함수의 값을 직접 바꿀 수 있다. 타입 앞에 inout을 적어준다. 호출 할때는 &을 적어준다. copy-in, copy-out 메모리 모델을 사용한다. copy-in : 변수에 저장된 값을 복사해서 전달한다. copy-out : 함수가 종료되면 함수에서 변경한 값이 아규먼트로 전달한 변수에 복사된다.

```swift

var num1 = 12
var num2 = 34

func swapNumber(_ a: inout Int, with b: inout Int) {
    let tmp = a
    a = b
    b = tmp
}

num1 //12
num2 //34

swapNumber(&num1, with: &num2)

num1 //34
num2 //12

특이한 점은 일반 함수 내에서는 파라미터인 a,b가 상수이지만 여기서는 그게 아니라는 것이다. 
또한 지금은 a = b, b = tmp로 해줬지만 함수내에서 a를 불러보면 inout이라는 타입의 a와 Int의 a로 2개의 타입이 나온다. 
그리고 실제로 a = 999 라는 식으로 값을 넣어도 잘들어간다. 함수에서 제일 마지막으로 999로 값을 변경했으니 전역변수 num1을 출력해보면 999로 바뀌어있다.
```

In-Out 자주하는실수, 제약조건

같은 변수를 전달한다. 상수를 전달한다. 리터럴 값을 전달한다. 리터럴은 값을 저장하고 있는 메모리 공간조차 없다. 함수의 기본값을 지정할 수 없다. 기본값은 리터럴값인데 위와 마찬가지로 메모리 공간조차 없다. 또한 기본값에 메모리 공간을 가진 변수도 지정할 수 없다. 그냥 문법적으로 기본값이 허용되지 않는다. 가변 파라미터는 inout 키워드에서 사용될 수 없다.

### function types

함수를 변수나 상수에 저장할 수 있다. 파라미터로 전달할 수도 있다. 함수에서 리턴할 수도 있다.

```swift
func printHello(with name: String) {
    print("hello, \(name)")
}

let f2: (String) -> () = printHello(with:) 
//함수를 변수에 넣을 수 있다.

let f3 = printHello(with:)
//타입 추론도 가능하다.

f3("World") //함수를 호출할 때는 아규먼트 레이블이 꼭 필요하지만, 이렇게 변수나 상수에 저장된 함수를 호출할 때에는 아규먼트 레이블이 필요하지 않다. 타입인 (String) -> ()만 저장되기 때문이다.

f3(with: "World") //이러면 에러가 발생한다. 
```

```swift
func add(a: Int, b: Int) -> Int {
    return a + b
}

var f4: (Int, Int) -> Int = add(a:b:)

f4(1, 2)
```

### nonreturning functions - never

리턴 타입 중에 하나인데, 음.. fatalError()하고 비슷한 느낌인 것 같다. 실제로 fatalError() 또한 리턴타입이 Never이긴 한데, 저 녀석이 있으면 함수가 정상적으로 리턴되지 않는다? 그리고 프로세스가 즉시 종료된다 뭐 그런의미인 것 같다. 사용예시는 아래와 같으나 솔직히 잘 모르겠다.

```swift
public func fatalError(_ message: @autoclosure () -> String = String(), file: StaticString = #file, line: UInt = #line) -> Never

//가드문에서 Never를 활용하는 방법
func terminate() -> Never {
//    Never가 있다면 컴파일러가 프로그램을 종료하거나 에러를 전달하도록 강제한다.
    fatalError("positive only")
}

func doSomething(with value: Int) -> Int {
    guard value >= 0 else {
        //보통 else문은 return문이나 Nonreturning function을 작성한다.
        terminate()
    }
    
    return 0
}
```

### @discardableResult

리턴 값이 있는데 사용하지 않으면 Swift는 경고를 표시해준다. 그러나 리턴 값이 있어도 사용하지 않을 수도 있는 거다. 그럴 때 함수 위에 @discardableResult를 명시해주면 해당 경고가 사라진다. 다른 방법도 있는데 @discardableResult를 굳이 쓰지않고, 와일드카드 패턴을 이용해서 \_ = saySomething() 라고 해줘도 그만이다.

```swift
@discardableResult
func saySomething() -> String {
   return "Hello"
}
```

## Closures

### capturing values

값을 캡처한다는 그냥 값을 가져와서 쓴다고 이해해도 괜찮다. 클로저 내부에서 클로저 외부에 있는 값에 접근하면 클로저는 값을 캡쳐(그냥 값을 가져와서 쓴다)한다. 값을 캡쳐할 때, objc에서는 값의 복사본을 가져와서 쓴다. 반면에 swift에서는 값의 참조를 캡쳐한다. 이말은 값의 원본값을 그대로 가져온다는 말이다. 그래서 클로저에서 가져온 값을 바꾸면 원래 값도 같이 변경된다.

정리하자면, 클로저 내부에서 외부에 있는 값에 접근하면 값의 참조를 획득한다. 내부에서 값을 바꾼다면 외부에 있는 원래값도 함께 바뀐다. 그렇기 때문에 클로저에서 값을 캡쳐할 때 메모리관리를 하지 않는다면 참조 사이클 문제가 발생한다.

```swift
var num = 0

let c = {
    num += 1
}

c()

print(num) 
1 
클로저는 값의 참조를 획득하고, num += 1을 해줬으므로 전역변수인 num의 값도 바뀌는 것을 볼 수 있다. 
같은 이유로 메모리 관리를 해줘야만 한다. 
```

### escaping closure

탈출하는 클로저. 무엇으로부터 탈출해? 함수흐름으로부터! @escaping 키워드를 사용한다. 함수 내부에서 실행되는 클로저는 디폴트값으로 NonEscaping Closure이다. 이 말은, 함수가 시작되고나서 클로저가 실행되고 함수가 종료되기전에 클로저가 완료된다는 뜻이다. 그러면 반대로 Escaping Closure는 뭘까? Escaping Closure 는 함수의 실행이 종료되어도 호출될 수 있다는 것을 말한다. 즉, 시작시점과 종료시점이 특정되지 않는다. 함수가 시작되면 호출되는 것은 같지만 함수가 실행되는 중간에 함수가 실행될 수도 있고, 함수실행이 종료된 후에 실행될 수도 있다. 또, 함수 종료도 마찬가지로 함수 실행이 끝난 후에 종료될 수도 있다.

```swift

func performNonEscaping(closure: () -> ()){
    print("start")
    closure()
    print("end")
    print("\n\n\n")
}
performNonEscaping {
    print("closure")
}
//클로저를 실행해보면, 아래의 순서대로 값이 출력된다.
//start
//closure
//end
//함수바디에서 실행하고 있는 클로저는 항상 함수실행이 완료되기전에 호출된다.
//이게 일반적이며, 이것을 탈출하지 않는 클로저 NonEscaping Closure라고 부른다.

func performEscaping(closure: @escaping () -> ()) {
    print("start")
    let a = 12
    DispatchQueue.main.asyncAfter(deadline: .now() + 3) {
        closure()
        print(a)
    }
    print("end")
}
//이번에는 클로저를 3초 후에 실행하려고 코드를 조금 바꿨다. 그리고 a 변수를 추가했다.
//이것이 원래대로 논에스케이핑 클로저라면 에러가 발생한다.
//이유는 논에스케이핑 클로저라면 함수가 끝나기 전에 종료되어야 하는데, DispatchQueue를 사용해서 함수가 실행되고 3초 후에 클로저가 실행되도록 했으니 컴파일러가 에러라고 인식하는 것이다.
//따라서, 함수의 종료 흐름과 관계없이 실행해야 하니 논에스케이핑 -> 에스케이핑 클로저로 바꿔주어야 한다.
//그러기 위해서는 클로저 타입 앞에 @escaping 키워드를 붙여준다.
//그리고 클로저를 호출해주면

performEscaping {
    print("closure")
}

//start
//end
//위 2개가 먼저 출력되고, 3초후에 아래두개가 출력된다.
//closure
//12


파라미터의 생명주기는 함수의 실행이 시작되면 생성되었다가, 함수의 실행이 종료되면 제거된다.
변수의 생명주기 또한 파라미터와 동일하다.
그런데, 클로저가 논에스케이핑 클로저라면 당연히 함수가 종료되면 클로저도 함께 종료된다.
그 말은 함수흐름과 관계없이 클로저를 실행하고 싶을 때, 함수가 종료되면 클로저가 실행되지 못하고 삭제된다는 것을 뜻한다.
그리고, 이전에 배웠던 것처럼 클로저 내부에서 외부에 있는 변수를 캡쳐하는데 변수의 생명주기 또한 파라미터와 같으니 함수가 종료될 때 삭제된다. 그러면 정작 클로저가 호출될 때 변수값이 없으니 쓸 수가 없게 되는 것이다.
그래서 탈출 클로저를 사용한다.
심지어 탈출 클로저를 사용한다면, 클로저가 캡쳐한 값이 해당 클로저가 완료되기 전까지는 삭제되지 않는다. 그래서 클로저의 흐름이 함수흐름을 벗어나더라도 메모리에러없이 정상적으로 실행된다.
```

### @autoclosure

파라미터로 전달되는 표현식을 표현식을 클로저로 래핑하는 단순한 특성이다. {} brace 를 안써도 되게 해주는 것으로 이해하면 쉽다. @autoclosure 특성을 선언하면 리턴타입은 자유롭지만, 파라미터는 항상 비워둬야 한다.

```swift
func notAutoClosure(param: () -> Int) { //클로저 전달
    print(param(), "notAutoClosure")
}
func useAutoClosure(param: @autoclosure () -> Int) { //클로저 전달
    print(param(), "useAutoClosure")
}

notAutoClosure {
    return 1111
}

useAutoClosure(param: 2222)

//1111 notAutoClosure
//2222 useAutoClosure
```

## Tuple

### tuple decomposition

```swift
let data = ("<html>", 200, "ok", 12.34)
//let body = data.0
//let code = data.1
//let message = data.2
//let size = data.3
//이렇게 해도 되지만,

let (body, code, message, size) = data
//Tuple Decomposition을 사용하는 것이 훠얼씬 더 편하다.
//이렇게 분해한 데이터는 그냥 글로벌 변수로 사용하면 된다.
body
code
message
size

//만약에 2개만 분해하고 싶다면 와일드카드 패턴을 쓰면 된다.
let (body2, code2, _, _) = data
body2
code2
```

### tuple matching

튜플매칭과 인터벌매칭, 밸류바인딩패턴, 스위치문을 활용하면 if문보다 코드가 훨씬 더 깔끔해진다.

```swift
let resolution = (1920.0, 1080.0)

//if문 사용
//가로로 너무길고, 새로운 조건을 추가할때마다 else if 를 추가해야해서 복잡하다.
if resolution.0 == 3840 && resolution.1 == 2160 {
    print("4K")
}else if condition{
}else if condition{
}else if condition{
}else...

//tuple, switch, 인터벌 매칭, 밸류 바인딩 패턴 사용
switch resolution {
case let(w, h) where w / h == 16.0 / 9.0: //밸류 바인딩 패턴을 이용한 매칭
    print("16:9")
case (_, 1080) : //높이가 1080인 모든것을 매칭
    print("1080p")
case (3840...4096, 2160): //4K는 4096인 경우도 있다. 이것도 된다. 이것은 인터벌 매칭이다!
    print("4K")
default:
    break
}
```

## String

### string types

NSString과 String은 호환된다. 개발자문서에서는 두 형식을 브릿징이 가능한 타입이라고 설명하고 있다. 두 자료형은 호환되지만 유니코드를 처리하는 방식이 다르다는 것은 알아야 한다. 다만 타입캐스팅이 필요하다.

```swift
var nsstr: NSString = "str"
let swiftStr: String = nsstr as String //String
nsstr = swiftStr as NSString //NSString
```

### multiline string literal

멀티라인은 """ \~ """ 같은 방식으로 명시적인 줄바꿈을 허용한다. 멀티라인을 시작할 때 """ 이후에 바로 문자열이 시작한다면 에러가 발생한다. 멀티라인의 내용은 항상 새로운 라인에서 시작해야 한다. 또 마지막에 있는 큰따옴표 또한 한줄에 단독으로 작성해야 한다. 마지막에 있는 큰따옴표는 항상 텍스트와 동일선상에 있거나 왼쪽에 있어야한다. 멀티라인에서 문장의 끝에 (백슬러쉬)를 추가하면 명시적으로 줄바꿈이 되어있어도 줄바꿈이 되지 않는다.

### escape sequences

백슬래시( \ ) 뒤에 한 문자나 숫자 조합이 오는 문자 조합을 "이스케이프 시퀀스"라고 합니다. 줄 바꿈 문자, 작은따옴표, 또는 문자 상수의 다른 특정 문자를 나타내려면 이스케이프 시퀀스를 사용해야 합니다. 이스케이프 시퀀스는 단일 문자로 간주되므로 문자 상수로 유효합니다. \n 과 같은것들 !

```swift
//str = "\"
//unterminated string literal
//백슬래쉬는 특별한 문자로 사용된다. 문자열 보간법에서 \(size)도 사용되고, 이스케이프 시퀀스(\n과 같은)에서도 사용되기 때문에 에러가 발생한다.

str = "\\"
print(str) //두개 적어야 하나만 출력된다. 이게 정상이다.

//자주 사용하는 이스케이프 시퀀스
print("A\tB") //탭추가
print("C\nD") //줄바꿈
print("\"Hello\" He said.") //"" 추가
print("\'Hello\' He said.") //'' 추가

\
A	B
C
D
"Hello" He said.
'Hello' He said.
```

### raw string

Raw String은 \와 "를 문자그대로 처리한다. = Raw String을 활용하면 특수문자 등을 쉽게 추가할 수 있고, 문자열의 가독성이 증가한다. = String Interpolation 같은 경우에도 로우 스트링일때는 이스케이프 시퀀스가 인식되지 않기 떄문에 #을 추가해줘야 한다. = 정규식 문자열을 로우스트링으로 처리하면 불필요한 백슬래쉬가 사라지기 때문에 문자열을 더욱 직관적으로 작성할 수 있는 장점이 있다.

```swift
var str = "\"Hello\", Swift 5"
print("===== str =====")
print(str)
//큰따옴표를 추가하고 싶다면 큰따옴표 왼쪽에 \(백슬러쉬)를 추가한다.

var rawStr = #"\"Hello\", Swift 5"#
print("===== rawStr =====")
print(rawStr)
//#을 추가하고 출력해보면 양쪽에 \가 같이 나온다. 즉, raw string이 된다.
//raw string은 \와 "를 문자그대로 처리한다.

rawStr = #""Hello", Swift 5"#
print("===== newRawStr =====")
print(rawStr)
//Raw String을 활용하면 특수문자 등을 쉽게 추가할 수 있고, 문자열의 가독성이 증가한다.
//정규식 문자열을 직관적으로 작성할 수 있는 장점이 있다.

===== str =====
"Hello", Swift 5
===== rawStr =====
\"Hello\", Swift 5
===== newRawStr =====
"Hello", Swift 5
```

```swift
//그냥 스트링이고 이스케이프 시퀀스가 포함되어 있는 문자
str = "Hello\nSwift"
print(str)

//로우 스트링인데 이스케이프 시퀀스가 포함되어 있는 문자
str = #"Hello\nSwift"#
print(str)

//로우 스트링인데 이스케이프 시퀀스를 사용하고 싶어. 줄바꿈 사이에 #
str = #"Hello\#nSwift"#
print(str)

//로우 스트링으로 만들 때 #의 갯수를 여러개 쓸 수도 있는데(아마도 가독성을 위해?), 여러개 쓴다면 그 갯수를 맞춰줘야 한다. 전부 3개
str = ###"Hello\###nSwift"###
print(str)

Hello
Swift
Hello\nSwift
Hello
Swift
Hello
Swift
```

```swift
let value = 123
str = "The value is \(value)"
rawStr = #"The value is \#(value)"#
//String Interpolation 같은 경우에도 로우 스트링일때 이스케이프 시퀀스가 인식되지 않기 떄문에 #을 추가해줘야 한다.
print("===== str =====")
print(str)
print("===== rawStr =====")
print(rawStr)

===== str =====
The value is 123
===== rawStr =====
The value is 123
```

```swift
//정규식에서의 로우스트링
//간단한 우편번호 검증 정규식
var zipCodeRegex = "^\\d{3}-?\\d{3}$"
//\\d처럼 메타문자를 그대로 백슬러쉬로 출력하면 이스케이프 시퀀스로 인식된다. 그래서 원래는 \d인데 \\d로 백슬러쉬를 2개를 적어야 한다.
//그래서 다른 언어에서 넘어왔다면 정규식 표현방법이 약간 다르기 때문에 혼란을 줄 수 있다.

zipCodeRegex = #"^\d{3}-?\d{3}$"#
//이렇게 로우스트링으로 처리하면 불필요한 백슬래쉬가 사라지기 때문에 문자열의 가독성이 높아진다.

let zipCode = "123-456"
if let _ = zipCode.range(of: zipCodeRegex, options: [.regularExpression]) {
    print("valid")
}

valid
```

### string interpolation

문자열을 동적으로 구현하는 방법이다. 코드가 간결해진다. 단점으로는 자동으로 타입을 바꿔주기 때문에 원하는 포맷을 지정할 수는 없다. (예를들어 size를 소수점 첫째자리까지 표현한다던지..) 그럴때는 아래의 포맷지정자를 사용한다.

```swift
var str = "12.34KB"
let size = 12.34
str = String(size) + "KB"
str = "\(size)KB" //문자열 보간법을 사용하면 자동적으로 size가 string으로 바뀐다.
```

### string interpolation(swift 5+)

```swift
기존에 하던 방식, CustomStringConvertible를 활용했다. 
//내가 원하는 출력
//1.2 x 3.4

struct Size {
    var width = 0.0
    var height = 0.0
}

extension Size: CustomStringConvertible {
    var description: String {
        return "\(width) x \(height)"
    }
}
let s = Size(width: 1.2, height: 3.4)
print(s)
print("\(s)")


Size(width: 1.2, height: 3.4) //이런 포맷은 디버깅에는 적합하지만, 사용자를 대상으로하는 출력에는 적합하지 않다.
1.2 x 3.4
```

```swift
새로 도입된 방식, String.StringInterpolation 구조체에 메서드를 추가하는 방법이다.

extension String.StringInterpolation {
    mutating func appendInterpolation(_ value: Size) {
//        appendLiteral(<#T##literal: String##String#>) 문자열 리터럴을 추가할 수 있다.
//        appendInterpolation(<#T##value: CustomStringConvertible & TextOutputStreamable##CustomStringConvertible & TextOutputStreamable#>) StringInterpolation에 결과문자열에 새로운 문자열을 추가할 수 있다.
        appendInterpolation("\(value.width) x \(value.height)")
    }
    
    mutating func appendInterpolation(_ value: Size, style: NumberFormatter.Style) {
        let formatter = NumberFormatter()
        formatter.numberStyle = style
        
        if let width = formatter.string(for: value.width),
           let height = formatter.string(for: value.height) {
            appendInterpolation("\(width) x \(height)")
        }else{
            appendInterpolation("\(value.width) x \(value.height)")
        }
    }
}

//차이점은 description은 속성, 구현한 appendInterpolation은 메소드이다.
//따라서 파라미터를 전달할 수 있다는 뜻이다.
//그래서 이런것도 가능하다? 는 것 같다.

print("\(s)")
print("\(s, style: .spellOut)")
print("\(s, style: .currency)")
print("\(s, style: .percent)")


1.2 x 3.4
one point two x three point four
$1.20 x $3.40
120% x 340%
```

### format specifier

https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Strings/Articles/formatSpecifiers.html#//apple\_ref/doc/uid/TP40004265-SW1

```swift

str = String(format: "%.1fKB", size)

String(format: "Hello, %@", "Swift") //문자열은 %@

String(format: "%d", 12) //정수

String(format: "%d", 12.34) //12가 아니라 0이 나온다.

String(format: "%f", 12.34) //실수, 소수점 6자리 기본

String(format: "%.3f", 12.34) //실수, 소수점 3자리

str = String(format: "%10.3f", 12.34) //실수, 전체문자열을 10자리로 출력 후 그중에서 소수점을 3자리
str.count //보이는건 10자리가 안되보여도 찍어보면 10자리다.

String(format: "%010.3f", 12.34)
//실수, 전체문자열을 10자리로 출력 후 그중에서 소수점을 3자리인데 빈공간을 0으로 채운다. 000012 6자리 + .340 4자리
//% 다음에 오는 0은 빈문자열을 대체하고, 그 다음 숫자는 전체 자릿수를 지정한다.
//0 을 제외한 다른 숫자나 문자는 적용되지 않는다.

String(format: "%410.3f", 12.34) //12.340
String(format: "%?10.3f", 12.34) //"?10.3f"


String(format: "[%d]", 123)
String(format: "[%10d]", 123) //양수는 오른쪽정렬
String(format: "[%-10d]", 123) //음수는 왼쪽정렬

//포맷 지정자의 순서
let firstName = "Jong-Chan"
let lastName = "Kim"

var korFormat = "나의 이름은 %@ %@ 입니다."
var engFormat = "My name is %@ %@."

//성과 이름이 이름부터 출력되었다. 한국은 반대인데.
String(format: korFormat, firstName, lastName)
String(format: engFormat, firstName, lastName)

korFormat = "나의 이름은 %2$@ %1$@ 입니다."
engFormat = "My name is %1$@ %2$@."

//1$ 2$는 순서를 바꿔줍니다. 유용하다.
String(format: korFormat, firstName, lastName)
String(format: engFormat, firstName, lastName)
```

### string indices

* string index는 정수타입이 아니다.
* endindex는 마지막의 다음 인덱스다. 따라서 마지막인덱스를 가져오기 위해 index(before:) 메소드를 이용한다.
* 잘못된 인덱스를 사용하면 크래쉬가 발생한다.

```swift
let str = "Swift"

//str.startIndex + 1 //정수 타입이 아니므로 불가
str.startIndex...str.endIndex //가능
//str[str.startIndex...str.endIndex] //endindex는 마지막의 다음 인덱스므로 에러발생 Fatal error: String index is out of bounds
let lastIdx = str.index(before: str.endIndex) //after: 도 지원한다.
str[str.startIndex...lastIdx]//이런식으로 해야함, Swift
str[str.startIndex...] //가능, Swift
str[..<str.endIndex] //가능, Swift

let wantIdx = str.index(str.startIndex, offsetBy: 2)
str[str.startIndex...wantIdx] //원하는 인덱스는 이런식으로, Swi
```

### string basic

```swift

var str = "Hello, Swift String"


var emptyString = ""
emptyString = String()

//문자열 생성자는 주로 다른값을 바꿀때 사용한다.
let a = String(true) //된다, "true"

let b = String(12) // "12"

let c = String(12.34) // "12.34"

let d = String(str)

let hex = String(123, radix: 16) //16진수로 변경, 7b

let octal = String(123, radix: 8) //8진수로 변경, 173

let binary = String(123, radix: 2) //2진수로 변경, 1111011

let repeatStr = String(repeating: "👏", count: 7) //"👏👏👏👏👏👏👏"
    
let unicode = "\u{1f44f}" //"👏", 이런건 이모지가져와서 원하는 이모지 선택 후, 문자정보복사

let e = "\(a) \(b)" //"true 12"

let f = a + " " + b //"true 12"

str += "!!" //"Hello, Swift String!!"

str.count //21

str.isEmpty //false

str == "Apple" //false
"apple" != "Apple" //대소문자를 구별한다.

"apple" < "Apple" //기본적으로 사전순서로 비교하는데 문자가 같다면 문자코드로 판단, 아스키코드의 소문자 a숫자(121)가 대문자 A(89)보다 크다.

str.lowercased() //소문자, ed나 ing로 끝나는 메소드는 원본은 건드리지 않는다.

str.uppercased() //대문자

"apple ipad".capitalized //각 단어의 첫번째 문자를 대문자로 바꿔준다.

//Swift에서는 문자열을 문자 집합(Character Sequence)이라고 한다. 그렇기 때문에 for-in문이 사용가능하다.
for char in "Hello" {
    print(char)
}

let num = "1234567890"
num.randomElement() //랜덤요소 방출

num.shuffled() //섞기
```

### 문자열 연결

```swift
var newStr = "Hello"
newStr.append("??") //"Hello??"
newStr //"Hello??", 원본활용

let n = newStr.appending("!!")// 새값생성
newStr //"Hello, ??"
n //"Hello, ??!!"
//appending은 새 문자열 생성, 보통 동사로 메소드가 생성되어있으면 원본을 활용하고 -ed, -ing가 적혀있다면 복사하여 새 값을 생성한다.
```

### 원하는 포맷으로 문자열을 연결

```swift
//자세한 포맷 종류는 건 formar specifier에
"File size is ".appendingFormat("%.1f", 12.3456) //"File size is 12.3", 실수 소수점 1자리
"File size is ".appendingFormat("%d", 100) //"File size is 100", 정수
```

### 문자열을 중간에 연결

```swift
//2개의 메소드를 지원한다.
//둘다 파라미터가 String.Index 타입이다.
//Char, Collection을 받을 수 있다.
var newStr = "Hello Swift"
newStr.insert(",", at: newStr.index(newStr.startIndex, offsetBy: 5))
newStr.insert(contentsOf: ["?","!","$"], at: newStr.index(newStr.startIndex, offsetBy: 5))

if let sIndex = newStr.firstIndex(of: "S") {
    newStr.insert(contentsOf: "Awesome", at: sIndex)
}
newStr //"Hello, AwesomeSwift"
```

### 문자열의 특정문자, 범위를 삭제

```swift

var str = "Hello, Awesome Swift!!!"

let lastCharIndex = str.index(before: str.endIndex)

var removed = str.remove(at: lastCharIndex)
//삭제 후 삭제된 문자를 리턴
//remove의 리턴형은 Character?이 아니고 Character이기 때문에 잘못된 인덱스를 전달하면 크래쉬가 발생하니 주의해야 한다.
removed
str

removed = str.removeFirst() // 마찬가지로 삭제된 문자를 리턴
removed
str

str.removeFirst(2) // 삭제된 문자를 리턴하지는 않고 결과값을 리턴
str

str.removeLast(2) // 삭제된 문자를 리턴하지는 않고 결과값을 리턴
str

if let range = str.range(of: "Awesome") {//범위로도 삭제 가능
    str //"lo, Awesome Swift"
    str.removeSubrange(range) //return void
    str //"lo, Swift"
}

str.removeAll() //파라미터없이 호출하면 메모리공간도 삭제한다.

str.removeAll(keepingCapacity: true) //만약에 삭제후에 새로운 값을 넣어야 한다면 이 옵션을 사용한다. 메모리공간이 삭제되지 않는다.
//capacity (메모리 공간의)용량
```

### drop

* str.dropLast() //Substring
* str.dropFirst() //Substring
* str.drop(while:) //Substring
* drop으로 시작하는 메소드들은 리턴값이 Substring 이므로 원본의 메모리를 공유한다.

```swift
str = "Hello, Awesome Swift!!!"

var substr = str.dropLast()
//원본 문자열에서 마지막 요소를 제외한 모든 문자열을 새로운 문자열로 리턴한다.
//메모리 공간은 없다. 원본 문자열과 같은 메모리 공간을 공유한다.

substr = str.dropLast(3) //dropFirst()는 안해봐도 알겠지?
//마찬가지인데 뒤에서 3개 삭제

substr = str.drop(while: { (ch) -> Bool in
    return ch != ","
}) //while이 true가 리턴되는동안 문자를 삭제한다.
substr
```

### 문자열의 일부를 교체

```swift

var str = "Hello, Objective-C"

//범위를 교체
if let range = str.range(of: "Objective-C") {
    str.replaceSubrange(range, with: "Swift") //return void
}

str // "Hello, Swift"


if let range = str.range(of: "Hello") {
    let s = str.replacingCharacters(in: range, with: "Hi")
    
    s //Hi, Swift", -ing이므로 새롭게 문자를 생성
    str //원본은 그대로 유지
}


// "Hello, Swift"  ->  "Hello, Awesome Swift", 성공
var s = str.replacingOccurrences(of: "Swift", with: "Awesome Swift")
s // "Hello, Awesome Swift"


// "Hello, Swift"  ->  "Hello, Awesome Swift", 실패
// 대소문자구분, 실패하면 원본을 그대로 출력한다.
s = str.replacingOccurrences(of: "swift", with: "Awesome Swift")
s // "Hello, Swift"


/*:
 #### 대소문자를 구분하지 않도록 옵션 추가
 ---
 */
//대소문자를 구분하지 않도록 옵션 추가도 가능하다.
//성공
s = str.replacingOccurrences(of: "swift", with: "Awesome Swift", options: [.caseInsensitive])
s // "Hello, Awesome Swift"
```

### 문자열 비교

```swift
let largeA = "Apple"
let smallA = "apple"
let b = "Banana"

//아스키 코드는 이런식으로 확인이 가능하다.
let a:Character = "a"
let A:Character = "A"
a.asciiValue //97, Character에서만 지원하는 프로퍼티
A.asciiValue //65

largeA == smallA

largeA != smallA

largeA < smallA //대문자 A 아스키코드가 소문자 a 보다 작다.

largeA < b // B 아스키코드가 A 보다 크다.

smallA < b //대문자 B 아스키코드가 소문자 a 보다 작다.

largeA.compare(smallA) == .orderedSame //대소문자 구분, false

largeA.caseInsensitiveCompare(smallA) == .orderedSame //대소문자 구분안함, true

largeA.compare(smallA, options: .caseInsensitive) == .orderedSame //대소문자 구분안함, true 

/*
 @frozen public enum ComparisonResult : Int {
     case orderedAscending = -1
     case orderedSame = 0
     case orderedDescending = 1
 }
 */
 
 //접두어, 접미어 비교 
let str = "Hello, Swift Programming"
let prefix = "Hello"
let suffix = "Programming"

str.hasPrefix(prefix) //접두어 비교, 대소문자구분, true

str.hasSuffix(suffix) //접미어 비교, 대소문자구분, true

//hasPrefix, hasSuffix 에서는 caseInsensitive 이 없기 때문에 만약에 비교대상의 대소문자가 다르다면 아래처럼 일치시켜서 비교한다.
str.lowercased().hasPrefix(prefix.lowercased()) //대소문자 일치시켜서 비교, true 
```

### 문자열 검색

```swift

let str = "Hello, Swift"
let str2 = "Hello, Programming"
let str3 = str2.lowercased() //"hello, programming"


//contains
str.contains("Swift") //true
str.lowercased().contains("swift".lowercased()) //true


//range, return 값으로 범위가 필요할 때도 있음
str.range(of: "Swift")
str.range(of: "swift", options: .caseInsensitive) //대소문자 무시한다.


//commonPrefix
str //"Hello, Swift"
str2 //"Hello, Programming"
var common = str.commonPrefix(with: str2) //"Hello, ", 두 문자열에서 공통된 부분만 새 문자열로 리턴해준다.

str.commonPrefix(with: str3) //"", 대소문자를 구분하기 때문에 빈문자열을 리턴

//특이한 점은 리턴값이 str3이아니라 메소드 호출대상에 포함되어있는 str의 값을 리턴한다.
//대소문자 구분안하는 옵션추가
str.commonPrefix(with: str3, options: .caseInsensitive) //"Hello, "
str3.commonPrefix(with: str, options: .caseInsensitive) //"hello, "
```

### 문자열 분리

```swift
var str = "Hello, playground"


//components
//제공되는 타입이 2개라서 Char, CharSet도 되고 String으로도 나눌 수 있다.
//리턴값은 [String]

let c = str.components(separatedBy: ",")
str //"Hello, playground"
c //["Hello", " playground"]

//파라미터가 CharacterSet이다. 그래서 이렇게도 된다.
let ct = "1+2-3*4/5".components(separatedBy: ["+","-","*","/"])
ct //["1", "2", "3", "4", "5"]

//사실 위에보다 이렇게 만들어서 하는게 낫다. 
//1~0까지 포함한 Char를 가진 CharacterSet을 만든다. 구조체다. 
//여기서 뭐가 포함됐는지를 검색한다던지, 컴포넌트를 기준으로 분리하든지 한다. 
let charSet = CharacterSet(charactersIn: "1234567890")
"324ㄴㅁ아럼ㄴ아러44443".components(separatedBy: charSet)

let ct2 = "1+2-3*4/5".components(separatedBy: ["+","-"])
ct2 //["1", "2", "3*4/5"]

let s = str.components(separatedBy: "pl")
s //["Hello, ", "ayground"]


//split
//리턴값 다르다 [String.SubSequence]

let s1 = "1234".split(separator: "3")
s1 //["12", "4"]


let line = "BLANCHE:   I don't want realism. I want magic!"

//공백으로 나누되 공백이 여러개여도 1개인것마냥 다나눈다.
line.split(whereSeparator: { $0 == " " })
// Prints "["BLANCHE:", "I", "don\'t", "want", "realism.", "I", "want", "magic!"]"

//공백으로 나누되 공백 1개만 인정
line.split(maxSplits: 1, whereSeparator: { $0 == " " })
// Prints "["BLANCHE:", "  I don\'t want realism. I want magic!"]"

//공백이 여러개면 그걸 ""로 반환해주는거같은데? 
line.split(omittingEmptySubsequences: false, whereSeparator: { $0 == " " })
// Prints "["BLANCHE:", "", "", "I", "don\'t", "want", "realism.", "I", "want", "magic!"]"

//Complexity: O(n), where n is the length of the collection.
```

## Collections

데이터 모음을 효율적으로 사용하기 위한 자료형이다. Swift에서는 아래와 같은 3가지의 컬렉션을 제공한다.

> 1. Array : 순서대로 저장되는 컬렉션

1. Dictionary : 키와 값이 쌍으로 연결된 컬렉션
2. Set : 수학에서 공부하는 집합연산을 제공하는 컬렉션

Swift에서 사용할 수 있는 컬렉션은 크게 2가지로 나뉜다.

> 1. Foundation Collection : 예전부터 사용되었던 컬렉션 클래스. - 컬렉션에 저장하는 하나의 데이터는 Element라고 부른다. - 컬렉션을 참조형식으로 처리해야 할 때 사용한다. - NSArray, NSDictionary, NSSet - Object(객체)형태만 저장이 가능하다. 문자열, 숫자 등을 저장하고 싶다면 NSNumber, NSValue 등을 사용해서 객체형식으로 바꿔주어야 한다. - 자료형에 제한이 없다. 하나의 컬렉션에 문자객체와 숫자객체를 저장하는 것도 가능하다. - 가변컬렉션(NSMutableArray, NSMutableDictionary, NSMutableSet)과 불변컬렉션(NSArray, NSDictionary, NSSet)을 별도로 제공한다. - 불변컬렉션에 가변변수의 값은 얼마든지 변경이 가능하지만, 가변컬렉션에 불변변수의 값을 변경이 불가능하다.

1. Swift Collection : Swift에서 새로 도입된 컬렉션 구조체.
   * 컬렉션에 저장하는 하나의 데이터는 Element라고 부른다.
   * 값형식
   * Array, Dictionary, Set
   * Object(객체)형태와 Value(값)형태 둘 다 저장가능하다.
   * 동일한 자료형의 데이터만 저장해야 한다. 그랴소 선언 시점에 저장할 데이터의 자료형을 명시적으로 선언해야한다.
   * 키워드로 가변(var)과 불변(let)을 결정한다.
   * 불변컬렉션에 가변변수의 값은 얼마든지 변경이 가능하지만, 가변컬렉션에 불변변수의 값을 변경이 불가능하다.
   * Swift는 값형식이기 때문에 컬렉션에서 값을 복사한다. 하지만 상대적으로 문자열,숫자에 비해서 큰 데이터를 저장한다. 그렇기 때문에 반드시 복사가 필요한 경우에만 실제 복사를 실행하는 Copy-on-write 최적화 방식을 활용한다. 컬렉션이 변경되지 않는다면 항상 동일한 데이터를 사용하다가, 변경된다면 그 시점에 새로운 데이터를 생성하고 변경을 저장한다.

### Array

### Dictionary

### Set

## Property

### Stored Properties

아래와 같이 어떤 값을 선언하고 할당하는 것을 저장 프로퍼티라고 한다. 프로퍼티는 구조체, 클래스 내부에 구현할 수 있다.

```swift
var name = "jong chan" 
let num = 3 
```

### Lazy Stored Properties

지연 저장 프로퍼티라고 부른다. 지연 저장 프로퍼티는 초기화를 지연시킨다. 즉, 인스턴스가 초기화 되는 시점이 아니라 속성에 처음 접근하는 시점에 초기화된다. 항상 변수(var) 저장 속성으로 선언해야 한다. lazy let이 안된다는 말이다. 생성자에서 초기화하지 않기 때문에 선언시점에 기본값을 저장해야 한다. 이미지가 필수가 아니라 선택일 때 지연 저장 속성을 사용하면 불필요한 메모리 공간을 줄일 수 있다.

```swift
lazy var attachment: Image = Image()
```

### Computed Properties

연산 프로퍼티라고 부른다. 프로퍼티는 구조체, 클래스, 열거형 내부에 구현할 수 있다. 수학적으로 계산된다는 뜻이 아니라 다른 속성값을 기반으로 속성값이 결정된다는 뜻이다. 저장 속성은 메모리 공간을 가지지만, 계산 속성은 메모리 공간을 가지지 않는다. 다른 속성에 저장된 값을 읽어서 필요한 계산을 실행한 다음 리턴하거나 속성으로 전달된 값을 다른 속성에 저장한다. 계산때마다 다른 결과가 나올 수 있기 때문에 항상 var로 선언해야 한다. 읽기전용으로는 구현할 수 있지만, 쓰기 전용으로는 구현할 수 없다. 읽기전용으로 구현하려면 get만 작성해주면 되고 생략도 가능하다. 읽기, 쓰기 모두 가능하게 하려면 get 블럭과 set블럭을 모두 구현해주면 된다. set(name)과 같은 식으로 파라미터를 사용할 수 있다. 생략하면 `newValue` 키워드를 사용한다. 읽기전용 계산 속성은 get과 brace가 생략되기 때문에 클로저라고 생각할 수 있다. 그럴땐 자료타입 뒤에 할당연산자(=)가 있나없나를 보면 알 수 있다.(=이 있으면 클로저)

> **읽기 전용(set이 없는) 연산 프로퍼티**

```swift
class Person {
    var name: String
    var yearOfBirth: Int
    init(name: String, year: Int) {
        self.name = name
        self.yearOfBirth = year
    }
    //태어난 연도를 기반으로 현재 나이를 계산해서 저장한다.
    //클로저와 구분하는 법은 Int 뒤에 =(할당연산자)가 있나 없나가 중요하다.
    var age: Int {
        let calendar = Calendar.current
        let now = Date()
        let year = calendar.component(.year, from: now)
        return year - yearOfBirth
    }
}
let p = Person(name: "Jong Chan", year: 2002)
p.age
//getter가 실행되면서 값이 저장된다.
//다른 속성에 저장된 값을 읽어서 필요한 계산을 실행한 다음 리턴하거나 속성으로 전달된 값을 다른 속성에 저장한다.
```

> **읽기 / 쓰기가 둘 다 있는 연산 프로퍼티**

```swift
class Person {
    var name: String
    var yearOfBirth: Int
    init(name: String, year: Int) {
        self.name = name
        self.yearOfBirth = year
    }
    //age를 선언하고 yearOfBirth 속성으로 나이를 계산해서 리턴한다.
    var age: Int {
        get {
            //현재 날짜에서 연도를 추출하고 속성을 뺀 값을 리턴
            let calendar = Calendar.current
            let now = Date()
            let year = calendar.component(.year, from: now)
            return year - yearOfBirth
        }
        set {
            //age 속성에 전달된 값을 기반으로 출생년도를 계산하고 있다.
            //그런 다음 계산된 결과를 yearOfBirth에 저장한다.
            let calendar = Calendar.current
            let now = Date()
            let year = calendar.component(.year, from: now)
            yearOfBirth = year - newValue //여기서는 파라미터를 사용하지 않았으므로 age로 전달되는 값이 newValue로 사용된다.
        }
    }
}
let p = Person(name: "Jong Chan", year: 2002)
p.age //getter 실행
p.yearOfBirth //2002
p.age = 50 //setter 실행
p.yearOfBirth //새롭게 저장된 1970
```

## Keypath String Expression

먼저 Keypath, KVC, KVO 부터 대충 알아보자.

> **Keypath** 문자열 키를 사용해서 속성에 접근하는 기술의 Key 부분을 말한다. Key Value Cording(KVC)와 Key Value Observing(KVO)의 근간을 이루는 개념이다. Keypath의 key도 딕셔너리처럼 문자열이다. 하나이상의 키가 점으로 연걸된 형태(a.b.c)이다. 키를 통해서 특정 속성에 접근한다. Keypath를 통해서 속성에 접근하기 위해서는 2가지 조건이 필요하다.

1. NSObject 클래스 상속
2. 속성 앞에 @objc 특성을 추가

```swift
//NSObject을 상속하고 있고 모든 속성에는 @objc 속성이 추가되어 있다.
class Person: NSObject {
   @objc let name: String = "Jane Doe"
   @objc var age: Int = 0
}
//이런 식으로 문자열 키(name)통해 값을 가져왔다. 
//리턴값은 Any?타입
//키를 문자열로 전달. 여기서 속성인 name을 잘못쓰면 크래쉬가 발생했다. 
//그래서 더이상 쓰지 않고, Keypath String Expression이 나왔다. 
let p = Person()
p.value(forKey: "name") 
```

> **KVC(Key Value Cording)** 문자열 키를 사용해서 속성에 접근하는 기술

> **KVO(Key Value Observing)** Swift의 `프로퍼티 옵저버(willSet, didSet)`처럼 변수가 변경될 때마다 관찰해서 코드가 실행되도록 하는 기능이다. 실제로 동작이 유사하기도 하지만, 프로퍼티 옵저버가 내가 만든 타입에서 정의해서 사용해야 하는 것과는 달리 KVO는 외부 라이브러리같은 내가 건드리지 못하는 곳에서 해당 속성을 옵저빙하고 싶을 때 사용하곤 한다. KVO는 Objective-C 런타임에 의존하기 때문에, 순수한 swift 코드에서는 그리 좋지 않다. NSObject를 상속받은 @objc를 사용해야 하고, 각각의 프로퍼티에 @objc dynamic 표시를 해야한다.

어쨋건, `p.value(forKey: "name")`와 같은 식으로 키 값을 틀릴 수 있으니 나온 것이 바로 `Keypath String Expression`이다. 아래와 같은 식으로 keyPath를 생성한다. 잘못된 키패스는 컴파일 에러가 발생하기 때문에 미리 알 수 있다. 리턴 값이 String이라서 사용은 똑같이 할 수 있다. 하지만, 단점은 생성한 리턴 값이 `Any?`이기 때문에 타입캐스팅을 해주어야 한다. 그래서 이 점을 보완해서 나온 것이 `Keypath Expression`이다.

```swift
var keypath = #keyPath(Person.name) //String

let r = p.value(forKey: keypath)
let s = p.value(forKeyPath: keypath)
```

## Keypath Expression

Keypath의 리턴값이 `Any?`라서 타입캐스팅이 필요했었다. 이 부분을 보완하기 위해 나온 기술이다. 한마디로 정의하면 **백슬래시(\\)와 닷(.)으로 원하는 속성까지 점으로 연결하는 표현식**이다. Keypath Types을 사용한다. Keypath Types이 제네릭으로 구현되어 있기 때문에 타입캐스팅 없이 저장된 값을 바로 사용할 수 있다.

> **Keypath Types** 스위프트가 제공하는 특별한 타입이다. 문자열로 된 키패스에 비해 다양한 기능을 제공한다. 두 개의 타입이 있는데 루트타입과 접근하는 속성의 타입이다. 종류는 아래와 같다. 자세한 건 공식문서를 보도록 하자. 딱히 안봐도 이해하는데 문제는 없다. ![](https://images.velog.io/images/dev\_kickbell/post/d6113892-39ff-4018-b101-57b4ce3a46c6/image.png)

자, 그래서 어떻게 Keypath Expression을 사용할까?

```swift
struct Person {
   let name: String = "Jane Doe"
   var age: Int = 0
}

var p = Person()

1. 키패스 타입 생성
//Keypath String Expression와는 다르게 `\루트타입.속성타입.속성타입..`식으로 연결한다.
//아래 보는 것 처럼 일단 Keypath Types을 생성한다. 
//var, let, struct, class에 따라서 키패스 타입이 지정된다. 각자 할 수 있는 일이 약간씩 다르다.
let keyPathToName = \Person.name // KeyPath<Person, String>
let keyPathToAge = \Person.age // WritableKeyPath<Person, Int>

2. 사용(속성에 접근)  
let nameValue = p[keyPath: keyPathToName] // "Jane Doe"

3. 값변경 
let ageValue = p[keyPath: keyPathToAge]
p[keyPath: keyPathToAge] = 1000

4. 문자열에 접근해서 길이를 확인
//count라는 속성이 있는게 아니지만 이런식으로도 KeyPath<Person, Int> 타입의 키패스 타입을 
//만들어서 Int타입인 8을 가져올 수 있다. 약간 map?같기도하다. String -> Int가 되었으니.. 
var ketPathToLength = \Person.name.count // KeyPath<Person, Int>
p[keyPath: ketPathToLength] //문자열의 길이를 리턴 "Jane Doe"의 길이 8

//.count 하지 않고 아래 .appending 메소드를 사용할 수도 있다. 결과는 같다. 
ketPathToLength = keyPathToName.appending(path: \.count)
p[keyPath: ketPathToLength]
```

## Keypath String Expression Vs Keypath Expression

![](https://images.velog.io/images/dev\_kickbell/post/a875512c-57f8-4c18-b0d9-1d06bf297502/image.png)
