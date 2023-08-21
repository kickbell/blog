# 파이썬과 스위프트 문법 비교

### 1. 문자열 출력하기

화면에 "Mary's cosmetics"를 출력하라. (중간에 '가 있음에 주의.)

> #### python

```python
sum = 0
for num in range(1, 11):
    sum += num
print (sum)
```

> #### swift

```swift
sum = 0
for num in range(1, 11):
    sum += num
print (sum)
```

### 2. 문자열 출력하기

화면에 "Mary's cosmetics"를 출력하라. (중간에 '가 있음에 주의.)

> #### python

```python
print("chan\'s computer")
print("\'를 쓰려면 백쿼드(`)아니고 작은따옴표와 \를 붙여써야해")
```

> #### swift

```swift
print("chan's computer")
print("chan`s computer")
print(#"chan's "super" computer"#)
print("chan's \"super\" computer")
```

### 3. 포맷 연산자

print 함수를 사용하여 3.141592의 값을 출력하라. 단, 출력된 값이 소수점 아래로 두 자리 숫자까지만 표시되도록 하라. 실행 예: 3.14

> #### python

```python
digit = 3.141592 
print("%.4f" % digit)
```

> #### swift

```swift
let digit = 3.141592
let newDigit = String(format: "%.4f", digit)
print(newDigit)
```

### 4. 사용자 입력

사용자로부터 두 개의 숫자를 입력받은 후 두 개의 숫자를 더한 값을 출력하는 프로그램을 작성하라. 실행 예: first: 3 second: 4 합: 7

> #### python

```python
first = input() 
second = input() 
result = int(first) + int(second)
print("결과값", result)
```

> #### swift

```swift
let first = readLine() ?? ""
let second = readLine() ?? ""
let add = (Int(first) ?? 0) + (Int(second) ?? 0)
print("결과값 : \(add)")
```

### 5. 사용자 입력 (반복문을 배우는 이유)

사용자로부터 출력하고자 하는 문자열과 반복 횟수를 4로 입력받았다고 가정하기, 문자열을 반복 횟수(4번)만큼 출력하는 프로그램을 작성하라. 실행 예: 문자열: hello 반복횟수: 4 "hellohellohellohello"

> #### python

```python
# 줄마침표 :, 다음줄 띄어쓰기, 0부터 시작 중요  
count = input() 
for i in range(int(count)):
	print("hello", i)
```

> #### swift

```swift
let count = Int(readLine() ?? "") ?? 0
for i in 0..<count {
    print("hello", i)
}
```

### 6. 형 변환

문자열 '720'를 정수형으로 변환하라. 정수 100을 문자열 '100'으로 변환하라.

> #### python

```python
string1 = "720"
digit1 = 100 
print(int(string1)) #문자를 넣어서 실패하면 에러 
print(str(digit1))
```

> #### swift

```swift
let string1 = "720"
let digit1 = 100
print(Int(string1) ?? 0)
print(String(digit1))
```

### 7. 사칙 연산

사용자로부터 두 개의 숫자를 입력 받은 후 두 숫자의 덧셈(+), 뺄셈(-), 곱셈(\*), 나눗셈(/) 결괏값을 출력하라.

> #### python

```python
digit1 = input()
digit2 = input()
print (int(digit1) + int(digit2))
print (int(digit1) - int(digit2))
print (int(digit1) * int(digit2))
print (int(digit1) / int(digit2))
```

> #### swift

```swift
let num1 = readLine() ?? ""
let num2 = readLine() ?? ""
let num1Int = Int(num1) ?? 0
let num2Int = Int(num2) ?? 0
print(num1Int + num2Int)
print(num1Int - num2Int)
print(num1Int * num2Int)
print(num1Int / num2Int)
```

### 8. 거듭제곱

사용자로부터 밑과 지수를 입력 받은 후 거듭제곱 값을 출력하라. 실행 예: 밑: 3 지수: 2 3^2 = 9

> #### python

```python
digit1 = input()
digit2 = input()
result = int(digit1)**int(digit2) 
print(result)
returnn = result**(1/2)
print(returnn) 
```

> #### swift

```swift
let num1 = readLine() ?? ""
let num2 = readLine() ?? ""
let num1Int = Double(num1) ?? 0
let num2Int = Double(num2) ?? 0
let result = pow(num1Int, num2Int)
print(result) //Double
let `return` = sqrt(result)
print(`return`) //Double
```

### 9. 입력과 출력

사용자로부터 두 개의 숫자를 입력받은 후 두 개의 숫자를 더한 값, 곱한 값, 나눈 값, 나눈 몫, 나머지 값을 각각 출력하는 프로그램을 작성하세요. Example INPUT: 4 and 4 OUTPUT: 8 16 1.0 1 0

> #### python

```python
print (int(digit1) // int(digit2)) # 몫
print (int(digit1) % int(digit2))  # 나머지
```

> #### swift

```swift
print(10 / 3)
print(10 % 3)
```

### 10. 입력과 출력

### 11. 입력과 출력

사용자로부터 두 개의 숫자를 입력받은 후 두 개의 숫자를 나눈 값을 다음 조건에 맞추어 출력하는 프로그램을 작성하세요. format() 함수를 사용해서 출력하세요 단, 나눈 값은 소숫점 첫번째 자리까지만 출력하세요. Example INPUT: 5 and 4 OUTPUT:

5 / 4 = 1.2

> #### python

```python
# 많이 다름 swift 랑은 
digit1 = int(input())
digit2 = int(input())
print('{0} / {1} = {2:0.1f}'.format(digit1, digit2, digit1 / digit2 ))
```

> #### swift

```swift
let num1 = readLine() ?? ""
let num2 = readLine() ?? ""
let num1Double = Double(num1) ?? 0
let num2Double = Double(num2) ?? 0
let result = num1Double / num2Double
let str = String(format: "%.1f", result)
print("\(num1Double) / \(num2Double) = \(result)")
```

### 12. 기본 자료형

10, 2.2, "fun-coding" 각각을 변수에 넣고, 각 데이터 타입을 출력하세요.

> #### python

```python
print(type(10))
print(type(2.2))
print(type("hello"))
```

> #### swift

```swift
print(type(of: 10))
print(type(of: 2.2))
print(type(of: "hello"))
```

### 13. 기본 자료형

다음 코드를 실행해보고 \t와 \n의 역할을 설명하세요.

> #### python

```python
# \n 줄바꿈, \t tap만큼(4칸)띄우기
code = '000660\n00000102\t12312312'
print (code)
```

> #### swift

```swift
//\n 줄바꿈, \t tap만큼(4칸)띄우기
let code = "000660\n00000102\t12312312"
print (code)
```

### 14. 조건문

사용자로부터 두 개의 숫자를 입력 받은 후 큰 숫자를 화면에 출력하세요.

> #### python

```python
# : , 줄바꿈 주의
if 5 > 3: 
    print("5")
else:
    print("3")
```

> #### swift

```swift
if 5 > 3 {
    print("5")
} else {
    print("3")
}
```

### 15. 조건문

### 16. 조건문

사용자로부터 세 개의 숫자를 입력 받은 후 가장 작은 숫자를 출력하세요.

> #### python

```python
digit1 = int(input())
digit2 = int(input())
digit3 = int(input())
# 
if digit1 < digit2:
    min = digit1
else:
    min = digit2
if min < digit3:
    print (min)
else:
    print (digit3)
```

> #### swift

```swift
let num1 = readLine() ?? ""
let num2 = readLine() ?? ""
let num3 = readLine() ?? ""
let num1Int = Int(num1) ?? 0
let num2Int = Int(num2) ?? 0
let num3Int = Int(num3) ?? 0
var minNum = 0
//
if num1Int < num2Int {
    minNum = num1Int
} else {
    minNum = num2Int
}
//
if minNum < num3Int {
    print(minNum)
} else {
    print(num3Int)
}
//
// also max(), min()
print(min(1, 2, 3))
print(min(1, 2, 3, 4, 5, 6, 7)) // 가변함수라서 계속 추가 가능
```

### 17. 조건문

사용자로부터 점수를 입력 받은 후 등급을 출력하라. (A: 100 \~ 81, B: 80 \~ 61, C: 60 \~ 0)

> #### python

```python
digit = 9
# 
if digit <= 100 and digit >= 81:
    print ("A")
elif digit <= 80 and digit >= 61:
    print ("B")
elif digit <= 60 and digit >= 0:
    print ("C")
```

> #### swift

```swift
let digit = 0
//
if digit <= 100 && digit >= 81 {
    print("A")
} else if digit <= 80 && digit >= 61 {
    print("B")
} else if digit <= 60 && digit >= 0 {
    print("C")
}
```

### 18. 데이터 구조 (리스트)

사용자로부터 주민등록번호를 입력받아 출생 연도를 출력하세요. 예) 800001-1231231 주민번호를 입력받으면 80을 출력하면 됨

> #### python

```python
personal_id = "800001-1231231"
print (personal_id[0:2])
```

> #### swift

```swift
let personal_id = "800001-1231231"
//swift에서는  personal_id.startIndex, personal_id.endIndex 같은걸 지원한다.
//서브스크립트 문법도 지원하는데 앞이 index면 당연히 뒤도 index 여야 한다. [0...endIndex] 이런건 안된다.
let endIndex = personal_id.index(personal_id.startIndex, offsetBy: 1)
print(personal_id[personal_id.startIndex...endIndex])
//
//또는 이렇게도 가능
print(personal_id.prefix(2))
print(personal_id.suffix(2))
```

### 19. 데이터 구조 (리스트)

사용자로부터 주민등록번호를 입력받아 뒷자리 맨 앞의 숫자를 출력하세요. 주민등록번호 뒷자리 맨 앞자리는 성별을 나타냄 예) 800001-1231231 주민번호를 입력받으면 1을 출력하면 됨 1은 남성을 의미, 2는 여성을 의미, 최근 아이들은 3과 4를 사용함

> #### python

```python
personal_id = "800001-1231231"
print (personal_id[7])
```

> #### swift

```swift
print(Array(personal_id)[7])
//Array(arrayLiteral: personal_id)를 사용하면 ["800001-1231231"] 이렇게 되버림
//또는
//personal_id.components(separatedBy: "-").last!.prefix(0)
```

### 20. 데이터 구조 (리스트)

사용자로부터 주민등록번호를 입력받아, 성별을 '남성' 또는 '여성'으로 출력하세요. 주민등록번호 뒷자리 맨 앞자리는 성별을 나타냄 예) 800001-1231231 주민번호를 입력받으면 1을 출력하면 됨 1이면 남성, 2이면 여성을 출력하면 됨 (최근 아이들은 3과 4를 사용하지만 이 경우는 고려하지 않기로 함)

> #### python

```python
personal_id = "991002-2012232"
# 
if personal_id[7] == "1":
    print ("남성")
elif personal_id[7] == "2":
    print ("여성")
```

> #### swift

```swift
let gender = Array(personal_id)[7]
//
if gender == "1" {
    print("man")
} else if gender == "2" {
    print("women")
}
```

### 21. 문자열 다루기 (strip)

다음 문자열에서 ...를 제거하라. mystr = "a man goes into the room..." 출력 예: 'a man goes into the room'

> #### python

```python
mystr = "a man.goes into the room..."
print (mystr.strip("."))
```

> #### swift

```swift
var mystr = "a man.goes into the room..."
print(mystr.components(separatedBy: "...").first ?? "")
/*
 - mystr.components(separatedBy: <#T##StringProtocol#>)가 있어서 "..." 을 넣을 수 있지만, 
 mystr.split(separator: <#T##Character#>) 라서 안된다.
 - components는 components(separatedBy: <#T##CharacterSet#>) 를 지원해서 mystr.components(separatedBy: ["-", "&", "*"]) 이게 된다.
 - 마냥 components가 더좋은것 같다만 꼭 그런건 아닌게 밑에 22번을 보면 그렇다.
 - split은 스위프트 표준 라이브러리에 속해있어 Foundation을 import 할 필요가 없습니다.
 - components는 Foundation 프레임워크에 속해있어 Foundation을 import 하여 사용해야 합니다.
 //
 - split은 [SubString]을 반환합니다.
 - components는 [String]을 반환합니다.
 //
 - split은 separator 외에 maxSplits, omittingEmptySubsequences 등의 옵션 인자값을 갖습니다.
 - components는 separatedBy: 인자값 딱, 하나입니다.
 */
```

### 22. 문자열 다루기 (strip)

주식 종목을 나타내는 종목코드에 공백과 줄바꿈 기호가 포함되어 있다. 공백과 잘바꿈 기호를 제거하고 종목코드만을 추출하라. code = ' 000660\n ' 출력: '000660'

> #### python

```python
code = '         000660\n            '
print (code.strip(' \n'))
```

> #### swift

```swift
let code1 = "         000660\n            "
//code1.components(separatedBy: [" "]) 를 하면 아래처럼 이상하게 출력된다.
//["", "", "", "", "", "", "", "", "", "000660\n", "", "", "", "", "", "", "", "", "", "", "", ""]
print(code1.components(separatedBy: [" ", "\n"]).filter { $0 != "" }.first ?? "")
print(code1.split(separator: " ").joined().split(separator: "\n").first ?? "")
```

### 23. 문자열 다루기 (count)

다음 문자열에서 'Python' 문자열의 빈도수를 출력하라. python\_desc = "Python is an interpreted high-level programming language for general-purpose programming. Created by Guido van Rossum and first released in 1991, Python has a design philosophy that emphasizes code readability, notably using significant whitespace." 출력 예: 2

> #### python

```python
python_desc = "Python is an interpreted high-level programming language for general-purpose programming. Created by Guido van Rossum and first released in 1991, Python has a design philosophy that emphasizes code readability, notably using significant whitespace."
counts_Python = python_desc.count("Python")
print (counts_Python)
```

> #### swift

```swift
let python_desc = "Python is an interpreted high-level programming language for general-purpose programming. Created by Guido van Rossum and first released in 1991, Python has a design philosophy that emphasizes code readability, notably using significant whitespace."
let counts_Python = python_desc.components(separatedBy: " ").filter { $0 == "Python" }.count
print(counts_Python)
```

### 24. 문자열 다루기 (count)

다음 문자열에서 'p' 문자가 몇번 나오는지 빈도수를 출력하라. python\_desc = "Python is an interpreted high-level programming language for general-purpose programming. Created by Guido van Rossum and first released in 1991, Python has a design philosophy that emphasizes code readability, notably using significant whitespace." 출력 예: 9

> #### python

```python
print (python_desc.count("p"))
```

> #### swift

```swift
print(Array(python_desc).filter { $0 == "p" }.count)
```

### 25. 문자열 다루기 (문자열 인덱싱)

letters 라는 변수에 들어 있는 문자열에서 두 번째와 네 번째 문자를 출력하라

letters = "python" 출력 예: y h

> #### python

```python
letters = "python"
# print (letters)
print (letters[1])
print (letters[3])
```

> #### swift

```swift
let letters = "python"
let arrayLetters = Array(letters)
print(arrayLetters[1])
print(arrayLetters[3])
```

### 26. 문자열 다루기 (문자열 인덱싱)

letters 라는 변수에 사용자로부터 문자열을 입력받아서 문자 n 이 들어있는지를 출력하라 ( n 이 들어 있으면 0, 안들어있으면 -1을 출력하라)

> #### python

```python
letters = input()
# 
if letters.find("n") >= 0:
    print ("0")
else:
    print ("-1")
```

> #### swift

```swift
let letters = readLine() ?? ""
//
if letters.contains("n") {
    print ("0")
} else {
    print ("-1")
}
```

### 27. 문자열 다루기 (문자열 인덱싱)

### 28. 문자열 다루기 (문자열 인덱싱)와 조건문

주민등록번호의 뒷 자리 7자리 중 두번째부터 세번째는 출생 지역 코드입니다. 다음 표를 참조하여 사용자로부터 주민 등록 번호를 입력 받은 후 출생지를 출력하세요. 지역 코드 출생 지역 00 \~ 08 서울 09 \~ 12 부산

> #### python

```python
string1 = "10"
# print (string1, type(string1))
# 
string1 = int(string1)
# print (string1, type(string1))
# 
if string1 >= 0 and string1 <= 8:
    print ("서울")
elif string1 >= 9 and string1 <= 12:
    print ("부산")
```

> #### swift

```swift
let localeCode = "10"
let localeCodeNum = Int(localeCode) ?? 0
//
switch localeCodeNum {
case 0...8: print("서울")
case 9...12: print("부산")
default: break
}
```

### 29. 문자열 다루기 (split)

letters 라는 변수에 Dave,David,Andy 가 들어있다. 해당 변수값을 , 를 기준으로 분리해서 출력하라 출력 예: \['Dave', 'David', 'Andy']

> #### python

```python
letters = "Dave,David,Andy,2222,3123123,LLL"
letter_list = letters.split(",")
print (letter_list)
# 
for letter in letter_list:
    print (letter)
```

> #### swift

```swift
let letters = "Dave,David,Andy,2222,3123123,LLL"
let letter_list = letters.split(separator: ",")
for letter in letter_list {
    print (letter)
}
```

### 30. 문자열 다루기 (split)

다음과 같은 파일 이름(확장자 포함)에서 확장자를 제거한 파일 이름만 출력하세요. filename = 'exercise01.docx'

> #### python

```python
filename = 'exercise01.docx'
print (filename.split(".")[0])
```

> #### swift

```swift
let filename = "exercise01.docx"
print(filename.split(separator: ".").first ?? "")
```

### 31. 반복문

1 \~ 10까지의 숫자에 대해 모두 더한 값을 출력하는 프로그램을 for 문을 사용하여 작성하세요.

> #### python

```python
# 1 부터 11 로 되어있는 것을 주의 
sum = 0
for num in range(1, 11):
    sum += num
print (sum)
```

> #### swift

```swift
var sum = 0
for num in Range(1...10) {
    sum += num
}
print (sum)
//
var sum1 = 0
for num in 1..<11 {
    sum1 += num
}
print(sum1)
//
var sum2 = 0
(1...10).forEach { sum2 += $0 }
print(sum2)
//
print((1...10).reduce(0, +))
```

### 32. 반복문

사용자로부터 2 \~ 9 사이의 숫자를 입력 받은 후, 해당 숫자에 대한 구구단을 출력하세요.

> #### python

```python
digit = int(input())
for num in range(1, 10):
    print (digit, "x", num, "=", digit * num)
```

> #### swift

```swift
let number = Int(readLine() ?? "") ?? 0
for num in 1...9 {
    print(number, "x", num, "=", number * num)
}
```

### 33. 반복문과 문자열 다루기 (split)

### 34. 반복문과 문자열 다루기 (split)

사용자로부터 \[이름1],\[이름2],\[이름3] 과 같은 형식으로 데이터를 입력받아서, 한 줄에 하나씩 이름을 출력하세요 사용자 입력: \[Dave],\[David],\[Andy],\[Arthor] 출력 예: Dave David Andy Arthor

> #### python

```python
strings = "[Dave],[David],[Andy],[Arthor]"
for string in strings.split(","):
    # print (string)
    print (string.strip("[]"))
```

> #### swift

```swift
let strings = "[Dave],[David],[Andy],[Arthor]"
print(strings.components(separatedBy: [",", "[", "]"]).filter { $0 != "" })
```

### 35. 반복문과 조건문

1부터 30까지의 숫자 중 3의 배수만 출력하세요.

> #### python

```python
for num in range(1, 31):
    if num % 3 == 0:
        print (num)
```

> #### swift

```swift
(1...30).filter { $0 % 3 == 0 }.forEach { print($0) }
```

### 36. 반복문 (while)

1부터 100까지 숫자를 모두 더한 값을 출력하세요. 단 while 구문을 사용해서 작성하세요.

> #### python

```python
total, num = 0, 1
#
while num < 101:
    total += num
    num += 1
# 
print (total)  
```

> #### swift

```swift
var total = 0
var num = 1
//
while num < 101 { //이 조건을 만족하는 동안만 스코프 안을 동작
    total += num
    num += 1
}
print (total)
```

### 37. 반복문 (while)

사용자로부터 4자리의 숫자로 구성된 데이터를 입력받아서 비밀번호와 같으면 '비밀번호가 맞습니다.'를 출력하고 종료하세요. 비밀번호와 다르면 '비밀번호가 틀렸습니다.'를 출력하고 다시 사용자로부터 데이터를 입력받으세요. 비밀번호는 4312 입니다.

> #### python

```python
password = "4312"
data = str()
# 
while data != password:
    data = input()
    if data == password:
        print ("비밀번호가 맞습니다.")
        break
    else:
        print ("비밀번호가 틀렸습니다.")
```

> #### swift

```swift
let password = "4312"
var data = ""
//
while data != password {
    data = readLine() ?? ""
    if data == password {
        print ("비밀번호가 맞습니다.")
        break
    } else {
        print ("비밀번호가 틀렸습니다.")
    }
}
```

### 38. 데이터 구조와 반복문 (리스트)

다음 리스트 변수에서 음수 데이터를 삭제하고, 양수만 가진 리스트 변수로 만들고, 해당 변수를 출력하세요. num\_list = \[0, -11, 31, 22, -11, 33, -44, -55]

> #### python

```python
num_list = [0, -11, 31, 22, -11, 33, -44, -55]
num_list2 = list()
# 
for index, num in enumerate(num_list):
    if num >= 0:
        num_list2.append(num)
#     
print (num_list2)
```

> #### swift

```swift
let num_list = [0, -11, 31, 22, -11, 33, -44, -55]
print(num_list.filter { $0 >= 0})
```

### 39. 데이터 구조와 반복문 (리스트)

다음 리스트에 있는 데이터의 길이를 한 라인에 하나씩 출력하세요. list\_data = \["fun-coding", "Dave", "Linux", "Python", "javascript", "front-end", "back-end", "dataengineering"]

> #### python

```python
list_data = ["fun-coding", "Dave", "Linux", "Python", "javascript", "front-end", "back-end", "dataengineering"]
for data in list_data:
    print ("\"" + data + "\"" + "'s length is", len(data))
```

> #### swift

```swift
let list_data = ["fun-coding", "Dave", "Linux", "Python", "javascript", "front-end", "back-end", "dataengineering"]
list_data.forEach { print("\($0)'s length is", $0.count) }
```

### 40. 데이터 구조와 반복문 (리스트)

다음 리스트에 있는 숫자를 역 방향으로 출력하세요. 단, 리스트에 있는 숫자들은 한 라인에 하나씩 출력하세요. data = \[1, 2, 3, 4, 5, 6, 7, 8, 9, 10] 실행 예: 10 9 8 7 6 5 4 3 2 1

> #### python

```python
data = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
data.reverse()
for item in data:
    print (item)
```

> #### swift

```swift
let data = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
data.reversed().forEach { print($0) }
```

### 41. 데이터 구조 (리스트), 반복문, 문자열 다루기

### 42. 데이터 구조 (리스트), 반복문, 조건문, 문자열 다루기

파일 이름이 다음과 같은 리스트에 저장되어 있을 때 확장자가 .txt 인 파일에 대한 리스트를 출력하라 filelist = \['exercise01.docx', 'exercise02.csv', 'exercise03.txt', 'exercise04.hwp']

> #### python

```python
filelist = ['exercise01.docx', 'exercise02.csv', 'exercise03.txt', 'exercise04.hwp']
for filename in filelist:
    if filename.split(".")[1] == "txt":
        print (filename)
```

> #### swift

```swift
let filelist = ["exercise01.docx", "exercise02.csv", "exercise03.txt", "exercise04.hwp"]
filelist
    .filter { $0.split(separator: ".").last ?? "" == "txt" }
    .forEach { print($0) }
```

### 43. 문자열 다루기와 조건문

### 44. 문자열 다루기와 조건문

### 45. 문자열 다루기, 조건문, 데이터 구조 (dictionary)

### 46. 이중 반복문

구구단을 2단부터 9단까지 다음과 같이 출력하세요 출력 예 2 X 1 = 2 2 X 2 = 4 2 X 3 = 6 2 X 4 = 8 2 X 5 = 10 2 X 6 = 12 2 X 7 = 14 2 X 8 = 16 2 X 9 = 18 3 X 1 = 3 3 X 2 = 6 . . . 9 X 7 = 63 9 X 8 = 72 9 X 9 = 81

> #### python

```python
for num1 in range(2, 10):
    for num2 in range(1, 10):
        print (num1, "x", num2, "=", num1 * num2)
```

> #### swift

```swift
for num1 in 2...9 {
    for num2 in 1...9 {
        print(num1, "x", num2, "=", num1 * num2)
    }
}
```

### 47. 이중 반복문과 조건문

구구단을 2단부터 9단까지 출력하되, 계산 값이 짝수인 경우에만 출력하세요 예: 3 X 3 = 9 에서 9는 홀수이므로 출력하지 않는다. 예: 2 X 4 = 8 에서 8은 짝수이므로 출력한다. 최종 출력 예 2 X 2 = 4 2 X 3 = 6 2 X 4 = 8 2 X 5 = 10 2 X 6 = 12 2 X 7 = 14 2 X 8 = 16 2 X 9 = 18 3 X 2 = 6 3 X 4 = 12 3 X 6 = 18 3 X 8 = 24 4 X 2 = 8 4 X 3 = 12 4 X 4 = 16 4 X 5 = 20 4 X 6 = 24 4 X 7 = 28 4 X 8 = 32 4 X 9 = 36 5 X 2 = 10 5 X 4 = 20 5 X 6 = 30 5 X 8 = 40 6 X 2 = 12 6 X 3 = 18 6 X 4 = 24 6 X 5 = 30 6 X 6 = 36 6 X 7 = 42 6 X 8 = 48 6 X 9 = 54 7 X 2 = 14 7 X 4 = 28 7 X 6 = 42 7 X 8 = 56 8 X 2 = 16 8 X 3 = 24 8 X 4 = 32 8 X 5 = 40 8 X 6 = 48 8 X 7 = 56 8 X 8 = 64 8 X 9 = 72 9 X 2 = 18 9 X 4 = 36 9 X 6 = 54 9 X 8 = 72

> #### python

```python
for num1 in range(2, 10):
    for num2 in range(1, 10):
        result = num1*num2
        if result % 2 == 0:
            print (num1, "x", num2, "=", result)
```

> #### swift

```swift
for num1 in 2...9 {
    for num2 in 1...9 {
        let result = num1 * num2
        if result % 2 == 0 {
            print (num1, "x", num2, "=", result)
        }
    }
}
```

### 48. 이중 반복문과 데이터 구조 (리스트)

아파트 동호수를 다음과 같은 두 리스트 변수를 활용해서 출력하세요. 단, 각 동과 동 사이에는 구분이 되도록 한 라인씩 띄워서 출력하세요 dongs = \["6209동", "6208동", "6207동"] hos = \["101호", "102호", "103호", "104호"] 출력 예: 6209동 101호 6209동 102호 6209동 103호 6209동 104호 6208동 101호 6208동 102호 6208동 103호 6208동 104호 6207동 101호 6207동 102호 6207동 103호 6207동 104호

> #### python

```python
dongs = ["6209동", "6208동", "6207동"]
hos = ["101호", "102호", "103호", "104호"]
for dong in dongs:
    for ho in hos:
        print (dong, ho)
```

> #### swift

```swift
let dongs = ["6209동", "6208동", "6207동"]
let hos = ["101호", "102호", "103호", "104호"]
dongs.forEach { dong in hos.forEach { ho in print(dong, ho) }}
```

### 49. 데이터 구조 (튜플)

a, b, c, d, e를 저장하는 튜플을 만들고 첫 번째 튜플값과 마지막 번째 튜플값을 출력하세요.

> #### python

```python
tuple_data = ("a", "b", "c", "d", "e")
print (tuple_data[0], tuple_data[4])
```

> #### swift

```swift
var tuple_data = ("a", "b", "c", "d", "e")
print(tuple_data.0, tuple_data.4)
```

### 50. 데이터 구조 (튜플)

다음 코드를 작성해서 실행해보고 에러가 나는 이유를 설명하세요.

실행코드 tupledata = ('dave', 'fun-coding', 'endless') tupledata\[0] = 'david' 에러

TypeError Traceback (most recent call last) in () 1 tupledata = ('dave', 'fun-coding', 'endless') ----> 2 tupledata\[0] = 'david'

TypeError: 'tuple' object does not support item assignment

> #### python

```python
tupledata = ('dave', 'fun-coding', 'endless')
tupledata[0] = 'david'
TypeError: 'tuple' object does not support item assignment
Tuple data cannot be changed!
파이썬 튜플은 값을 변경할 수 없지만 swift는 가능하다. 
```

> #### swift

```swift
//파이썬 튜플은 값을 변경할 수 없지만 swift는 가능하다.
print(tuple_data)
tuple_data.0 = "z"
print(tuple_data)
```

### 51. 데이터 구조 (튜플)

다음 코드를 읽고, 최종적으로 var1과 var2의 값이 어떤 값이될지 확인해보고, 왜 이렇게 동작하는지 튜플을 기반으로 설명하세요. 실행코드 var1, var2 = 1, 2 var1, var2 = var2, var1

> #### python

```python
# 파이썬에서 튜플은 불변값
# 리스트는 [], 튜플은 () 이거나 생략 
# 리스트는 그 값의 생성, 삭제, 수정이 가능하지만 튜플은 그 값을 바꿀 수 없다.
var1, var2, var3, var4 = 1, 2, 3, 4
var1, var2, var3, var4 = var4, var3, var2, var1
print (var1, var2, var3, var4)
```

> #### swift

```swift
//파이썬 리스트 vs 튜플
//
//리스트는 [ ]으로 둘러싸지만 튜플은 ( )으로 둘러싼다.
//리스트는 그 값의 생성, 삭제, 수정이 가능하지만 튜플은 그 값을 바꿀 수 없다.
//
//튜플의 모습은 다음과 같다.
//>>> t1 = ()
//>>> t2 = (1,)
//>>> t3 = (1, 2, 3)
//>>> t4 = 1, 2, 3
//>>> t5 = ('a', 'b', ('ab', 'cd'))
//
//리스트와 모습은 거의 비슷하지만 튜플에서는 리스트와 다른 2가지 차이점을 찾아볼 수 있다. t2 = (1,)처럼 단지 1개의 요소만을 가질 때는 요소 뒤에
//콤마(,)를 반드시 붙여야 한다는 것과 t4 = 1, 2, 3처럼 괄호( )를 생략해도 무방하다는 점이다.
//
//얼핏 보면 튜플과 리스트는 비슷한 역할을 하지만 프로그래밍을 할 때 튜플과 리스트는 구별해서 사용하는 것이 유리하다. 튜플과 리스트의 가장 큰 차이는 값을
//변화시킬 수 있는가 여부이다. 즉 리스트의 항목 값은 변화가 가능하고 튜플의 항목 값은 변화가 불가능하다. 따라서 프로그램이 실행되는 동안 그 값이 항상
//변하지 않기를 바란다거나 값이 바뀔까 걱정하고 싶지 않다면 주저하지 말고 튜플을 사용해야 한다. 이와는 반대로 수시로 그 값을 변화시켜야할 경우라면 리스트를
//사용해야 한다. 실제 프로그램에서는 값이 변경되는 형태의 변수가 훨씬 많기 때문에 평균적으로 튜플보다는 리스트를 더 많이 사용한다.
//
//파이썬에서 튜플이라는 애가 있고 리스트라는 애가 있다.
//리스트는 swift로 치면 배열쯤 된다.
//근데 차이점은 파이썬에서 튜플은 값을 변경할 수가 없다.
//따라서 이 문제는 성립불가
```

### 52. 데이터 구조 (튜플)

다음 코드에서 두번째 데이터부터 마지막 데이터를 다음과 같이 출력하세요 tupledata = ('fun-coding1', 'fun-coding2', 'fun-coding3', 'fun-coding4', 'fun-coding5') 출력: ('fun-coding2', 'fun-coding3', 'fun-coding4', 'fun-coding5')

> #### python

```python
tupledata = ('fun-coding1', 'fun-coding2', 'fun-coding3', 'fun-coding4', 'fun-coding5')
tupledata[1:]
```

> #### swift

```swift
//swift 에서 튜플은 시퀀스 타입이 아니야.
//그래서 서브스크립트 문법 같은 것도 지원하지 않는다.
```

### 53. 데이터 구조 (튜플과 리스트)

다음 튜플 데이터를 리스트 데이터로 변환한 후에 'fun-coding4' 데이터를 마지막에 추가하고, 다시 튜플 데이터로 변환하세요. tupledata = ('fun-coding1', 'fun-coding2', 'fun-coding3')

> #### python

```python
tupledata = ('fun-coding1', 'fun-coding2', 'fun-coding3')
# print (type(tupledata))
listdata = list(tupledata)
# print (type(listdata))
listdata.append('fun-coding4')
# print (listdata)
tupledata = tuple(listdata)
print (tupledata)
```

> #### swift

```swift
//Mirror는 클래스나 구조체같은(여기서는 튜플)을 AnyCollection<(label: Optional<String>, value: Any)> 으로 만들어주는 녀석이다.
//억지로 튜플을 컬렉션 타입으로 변경해서 값을 추가해줄 수는 있다.
let tupledatas = ("fun-coding1", "fun-coding2", "fun-coding3", "fun-coding4", "fun-coding5")
var tupleArray = Mirror(reflecting: tupledatas).children.map { ($0.value) }
tupleArray.append("newData")
print(tupleArray)
print(tupleArray[1...])
```

### 54. 데이터 구조 (튜플, 리스트, 딕셔너리)

비어 있는 튜플, 리스트, 딕셔너리 변수를 하나씩 각각 만드세요.

> #### python

```python
tupledata = tuple()
listdata = list()
dictdata = dict()
print (type(tupledata), type(listdata), type(dictdata))
```

> #### swift

```swift
let tupledata: (name: Any, Any) = ("", "")
let listdata: [Any] = []
let dictdata: [String: Any] = [:]
print (type(of:tupledata), type(of:listdata), type(of:dictdata))
```

### 55. 데이터 구조 (딕셔너리)

다음 영어 사전 데이터를 딕셔너리 변수로 만들고, 딕셔너리 변수의 key 값인 영어단어, value 값인 의미 만을 가진 리스트 변수를 각각 만들고, 두 리스트 변수를 출력하세요. 영어단어 의미 environment 환경 company 회사 government 정부, 정치 face 얼굴 출력 예 {'environment': '환경', 'company': '회사', 'government': '정부, 정치', 'face': '얼굴'} \['environment', 'company', 'government', 'face'] \['환경', '회사', '정부, 정치', '얼굴']

> #### python

```python
# 고차함수를 쓰는방법?이 특이함. 포문을 돌리고 그 밖에 배열을 씌우는식? 
dictdata = {'environment': '환경', 'company': '회사', 'government': '정부, 정치', 'face': '얼굴'}
dictdata_keys = [data for data in dictdata.keys()] # using list comprehension
dictdata_values = [data for data in dictdata.values()] # using list comprehension
print (dictdata_keys)
print (dictdata_values)
```

> #### swift

```swift
let dict = ["environment": "환경", "company": "회사", "government": "정부, 정치", "face": "얼굴"]
let dictdata_keys = dict.map { $0.key }
let dictdata_values = dict.map { $0.value }
print (dictdata_keys)
print (dictdata_values)
```

### 56. 데이터 구조 (딕셔너리)

### 57. 데이터 구조 (딕셔너리)

다음 영어 사전 데이터를 딕셔너리 변수로 만들고 외움표시가 X 인 영어단어만 출력하세요 단, key는 영어단어, value는 의미와 외움표시 두 데이터를 넣습니다. environment : 환경 company : 회사 gonernment : 정부, 정치 face : 얼굴 영어단어 의미 외움표시 environment 환경 X company 회사 O government 정부, 정치 X face 얼굴 X 출력 예 environment government face

> #### python

```python
dictdata = {'environment': ['환경', 'X'], 'company': ['회사', 'O'], 'government': ['정부, 정치', 'X'], 'face': ['얼굴', 'X']}
# 
for data in dictdata.keys():
    if dictdata[data][1] == 'X':
        print (data)
# print (dictdata['environment'][0], dictdata['environment'][1])
```

> #### swift

```swift
//특이한 점은 value 값이 2개인데 파이썬에서는 contains 같은 것이 아닌 그냥 dictdata['environment'][1] 처럼 조회를 해도 해당 값의 존재여부를 알 수 있다는 점이다.
var dictvalues = ["environment": ["환경", "X"],
                  "company": ["회사", "O"],
                  "government": ["정부, 정치", "X"],
                  "face": ["얼굴", "X"]]
//print(dictvalues.filter { $0.value.contains("X") }.keys.forEach { print($0) })
```

### 58. 데이터 구조 (딕셔너리)

다음 영어 사전 데이터를 딕셔너리 변수로 만들고 사용자로부터 영어단어를 입력받으면 해당 영어단어의 외움표시를 O로 수정하고, 외움표시가 X 인 단어만 출력하는 프로그램을 작성하세요. 단, key는 영어단어, value는 의미와 외움표시 두 데이터를 넣습니다. environment : 환경 company : 회사 gonernment : 정부, 정치 face : 얼굴 영어단어 의미 외움표시 environment 환경 X company 회사 O government 정부, 정치 X face 얼굴 X 입력: government 출력 environment face

> #### python

```python
dictdata = {'environment': ['환경', 'X'], 'company': ['회사', 'O'], 'government': ['정부, 정치', 'X'], 'face': ['얼굴', 'X']}
# 
data = input()
dictdata[data][1] = 'O'
# print (dictdata)
# 
for data in dictdata.keys():
    if dictdata[data][1] == 'X':
        print (data)
```

> #### swift

```swift
let key = readLine() ?? ""
dictvalues[key] = [ dictvalues[key]?.first ?? "", "O" ]
print(dictvalues)
print(dictvalues.filter { $0.value.contains("X") }.keys.forEach { print($0) })
```

### 59. 데이터 구조 (딕셔너리)

다음 dict\_all, dict2, dict3 딕셔너리 변수가 있을 때, dict2와 dict3 두 데이터를 dict\_all 딕셔너리 변수에 추가한 후, dict\_all 변수를 출력하세요. dict\_all = {'environment': '환경', 'gonernment':'정부, 정치'} dict2 = {'company': '회사', 'face':'얼굴'} dict3 = {'apple': '사과'}

> #### python

```python
dict_all = {'environment': '환경', 'gonernment':'정부, 정치'}
dict2 = {'company': '회사', 'face':'얼굴'}
dict3 = {'apple': '사과'}
# 
print (dict_all)
for data in dict2.keys():
    dict_all[data] = dict2[data]
for data in dict3.keys():
    dict_all[data] = dict3[data]    
print (dict_all)
```

> #### swift

```swift
var dict_all = ["environment": "환경", "gonernment":"정부, 정치"]
let dict2 = ["company": "회사", "face":"얼굴"]
let dict3 = ["apple": "사과"]
dict2.forEach { dict_all.updateValue($1, forKey: $0) }
dict3.forEach { dict_all.updateValue($1, forKey: $0) }
print (dict_all)
```

### 60. 데이터 구조 (딕셔너리)

### 61. 데이터 구조 (집합)

number\_list가 다음과 같은 리스트일 때 중복 숫자를 제거한 리스트를 만들고, 출력하세요

number\_list = \[5, 1, 2, 2, 3, 3, 4, 5, 5, 6, 7, 8, 9, 9, 10, 10]

> #### python

```python
number_list = [5, 1, 2, 2, 3, 3, 4, 5, 5, 6, 7, 8, 9, 9, 10, 10]
number_set = set(number_list)
print (number_set)
```

> #### swift

```swift
let number_list = [5, 1, 2, 2, 3, 3, 4, 5, 5, 6, 7, 8, 9, 9, 10, 10]
let number_set = Set(number_list)
print (number_set.sorted())
```

### Reference

swift 코드는 직접 작성했고, python 관련 코드는 [여기](https://www.fun-coding.org/python-question1-answer.html)를 참고하였습니다.
