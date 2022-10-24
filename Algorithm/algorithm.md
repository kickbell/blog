## Algorithm(알고리즘)이란 ? 
- 어떤 문제를 풀기 위한 절차 또는 방법 
- 어떤 문제에 대해 특정한 '입력'을 넣으면 원하는 '출력'을 얻을 수 있도록 만드는 프로그래밍을 말한다. 
- 예를 들어, '라면 레시피'라는 알고리즘이 있다. 이 특정한 '입력'은 물 500ml를 몇분간끓이고, 면과 스프를 넣으면 라면이라는 '출력'을 얻는 알고리즘이다. 그런데, 라면은 사람마다 끓이는 스타일이 다를 수 있다. 누구는 찬물에 먼저 스프를 넣을 수 있고, 누구는 물을 350ml만 할 수도 있고 또 라면을 10분간 끓일 수도 있다. 결과물은 어찌됐건 같은 '라면'이다. 그러면 어찌됐건 똑같은 라면이라는 '출력'이 나왔다고 했을 때, 무슨 알고리즘을 택해야 할까의 기준은 이러하다. 
- 어떤 방법이 더 시간을 적게 쓰는지, 또 어떤 방법이 더 공간을 적게 사용하는 지의 여부다. 시간복잡도/공간복잡도 라는 개념인데 나중에 살펴보겠다. 
			
![](https://velog.velcdn.com/images/dev_kickbell/post/d52af376-b87d-4b33-8365-22e9dcc65c4c/image.png)		

- 알고리즘 복잡도 계산 항목
	- 시간 복잡도( 알고리즘 실행 속도 )
    - 공간 복잡도( 알고리즘이 사용하는 메모리 사이즈 )
    - 메모리 성능이 좋아짐에 따라서 공간복잡도보다는 시간 복잡도의 중요성이 커짐. 
	- 시간 복잡도를 결정하는 주요요인은 바로 반복문.
	- 시간 복잡도 계산은 반복문이 핵심 요소임을 인지하고, 계산 표기는 최상/평균/최악 중, 최악의 시간인 Big-O 표기법을 중심으로 익히면 됨

- 알고리즘 성능 표기법
	- Big O (빅-오) 표기법: O(N)
    	- 알고리즘 최악의 실행 시간을 표기
      	- **가장 많이/일반적으로 사용함**
      	- **아무리 최악의 상황이라도, 이정도의 성능은 보장한다는 의미이기 때문**
	- Ω (오메가) 표기법:  Ω(N)		
  		- 오메가 표기법은 알고리즘 최상의 실행 시간을 표기		
	- Θ (세타) 표기법: Θ(N)		
    	- 오메가 표기법은 알고리즘 평균 실행 시간을 표기		

- Big-O (빅-오 표기법)				
	- O(입력)		
    - 입력 n 에 따라 결정되는 시간 복잡도 함수		
    - O(1), O($log n$), O(n), O(n$log n$), O($n^2$), O($2^n$), O(n!)등으로 표기함
    - 입력 n 의 크기에 따라 기하급수적으로 시간 복잡도가 늘어날 수 있음
    - O(1) < O($log n$) < O(n) < O(n$log n$) < O($n^2$) < O($2^n$) < O(n!)
    - 참고: log n 의 베이스는 2 - $log_2 n$			
        		
![](https://velog.velcdn.com/images/dev_kickbell/post/f4c3753a-b41a-43f7-86ab-f9ee661ae089/image.png)			
					
## Bubble Sort(버블 정렬)

<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드</a></summary>
  <p>

```swift
import Foundation

func processTime(closure: () -> ()){
    let start = CFAbsoluteTimeGetCurrent()
    closure()
    let processTime = CFAbsoluteTimeGetCurrent() - start
    print("경과 시간: \(processTime)\n")
}


/*
 버블정렬 1
 https://visualgo.net/en/sorting
 
 예: data_list = [1, 9, 3, 2]
 1차 로직 적용
     1 와 9 비교, 자리바꿈없음 [1, 9, 3, 2]
     9 와 3 비교, 자리바꿈 [1, 3, 9, 2]
     9 와 2 비교, 자리바꿈 [1, 3, 2, 9]
 2차 로직 적용
     1 와 3 비교, 자리바꿈없음 [1, 3, 2, 9]
     3 과 2 비교, 자리바꿈 [1, 2, 3, 9]
     3 와 9 비교, 자리바꿈없음 [1, 2, 3, 9]
 3차 로직 적용
     1 과 2 비교, 자리바꿈없음 [1, 2, 3, 9]
     2 과 3 비교, 자리바꿈없음 [1, 2, 3, 9]
     3 과 9 비교, 자리바꿈없음 [1, 2, 3, 9]
 
 bubblesort1 의 핵심은 일단 array.count - 1 만 순회한다는 것이다.
 배열의 길이가 4라면 4 * 4 가 아니라 3 * 3 회 순회한다.
 그래야 result[index2] > result[index2 + 1] 로직이 정상적으로 동작한다.
 */

func bubblesort1(_ array: [Int]) -> [Int] {
    var result = array
    
    for _ in 0..<array.count - 1 {
        for index2 in 0..<array.count - 1 {
            if result[index2] > result[index2 + 1] {
                result.swapAt(index2, index2 + 1)
            }
        }
    }
    
    return result
}

//print("===before bubble sort1===")
//print([1,9,3,2])
//
//print("===after bubble sort1===")
//print(bubblesort1([1,9,3,2]))



/*
 코드 최적화작업 1
 
 [1, 9, 3, 2] 를 정렬할 때, 1 cycle을 다 돌면 [1, 3, 2, 9]로 제일 오른쪽 숫자는 가장 큰 숫자가 된다.
 그 말은 2 cycle을 돌면 제일 오른쪽 - 1 의 숫자는 두번째로 큰 숫자가 된다는 거고, 3 cycle을 돌면 제일 오른쪽 - 2 의 숫자는 세번째로 큰 숫자가 된다는 거다.
 쉽게 말하면 그거다.
 [1, 9, 3, 2] 일 때, 1 cycle 은 3 회를 돌면 된다.
 2 cycle 은 2회만 돌면 된다.
 3 cycle 은 1회만 돌면 된다.
 왜? 1 cycle 을 반복할 때마다 제일 오른쪽 숫자가 정렬이 되서 가장 큰 숫자가 되니까.
 그러면 아래와 같이 코드를 최적화 할 수 있다.
 
 아래에서 processTime 을 측정해보면 실제로 시간이 덜 걸린다. 반정도?
 */
func bubblesort2(_ array: [Int]) -> [Int] {
    var result = array
    
    for index1 in 0..<array.count - 1 { //여기는 전체 포문이니까 무시하고
        for index2 in 0..<array.count - 1 - index1 { //결국엔 여기다.
            //첫번째 0, 두번째 1, 세번째 2 하면 딱 맞다.
            if result[index2] > result[index2 + 1] {
                result.swapAt(index2, index2 + 1)
            }
        }
    }
    
    return result
}



/*
 코드 최적화작업 2
 
 추가적으로 최적화 할 수 있는 작업이 있다. 일종의 예외처리 코드일 수 있겠다.
 예를 들어 [1, 2, 3, 5] 라는 데이터가 있다고 치자.
 근데 얘는 이미 정렬이 되어있다. 그렇다면 바로 종료해야 하지 않을까? 그럼 그걸 어떻게 체크할까?
 swap이 false라는 것은 이미 정렬이 다 되어있다는 것이고 true는 그렇지 않다는 것이다.
 코드에 isSwap 이라는 Bool 타입 변수를 하나 만든다. 그다음에 for문을 한번 돈다.
 그런데 정렬된 코드라면? 1cycle만 돌고 반복문을 종료시켜버리는 것이다.
 */
func bubblesort3(_ array: [Int]) -> [Int] {
    var result = array
    for index1 in 0..<array.count - 1 {
        var isSwap = false //swap 위치가 중요하다.  1번 스왑되면 바로 정렬이 될 수 있으니까.
        for index2 in 0..<array.count - 1 - index1 { //결국엔 여기다.
            //첫번째 0, 두번째 1, 세번째 2 하면 딱 맞다.
            if result[index2] > result[index2 + 1] {
                result.swapAt(index2, index2 + 1)
                isSwap = true
            }
        }
        if isSwap == false { break }
    }
    
    return result
}

var list: Set<Int> = []

while list.count < 50 {
    let randomInt = Int.random(in: 1...100)
    list.insert(randomInt)
}



//1. bubblesort1 와 bubblesort2 비교
print("\n===before bubble sort===")
print(list)

print("\n===after bubble sort===")
processTime { print(bubblesort1(Array(list))) }
processTime { print(bubblesort2(Array(list))) }
processTime { print(bubblesort3(Array(list))) }


//2. bubblesort2 와 bubblesort3 비교
//실제로 위에서는 2,3 이 딱히 차이나지 않거나 비슷하다. 하지만 일부 정렬된 데이터를 넣어버리면 차이는 크다.
print("\n===after bubble sort===")
let sortedList = [1, 2, 3, 33, 5, 6, 8, 9, 85, 13, 14, 15, 16, 20, 88, 27, 31, 33, 34, 35, 37, 38, 43, 46, 47, 51, 52, 53, 54, 62, 63, 64, 65, 70, 73, 75, 77, 78, 81, 87, 89, 90, 93, 94, 96, 98, 100]
processTime { print(bubblesort2(sortedList)) }
processTime { print(bubblesort3(sortedList)) }

```
  </p>
</details>

- 두 인접한 데이터를 앞에 있는 데이터가 뒤에 있는 데이터보다 크면 자리를 바꾸는 알고리즘
- [https://visualgo.net/en/sorting](https://visualgo.net/en/sorting)		
- 코드 최적화 작업 1,2까지 언제든지 구현하라고 하면 할 수 있어야 함. 
- 알고리즘 분석
    - 반복문이 두 개 -> O($n^2$)
    - 완전 정렬된 상태일 때 -> O($n$)

## Insertion Sort(삽입 정렬)
    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드</a></summary>
  <p>

```swift

import Foundation

/*
 삽입정렬
 https://visualgo.net/en/sorting
 
 버블정렬하고 약간 비슷한데 다르기도 하다.
 
 [1,9,3,2] 가 있다고 하면, 일단 두번째 녀석부터 시작한다. 여기서는 9.
 그리고 9를 1과 비교한다. 앞에가 더 작으면 멈추고 아니면 스왑한다.
 언제까지? 자기보다 작은애가 나올때까지.
 
 */


func insertSort(_ array: [Int]) -> [Int] {
    var result = array
    
    for index in 0..<array.count - 1 { //전체 데이터 킉 - 1 만큼 순회
        
        //swift 는 for문 reverse를 위해서 stride 함수를 사용한다.
        //되게 중요한 부분인데, index는 0,1,2,3...이면 위에도 말했듯이 두번째꺼를 기준으로 첫번째꺼와 비교하면서 시작하니까
        //from : index + 1 부터 to : 0 번째 까지 비교해라. 다돌면 by : -1 씩 내려라 뭐 그런 뜻이다.
        for index2 in stride(from: index + 1, to: 0, by: -1) {
            //그래서 index : 0 , index2 : 1 이고, result[1](오른쪽) < result[0](왼쪽) 보다 작으면 스왑이다.
            //약간 헷갈릴 수 있는 부분이다.
            if result[index2] < result[index2 - 1] {
                result.swapAt(index2, index2 - 1)
            } else {
                break
            }
        }
    }
    
    return result
}


var list: Set<Int> = []

while list.count < 50 {
    let randomInt = Int.random(in: 1...100)
    list.insert(randomInt)
}

print(list)

print("\n===insertSort===")
print(insertSort(Array(list)))
```
  </p>
</details>

- 2번째 index부터 시작한다. 
- 해당 값을 해당 값보다 앞에 있는 데이터와 비교해서 앞에가 더 작으면 멈추고 아니면 앞에가 더 작을 때까지 swap한다. 
- [https://visualgo.net/en/sorting](https://visualgo.net/en/sorting)
- 알고리즘 분석
    - 반복문이 두 개 -> O($n^2$)
    - 완전 정렬된 상태일 때 -> O($n$)

    
## Selction Sort(선택 정렬) 

<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드</a></summary>
  <p>

```swift

import Foundation

/*
 선택정렬
 https://visualgo.net/en/sorting
 
 1. 주어진 데이터 중 첫번째꺼와 비교해서 일단 최소값을 찾음
 2. 최소값을 찾는데, 첫번째꺼보다 작다면 작은걸로 즉시 최소값 위치가 바뀌는 것임.
 3. 해당 최소값을 데이터 맨 앞에 위치한 값과 교체함
 4. 맨 앞의 위치를 뺀 나머지 데이터를 동일한 방법으로 반복함
 */


func selectionSort(_ array: [Int]) -> [Int] {
    var result = array
    
    for standIndex in 0..<array.count - 1 { //첫번째를 기준으로 나머지하고 비교하는거니까 4개의 배열이면 0,1,2 로 3개면 됨.
        
        //1. 최소값 찾기
        //최소값의 기준이 되는 minIndex를 만들고 시작점인 standIndex를 할당함
        var minIndex = standIndex
        //제일첫번째(minIndex 또는 standIndex)와 그 다음부터 비교해야하니 (standIndex + 1) 라고 지정함.
        for index in (standIndex + 1)..<array.count {
            //[9, 3, 2, 1] 이라고 할 때, 9가 3보다 크다면 9보다 3이 최소값인 거니까 최소값의 인덱스를 3의 인덱스로 바꿔줌
            //어디까지? 끝까지. 그래서 최소값을 찾는것임. 최소값의 index는 miniIndex가 되어야 겠지.
            if result[minIndex] > result[index] {
                minIndex = index
            }
        }
        
        //2. 최소값을 기준점과 swap하기
        //최소값을 찾았으니 현재의 index(standIndex)와 찾은 최소값을 index(minIndex)의 데이터를 스왑해줌
        result.swapAt(minIndex, standIndex)
    }
    
    return result
}



var list: Set<Int> = []

while list.count < 50 {
    let randomInt = Int.random(in: 1...100)
    list.insert(randomInt)
}

print(list)

print("\n===insertSort===")
print(selectionSort(Array(list)))
```
  </p>
</details>

- 먼저 최소값을 지정할 변수 minIndex 를 하나 만들고 for문을 끝까지 돌아서 최소값을 찾는다. 
- 해당 최소값을 0번째 index와 swap한다. 
- 다시 그 다음 최소값을 for문으로 찾고, 1번째 index와 swap한다. 
- 다시 그 다음 최소값을 for문으로 찾고, 2번째 index와 swap한다.
- 그렇게 끝까지 한다. 
- [https://visualgo.net/en/sorting](https://visualgo.net/en/sorting)
- 알고리즘 분석
    - 반복문이 두 개 -> O($n^2$)
    - 완전 정렬된 상태일 때 -> O($n^2$)

## Recursive Call(재귀 용법, 재귀 호출)

<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드</a></summary>
  <p>

```swift
import Foundation

/*
 보통 1,2번과 같은 2가지 패턴으로 많이 쓰인다.
 주로 2번이 더 많이 쓰이는 것 같고.. 자기 자신을 호출하는데 꼭 -1 하거나 +1 하거나 그러곤 하지.
 */
func factorial1(_ num: Int) -> Int {
    if num > 1 {
        return num * factorial1(num - 1)
    } else {
        return num
    }
}

func factorial2(_ num: Int) -> Int {
    if num <= 1 { return num }
    return num * factorial2(num - 1)
}
    

print("\n===factorial===")
(1...10).forEach { print(factorial1($0)) }
(1...10).forEach { print(factorial2($0)) }



//1부터 num까지의 곱
//1*2*3*4*5와 같다.
func multiple1(_ num: Int) -> Int {
    var result = 1
    for index in 1...num {
        result *= index //result = result * index 와 같은 것
    }
    return result
}


func multiple2(_ num: Int) -> Int {
    if num <= 1 { return num }
    return num * multiple2(num - 1)
}

print("\n===multiple===")
print(multiple1(5))
print(multiple2(5))


//숫자가 들어가있는 리스트가 있을 때 그 합을 구해라
let list = [1,2,3,4,5]

func sumList1(_ array: [Int]) -> Int {
    var result = 0
    for num in array {
        result += num
    }
    return result
}

func sumList2(_ array: [Int]) -> Int {
    return array.reduce(0, +)
}

func sumList3(_ array: [Int]) -> Int {
    var result = array
    if result.count <= 1 { return result.first ?? 0 }
    return result.removeFirst() + sumList3(result) //이렇게도 할 수 있고
}

func sumList4(_ array: [Int]) -> Int {
    let result = array
    if result.count <= 1 { return result.first ?? 0 }
    return (result.first ?? 0) + sumList4(Array(result[1...])) //이렇게 처리할 수도 있겠습니다.
}

print("\n===sumList===")
print(sumList1(list))
print(sumList2(list))
print(sumList3(list))
print(sumList4(list))


//회문, 앞뒤로바꿔도 똑같은지, 기러기/오디오/역삼역/level...
func palindrome1(_ word: String) -> Bool {
    let reversedWord = word.reversed().map {"\($0)"}.joined()
    return word == reversedWord
}

func palindrome2(_ word: String) -> Bool {
    if word.count <= 1 { return true }

    //위에서 1보다 크다는게 통과되었기 떄문에 옵셔널 강제추출을 사용
    if word.first! == word.last! {
        // 파이썬 기준으로 하면 first, last를 제외한 나머지 단어를 이렇게 해서 array[1:-1] 재호출
        let wordArray = word.map { "\($0)"}
        let newWord = wordArray[1...wordArray.count-2].joined()
        return palindrome2(newWord)
    } else {
        return false
    }
}

print("\n===palindrome1===")

print(palindrome1("level"))
print(palindrome1("levzl"))
print(palindrome1("madam"))
print(palindrome1("madem"))
print(palindrome1("kayak"))
print(palindrome1("kaydk"))

print("\n===palindrome2===")

print(palindrome2("level"))
print(palindrome2("levzl"))
print(palindrome2("madam"))
print(palindrome2("madem"))
print(palindrome2("kayak"))
print(palindrome2("kaydk"))


/*
 프로그래밍 연습
 1, 정수 n에 대해
 2. n이 홀수이면 3 X n + 1 을 하고,
 3. n이 짝수이면 n 을 2로 나눕니다.
 4. 이렇게 계속 진행해서 n 이 결국 1이 될 때까지 2와 3의 과정을 반복합니다.
 예를 들어 n에 3을 넣으면,
 3
 10
 5
 16
 8
 4
 2
 1
 이 됩니다.
 이렇게 정수 n을 입력받아, 위 알고리즘에 의해 1이 되는 과정을 모두 출력하는 함수를 작성하세요.
 */

func calculation1(_ num: Int) {
    print("n: \(num)")
    
    var n = num
    while n != 1 {
        if n % 2 == 0 {
            n = n / 2
        } else {
            n = 3 * n + 1
        }
        print("n: \(n)")
    }
}

func calculation2(_ num: Int) {
    print("n: \(num)")
    //대부분은 혹시 놓칠 케이스 때문에 <= 1 이렇게 하는데, 여기서는 문제에서 1 이라고 명시가 되어있어서 그냥 1로.
    if num == 1 { return }
    calculation2((num % 2 == 0) ? (num / 2) : (3 * num) + 1)
}

print("\n===calculation1===")
calculation1(3)
print("\n===calculation2===")
calculation2(3)


/*
 프로그래밍 연습
 문제: 정수 4를 1, 2, 3의 조합으로 나타내는 방법은 다음과 같이 총 7가지가 있음
 1+1+1+1
 1+1+2
 1+2+1
 2+1+1
 2+2
 1+3
 3+1
 정수 n이 입력으로 주어졌을 때, n을 1, 2, 3의 합으로 나타낼 수 있는 방법의 수를 구하시오
 힌트: 정수 n을 만들 수 있는 경우의 수를 리턴하는 함수를 f(n) 이라고 하면,
 f(n)은 f(n-1) + f(n-2) + f(n-3) 과 동일하다는 패턴 찾기
 출처: ACM-ICPC > Regionals > Asia > Korea > Asia Regional - Taejon 2001
 
 정수 1 -> 1가지
 정수 2 -> 2가지
 정수 3 -> 4가지
 정수 4 -> 7가지
 정수 5 -> 13가지
 
 즉, 1,2,3을 제외하고 4부터는 f(n-1) + f(n-2) + f(n-3)의 패턴을 활용할 수 있다는 뜻이다.
 */


func totalCount(_ num: Int) -> Int {
    switch num {
    case 1: return 1
    case 2: return 2
    case 3: return 4
    default: return totalCount(num - 1) + totalCount(num - 2) + totalCount(num - 3)
    }
}

print("\n===totalCount===")
print(totalCount(1))
print(totalCount(2))
print(totalCount(3))
print(totalCount(4))
print(totalCount(5))
print(totalCount(6))    
```
  </p>
</details>

- 함수 안에서 동일한 함수를 호출하는 형태
- 동일한 함수를 계속 사용하면서 변수가 계속 생성되면 공간복잡도는 안좋을 수 있으나, 꽤나 직관적이게 문제를 해결 할 수 있어 여러 알고리즘에 쓰인다. 
- exit 코드를 명확히 하지 않으면 무한 순환에 빠질 수 있다. 
- 재귀 호출은 스택의 전형적인 예이다. 
    - 함수가 내부적으로 스택처럼 관리되고 호출된다. 차례로 하나씩 열리고 차례로 하나씩 닫힌다. 
			
![](https://velog.velcdn.com/images/dev_kickbell/post/b4cc54dd-fae1-4578-9e9d-ca3fed96dc91/image.png)					
			    
## Dynamic Programming(동적 계획법) 
    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드</a></summary>
  <p>

```swift

import Foundation


func processTime(closure: () -> ()){
    let start = CFAbsoluteTimeGetCurrent()
    closure()
    let processTime = CFAbsoluteTimeGetCurrent() - start
    print("경과 시간: \(processTime)\n")
}


/*
 Dynamic Programming(동적계획법)
 
 코드로 보는게 제일 편하지 싶다. 그래도 정의를 정리하자면,
 어떤 문제가 있을 때 작은 부분으로 잘개 쪼개서 그 답을 상위 문제를 해결하는데 재사용한다는 것이다.
 Memoization 기법을 사용하는데 이 말의 뜻은 부분 문제의 해답을 저장해서 재활용하는 최적화 기법이라는 뜻이다.
 코드가 더 이해가 쉽다. 코드로 보자.
 */



/*
 피보나치 수열(Fibonacci numbers)
 - 첫째 및 둘째 항이 1이며 그 뒤의 모든 항은 바로 앞 두 항의 합인 수열
 - 즉 F0: 0, F1: 1, Fn: Fn-1 + Fn-2 이다.
 
 이걸 재귀(recursive)로 푼다면 공식대로 하면 아래와 같다.
 */

func fibonacci_recursive(_ num: Int) -> Int {
    if num <= 1 { return num }
    return fibonacci_recursive(num - 1) + fibonacci_recursive(num - 2)
}

/*
 근데 여기서 재귀를 사용하면 문제점이 있다.
 
 fibonacci_recursive(4)를 예시로 들어보자. 공식대로 하면 fibonacci(4)는 아래와 같아진다.
 
                                fibonacci(4)
                        fibonacci(2) + fibonacci(3)
    
        fibonacci(1) + fibonacci(0)        fibonacci(2) + fibonacci(1)
 
                                    fibonacci(1) + fibonacci(0)
 
 즉, f(4)를 구하기 위해 f(3)를 1번 계산하고 , f(2)를 2번 계산하며, f(1)은 3번계산하고, f(0)도 2번 계산한다.
 작은 계산이 계속 중복이 된다는 것이다.
 재귀는 스택구조와 유사하니 스택처럼 주루룩 쌓였다가 다시 주루룩 해결되면서 내려올거다.
 이런 중복된 계산을 없애고자 하는게 DP 알고리즘 이다. 이걸 DP로 해결해보자.
 */



func fibonacci_dp(_ num: Int) -> Int {
    if num <= 1 { return num } //여기는 공식대로 똑같다.
    
    //1. 먼저 num의 크기만큼의 0이라는 값을 넣은 배열을 만든다.
    var cache = (0...num).map { _ in 0 }
    cache[0] = 0 //2. 그리고 공식대로 0번쨰와 1번째에는 0과 1을 넣어준다.
    cache[1] = 1 //그러면 이런식으로 되겠지. [0, 1, 0, 0, 0, 0, 0, 0, 0, 0]
    
    //3. 그리고 2번째부터 num까지 for문을 돌아주는데, 중요한 건 공식대로 cache[index]에 값을 계산해서 넣어준다.
    //그리고 핵심이 배열에 계산한 값이 저장되어 있을거아니냐? 그럼 그걸 그대로 그냥 꺼내온다. O(1)로 말이다.
    //그리고 그걸 더해줘서 새로운 index에 넣어주는 것이다. 즉, 이미 저장된 작은 값을 다음 계산에 활용해주고 있다.
    for index in 2...num {
        cache[index] = cache[index - 1] + cache[index - 2]
    }
    
    //4. for문의 순회가 끝났다면 자연스럽게 마지막 cache[num]에는 cache[num - 1] + cache[num - 2]
    //값이 들어가 있을 것이다. 그럼 그냥 그걸 리턴해주면 된다.
    return cache[num]
}

//print(fibonacci_dp(0))
//print(fibonacci_dp(1))
//print(fibonacci_dp(2))
//print(fibonacci_dp(3))
//print(fibonacci_dp(4))
//print(fibonacci_dp(5))
//print(fibonacci_dp(6))
//print(fibonacci_dp(7))
//print(fibonacci_dp(8))
//print(fibonacci_dp(9))

/*
 조금 큰 수로 시간을 비교해보자.
 100은 너무오래걸려서 되지도 않고 40으로 하니까
 차이가 몇배냐.. 음 아무튼 겁나 많이 차이가 난다.
 
 102334155
 경과 시간: 1.8227120637893677
 102334155
 경과 시간: 0.00022804737091064453
 */

print("\n===fibonacci===")
processTime { print(fibonacci_recursive(40)) }
processTime { print(fibonacci_dp(40)) }



/*
 2n 타일링
 https://www.acmicpc.net/problem/11726
 첫째 줄에 n이 주어진다. (1 ≤ n ≤ 1,000)
 첫째 줄에 2×n 크기의 직사각형을 채우는 방법의 수를 10,007로 나눈 나머지를 출력한다.
 */

func 타일링_recursive(_ num: Int) -> Int {
    if num <= 2 { return num }
    return (타일링_recursive(num - 1) + 타일링_recursive(num - 2)) % 10007
}

func 타일링_dp(_ num: Int) -> Int {
    if num <= 2 { return num }
    
    var cache = (0...num).map { _ in 0 }
    cache[1] = 1
    cache[2] = 2
    
    for index in 3...num {
        cache[index] = cache[index - 1] + cache[index - 2]
    }
    
    return cache[num] % 10007
}

print("\n===타일링===")
print(타일링_recursive(2))
print(타일링_recursive(9))
print(타일링_dp(2))
print(타일링_dp(9))



/*
 파도반 수열
 https://www.acmicpc.net/problem/9461
 
 첫째 줄에 테스트 케이스의 개수 T가 주어진다. 각 테스트 케이스는 한 줄로 이루어져 있고, N이 주어진다. (1 ≤ N ≤ 100)
 각 테스트 케이스마다 P(N)을 출력한다.
 */

func 파도반수열_dp(_ num: Int) -> Int {
    if num <= 3 { return 1 }
    
    var cache = (1...num + 1).map { _ in 1 }
    
    for index in 4...num {
        cache[index] = cache[index - 3] + cache[index - 2]
    }
    
    return cache[num]
}

print("\n===파도반수열===")
print(파도반수열_dp(1))
print(파도반수열_dp(2))
print(파도반수열_dp(3))
print(파도반수열_dp(4))
print(파도반수열_dp(5))
print(파도반수열_dp(6))
print(파도반수열_dp(7))
print(파도반수열_dp(8))
print(파도반수열_dp(9))
print(파도반수열_dp(10))
```
  </p>
</details>

- 이름이 뭔가 어렵다. 동적 계획법? 다이내믹 프로그래밍? 그냥 DP라고 많이 부른다. 		
- 이름은 어렵지만 개념은 그렇게 어렵지 않다. 어떤 문제가 있을 때, 그것을 해결하기위해 문제를 잘게잘게 쪼개서 그 답을 상위 문제를 해결하는데 재사용하는 방법정도 라고 정의할 수 있다. 			
- 잘게 쪼갠 답을 저장해뒀다가 사용하는 것을 Memoization(메모이제이션)기법 이라고 한다. 
- 위에 코드 예시해서 피보나치 수, 타일링, 파도반 수열을 재귀 호출을 사용한 경우와 비교해서 보면 되겠다. 코드가 공간복잡도 또는 여러번 실행되는 경우없이 최적화 되는 이점이 있다. 
- 주로 사용되는 패턴이 있어서 그것만 숙지하면 어렵지 않다. 
- 왜 사용하나요 ? 
    - 아래 피보나치수를 구하는 문제 해결 방법에서 재귀 호출을 사용하면, f(6)을 구할 때 f(5) -> 1회, f(4) -> 2회, f(3) -> 3회, f(2) -> 5회, f(1) -> 8회, f(0) -> 5회가 실행된다. 즉 코드가 계속 여러번 실행되는 것이다. 하지만 DP를 활용하면 이미 계산된 값 저장해서 재활용(메모이제이션 기법)할 수 있어 코드를 여러번 실행할 필요없이 최적화 된다. ( 사용 방법은 위에 코드 참고 )
				    
![](https://velog.velcdn.com/images/dev_kickbell/post/80eef14d-d718-4104-839b-a8c36df76067/image.png)		
			    
- 실행 코드를 보며 이해해보기: [코드분석](http://www.pythontutor.com/live.html#code=def%20fibo_dp%28num%29%3A%0A%20%20%20%20cache%20%3D%20%20%5B%200%20for%20index%20in%20range%28num%20%2B%201%29%20%5D%0A%20%20%20%20cache%5B0%5D%20%3D%200%0A%20%20%20%20cache%5B1%5D%20%3D%201%0A%20%20%20%20%0A%20%20%20%20for%20index%20in%20range%282,%20num%20%2B%201%29%3A%0A%20%20%20%20%20%20%20%20cache%5Bindex%5D%20%3D%20cache%5Bindex%20-%201%5D%20%2B%20cache%5Bindex%20-%202%5D%0A%20%20%20%20return%20cache%5Bnum%5D%0A%0Aprint%28fibo_dp%2810%29%29&cumulative=false&curInstr=41&heapPrimitives=nevernest&mode=display&origin=opt-live.js&py=3&rawInputLstJSON=%5B%5D&textReferences=false)
				
    
## Divide and Conquer(분할 정복)
- 문제를 나눌 수 없을 때까지 나누어서 각각을 풀면서 다시 합병해서 문제의 답을 얻는 알고리즘
- 하향식 접근법으로 상위 해답을 구하기 위해 아래로 내려가면서 하위 해답을 구하는 방식이다. 
- 일반적으로 재귀함수로 구현한다. 
- 문제를 잘게 쪼갤 때, 부분 문제는 서로 중복되지 않는다. 
- 병합정렬, 퀵정렬을 사용 예시로 들 수있다. ( 분할 정복을 이해하기 위해서는 아래 병합/퀵 정렬을 보자. )

## Dynamic Programming(동적 계획법) vs Divide and Conquer(분할 정복)
- 공통점
    - 문제를 잘게 쪼개서, 가장 작은 단위로 분할한다. 
- 차이점
    - Dynamic Programming
    	- 부분 문제는 중복되어, 상위 문제 해결 시 재활용됨
    	- Memoization 기법 사용 (부분 문제의 해답을 저장해서 재활용하는 최적화 기법으로 사용)
    - Divide and Conquer
    	- 부분 문제는 서로 중복되지 않음
    	- 부분 문제가 중복되지 않으니 당연히 이미 부분 문제의 해답을 따로 저장해서 재활용하는 메모이제이션 기법 또한 사용하지 않는다. 
    
## Quick Sort(퀵 정렬) 

<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드</a></summary>
  <p>

```swift
import Foundation

func quickSort1(_ array: [Int]) -> [Int] {
    if array.count <= 1 { return array }
    
    var left: [Int] = []
    var right: [Int] = []
    let pivot = array.first ?? 0
    
    //pivot이 0번째니까 array.count까지가 아니라 1번째부터 array.count - 1 까지다.
    for index in 1...array.count - 1 {
        if pivot > array[index] {
            left.append(array[index])
        } else {
            right.append(array[index])
        }
    }
    
    return quickSort1(left) + [pivot] + quickSort1(right)
}


func quickSort2(_ array: [Int]) -> [Int] {
    if array.count <= 1 { return array }
    
    let pivot = array.first ?? 0
    let left = array[1...].filter { pivot > $0 }
    let right = array[1...].filter { pivot <= $0 }
    
    return quickSort2(left) + [pivot] + quickSort2(right)
}


print("\n===QuickSort===")

let list = (1...10).map { _ in Int.random(in: 1...100) }
print(list)

print(quickSort1(list))
print(quickSort2(list))

```
  </p>
</details>

- Divide and Conquer(분할 정복) 개념을 사용한다.
- 재귀용법을 활용한 정렬 알고리즘이다. 
- 정렬 알고리즘의 꽃이라고 불린다. 

    
- 분할(Divide)
	- 기준점(pivot 이라고 부름)을 정한다. 통상적으로는 그냥 첫번째 데이터로 한다.
	- 기준점을 기준으로 보다 작은 데이터는 왼쪽(left), 큰 데이터는 오른쪽(right)으로 분할(Divide)한다. 
	- 기준점(pivot)은 리스트가 계속 쪼개질때마다 그 리스트의 첫번째 데이터로 계속 바뀐다.
	- 병합 정렬(Merge Sort) 하고는 조금 다른 점이 합치면서 정렬했던 병합 정렬(Merge Sort)에 반해 Quick Sort는 pivot을 기준으로 나누면서 왼쪽/오른쪽으로 정렬한다. 
	- 재귀용법을 사용해서 다시 동일 함수를 호출하여 리스트가 1개 남을 때까지 분할(Divide)한다.
			
![](https://velog.velcdn.com/images/dev_kickbell/post/c8681bda-61df-4e2e-a69e-4d98572a6fd6/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/47feec0b-8124-41d4-9541-e9fcc484b1aa/image.png)![](https://velog.velcdn.com/images/dev_kickbell/post/18473394-9efd-49a9-b198-3b18f2a87baf/image.png) 
			
- 정복(Conquer)			
    - 이제 분할된 데이터를 다시 합치는데, quickSort의 리턴값은 리스트이다. 그래서 아래의 모양대로 분할된 그대로 다시 리턴하면서 주루룩 더하게 되면 정렬된 데이터가 짜잔 하고 신기하게도 완성된다. ( 이미 분할 하면서 정렬을 했기 때문이다. ) 
    - quickSort(left) + [pivot] + quickSort(right)
    - 자세한 사항은 코드 예시를 참고하자. 
		    
![](https://velog.velcdn.com/images/dev_kickbell/post/38084356-1aec-43fd-92bd-074fe552bb35/image.png)		
    
- 알고리즘 분석
	- 병합정렬과 유사, 시간복잡도는 O(n log n)
    - 단, 최악의 경우 
    	- 맨 처음 pivot이 가장 크거나, 가장 작으면
    	- 모든 데이터를 비교하는 상황이 나옴
    	- O($n^2$)
			    
![](https://www.fun-coding.org/00_Images/quicksortworks.jpg)			
    
## Merge Sort(병합 정렬)
    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드</a></summary>
  <p>

```swift
import Foundation

//1. 분할(Divide)
func split(_ array: [Int]) -> [Int] {
    if array.count <= 1 { return array }
    
    let medium = array.count / 2
    let left = split(Array(array[..<medium]))
    let right = split(Array(array[medium...]))

    return merge(left, right)
}

//2. 정복(Conquer)
func merge(_ left: [Int], _ right: [Int]) -> [Int] {
    var merged: [Int] = []
    var leftIndex = 0
    var rightIndex = 0
    
    //case 1: left, right가 아직 남아있을 때
    while left.count > leftIndex && right.count > rightIndex {
        if left[leftIndex] > right[rightIndex] {
            merged.append(right[rightIndex])
            rightIndex += 1
        } else {
            merged.append(left[leftIndex])
            leftIndex += 1
        }
    }
    //case 2: left만 남아있을 때
    while left.count > leftIndex {
        merged.append(left[leftIndex])
        leftIndex += 1
    }
    
    //case 3: right만 남아있을 때
    while right.count > rightIndex {
        merged.append(right[rightIndex])
        rightIndex += 1
    }
    
    return merged
}

func mergeSort(_ array: [Int]) -> [Int] {
    return split(array)
}



print("\n===mergeSort===")

let list = (1...10).map { _ in Int.random(in: 1...100)}
print(mergeSort(list))
```
  </p>
</details>

- Divide and Conquer(분할 정복) 개념을 사용한다. 
- 재귀용법을 활용한 정렬 알고리즘이다. 
    - 리스트를 절반으로 계속 분할(Divide)한다. 리스트가 1개가 남을 때까지 계속 한다.
    - 합치는데(Conquer), 좌우를 비교해서 정렬하면서 합친다.
		    		
![](https://upload.wikimedia.org/wikipedia/commons/c/cc/Merge-sort-example-300px.gif)
			
- 알고리즘 분석
	- O(n log n)
    - 다음을 보고 이해해보자. (어려우니 참고로만 알아둬도 괜찮다.) 
    	- 몇 단계 깊이까지 만들어지는지를 depth 라고 하고 i로 놓자. 맨 위 단계는 0으로 놓자.
    	- 다음 그림에서 n/$2^2$ 는 2단계 깊이라고 해보자.
    	- 각 단계에 있는 하나의 노드 안의 리스트 길이는 n/$2^2$ 가 된다.
    	- 각 단계에는 $2^i$ 개의 노드가 있다.
    	- 따라서, 각 단계는 항상 <font size=4em>$2^i * \frac { n }{ 2^i } = O(n)$</font>
    	- 단계는 항상 $log_2 n$ 개 만큼 만들어짐, 시간 복잡도는 결국 O(log n), 2는 역시 상수이므로 삭제
    	- 따라서, 단계별 시간 복잡도 O(n) * O(log n) = O(n log n)
			    
![](https://www.fun-coding.org/00_Images/mergesortcomplexity.png)			
				


## 이진탐색..

<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드</a></summary>
  <p>

```swift

```
  </p>
</details>
    

## Reference			
[https://velog.velcdn.com/images/dev_kickbell/post/f4c3753a-b41a-43f7-86ab-f9ee661ae089/image.png](https://velog.velcdn.com/images/dev_kickbell/post/f4c3753a-b41a-43f7-86ab-f9ee661ae089/image.png)	
			
[https://visualgo.net/en/sorting](https://visualgo.net/en/sorting)			

[https://fastcampus.co.kr/dev_online_devjob/?gclid=Cj0KCQjwqc6aBhC4ARIsAN06NmOdZUJGwc8x-w8ht0jS3idCSNhUZ49m--cQdDsVqIZWSnVmrBz5zxMaAtTBEALw_wcB](https://fastcampus.co.kr/dev_online_devjob/?gclid=Cj0KCQjwqc6aBhC4ARIsAN06NmOdZUJGwc8x-w8ht0jS3idCSNhUZ49m--cQdDsVqIZWSnVmrBz5zxMaAtTBEALw_wcB)

[https://www.fun-coding.org/python-question1-answer.html](https://www.fun-coding.org/python-question1-answer.html)				
			    
[https://github.com/kickbell/DataStructure_and_Algorithm](https://github.com/kickbell/DataStructure_and_Algorithm)	
