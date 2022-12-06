## Algorithm(알고리즘)이란 ? 
- 어떤 문제를 풀기 위한 절차 또는 방법 
- 어떤 문제에 대해 특정한 '입력'을 넣으면 원하는 '출력'을 얻을 수 있도록 만드는 프로그래밍을 말한다. 
- 예를 들어, '라면 레시피'라는 알고리즘이 있다. 이 특정한 '입력'은 물 500ml를 몇분![image](https://user-images.githubusercontent.com/85085822/206020402-b3035910-dc60-434b-b5d5-1a992076233b.png)
간끓이고, 면과 스프를 넣으면 라면이라는 '출력'을 얻는 알고리즘이다. 그런데, 라면은 사람마다 끓이는 스타일이 다를 수 있다. 누구는 찬물에 먼저 스프를 넣을 수 있고, 누구는 물을 350ml만 할 수도 있고 또 라면을 10분간 끓일 수도 있다. 결과물은 어찌됐건 같은 '라면'이다. 그러면 어찌됐건 똑같은 라면이라는 '출력'이 나왔다고 했을 때, 무슨 알고리즘을 택해야 할까의 기준은 이러하다. 
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
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
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
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
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
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
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
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
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
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
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
- 문제를 나눌 수 없을 때까지 나누고(분할) 난 후에 각각을 해결(정복)하면서 다시 합병(조합)해서 문제의 답을 얻는 알고리즘
- 하향식 접근법으로 상위 해답을 구하기 위해 아래로 내려가면서 하위 해답을 구하는 방식이다. 
- 일반적으로 재귀함수로 구현한다. 
- 문제를 잘게 쪼갤 때, 부분 문제는 서로 중복되지 않는다. 
- 대표적인 예로 병합정렬, 퀵정렬, 이진탐색을 사용 예시로 들 수있다. 
- 분할과 정복 말고도 나누어서 정복한 문제를 다시 조합해줘야 하는 개념도 있다. 
- 개념설명 ( 정복이라는 의미가 다시 합치는 것?이 아니라 말그대로 문제를 정복한다는 의미이다. 그리고 그걸 다시 조합해야 한다. )
    - 분할(Divide): 문제를 더이상 분할할 수 없을 때까지 동일한 유형의 여러 하위 문제로 나눈다.
    - 정복(Conquer): 가장 작은 단위의 하위 문제를 해결하여 정복한다.
    - 조합(Combine): 하위 문제에 대한 결과를 원래 문제에 대한 결과로 다시 조합한다.

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
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
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
    - 이제 분할된 데이터를 다시 합치(Combine)는데, quickSort의 리턴값은 리스트이다. 그래서 아래의 모양대로 분할된 그대로 다시 리턴하면서 주루룩 더하게 되면 정렬된 데이터가 짜잔 하고 신기하게도 완성된다. ( 이미 분할(Divide) 하면서 정렬(Conquer)을 했기 때문이다. ) 
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
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
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
    - 그런 후에, 좌우를 비교해서 정렬(Conquer)하면서 합친(Combine)다.
		    		
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
				
## Sequential Search(순차 탐색)
    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
  <p>

```swift
import Foundation

func sequencial(_ array: [Int], searchData: Int) -> Int {
    for index in 0..<array.count {
        if array[index] == searchData { return index } //찾는 데이터의 index 반환
    }
    return -1 //찾는 데이터 없음
}

let list = (1...10).map { _ in Int.random(in: 1...100) }
//print(list)

(1...10).forEach { _ in
    print(sequencial(list, searchData: Int.random(in: 1...100)))
}
```
  </p>
</details>

- 탐색은 여러 데이터 중에서 원하는 데이터를 찾아내는 것을 의미
- 데이터가 담겨있는 리스트를 앞에서부터 하나씩 비교해서 원하는 데이터를 찾는 방법
- 이름에 비해 매우 쉬운 탐색 방법이다. 
- 알고리즘 분석
    - 최악의 경우 리스트 길이가 n일 때, n번 비교해야 함
    - O(n)


## Binary Search(이진 탐색)
    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
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
 중요 부분
 1. array[mediumIndex...]
 2. array[..<mediumIndex] // ...으로 하면 터지는데 잘 이해가 안가긴 한다. 
 */
func binarySearch1(_ array: [Int], searchData: Int) -> Bool {
    //제일 마지막까지 데이터를 나누고 마지막 데이터가 찾는 데이터인 경우
    if array.count == 1 && searchData == array.first ?? 0 { return true }
    //제일 마지막까지 데이터를 나누고, 찾는 데이터가 없는 경우
    if array.count == 1 && searchData != array.first ?? 0 { return false }
    
    let mediumIndex = array.count / 2
    
    //제일 마지막까지 나누지 않았어도 데이터를 찾은 경우
    if searchData == array[mediumIndex] { return true }
    
    //그렇지 않은 경우
    if searchData > array[mediumIndex] {
        return binarySearch1(Array(array[mediumIndex...]), searchData: searchData)
    } else {
        //..< 이게 중요한데.. 흠 정확히는 모르겠.
        return binarySearch1(Array(array[..<mediumIndex]), searchData: searchData)
    }
}

/*
 중요 부분
 1. if startIndex > endIndex { return false }
 2. mediumIndex + 1,  mediumIndex - 1
 */
func binarySearch2(_ array: [Int],_ searchData: Int, _ startIndex: Int,_ endIndex: Int) -> Bool {
    if startIndex > endIndex { return false } //중요
    
    let mediumIndex = (startIndex + endIndex) / 2
    
    //제일 마지막까지 나누지 않았어도 데이터를 찾은 경우
    if searchData == array[mediumIndex] { return true}
    
    if searchData > array[mediumIndex] {
        return binarySearch2(array, searchData, mediumIndex + 1, endIndex)
    } else {
        return binarySearch2(array, searchData, startIndex, mediumIndex - 1)
    }
}



/*
 수 찾기
 https://www.acmicpc.net/problem/1920
 
 문제가 이해하기가 약간 어렵다. 뭐 모든 문제가 그렇긴 하지.
 근데 그냥 이거다. 5개의 숫자를 가진 input 4 1 5 2 3 이 있고, 마찬가지로 5개의 숫자를 가진 1 3 7 9 5 이 있다.
 
 N = 5
 N_list = [4 1 5 2 3]
 M = 5
 M_list = [1 3 7 9 5]
 
 여기서부터 찾는거다.
 
 M_list에서 1이 N_list에 포함되면 1, 아니면 0 -> 1
 M_list에서 3이 N_list에 포함되면 1, 아니면 0 -> 1
 M_list에서 7이 N_list에 포함되면 1, 아니면 0 -> 0
 M_list에서 9이 N_list에 포함되면 1, 아니면 0 -> 0
 M_list에서 5이 N_list에 포함되면 1, 아니면 0 -> 1
 */


let N = 5
let N_list = [4, 1, 5, 2, 3]
let M = 5
let M_list = [1, 3, 7, 9, 5]

func find1(_ nlist: [Int],_ mlist: [Int]) {
    for m in mlist {
        if nlist.contains(m) {
//            print("1")
        } else {
//            print("0")
        }
    }
}


func find2(_ nlist: [Int],_ mlist: [Int]) {
    for m in mlist {
        if binarySearch1(nlist, searchData: m) {
//            print("1")
        } else {
//            print("0")
        }
    }
}

func find3(_ nlist: [Int],_ mlist: [Int]) {
    let sortedArray = nlist.sorted()
    
    for m in mlist {
        if binarySearch2(sortedArray, m, 0, sortedArray.count - 1) {
//            print("1")
        } else {
//            print("0")
        }
    }
}

print("\n===수찾기 find1===")
find1(N_list, M_list)

print("\n===수찾기 find2===")
let nlist = N_list.sorted()
find2(nlist, M_list)

print("\n===수찾기 find3===")
find3(nlist, M_list)



print("\n===경과 시간 비교===")
/*
 경과 시간: 0.019398927688598633
 경과 시간: 0.0088731050491333
 경과 시간: 0.006464958190917969
 */

let list1 = (1...1000).map { _ in Int.random(in: 1...100)}
let list2 = (1...1000).map { _ in Int.random(in: 1...100)}

processTime {
        find1(list1, list2)
}

processTime {
        let sortedList = list1.sorted()
        find2(sortedList, list2)
}

processTime {
        let sortedList = list1.sorted()
        find3(sortedList, list2)
}
```
  </p>
</details>

- 탐색할 자료를 둘로 나누어 해당 데이터가 있을만한 곳을 탐색하는 방법 
- Divide and Conquer(분할 정복) 개념을 사용한다.
- 재귀용법을 활용한 정렬 알고리즘이다. 
- 정렬되어야 한다는 조건이 있다. 
- 이진 탐색(Binary Search)과 순차 탐색(Sequential Search)
![](https://www.mathwarehouse.com/programming/images/binary-vs-linear-search/binary-and-linear-search-animations.gif)
- 이진 탐색(Binary Search)과 이진 탐색 트리(Binary Search Tree)
    - 공통점 
    	- 이름이 비슷하다. 그래서 헷갈릴 수 있다. 
    	- 둘 다 주로 검색 용도로 사용되는 알고리즘이다. 
    - 차이점 
    	- 이진 탐색(Binary Search)
    		- 배열 자료구조를 사용한다. 
    		- 데이터가 정렬된 상태에서 검색을 해야 한다는 조건이 있다.
    	- 이진 탐색 트리(Binary Search Tree)
        	- 트리 자료구조를 사용한다.
    		- 정렬과는 관계가 없고, 이진 탐색 트리만의 정책을 따른다. 
				 
![](https://www.mathwarehouse.com/programming/images/binary-search-tree/binary-search-tree-sorted-array-animation.gif)										

- 알고리즘 분석
	- O(logn)
* n개의 리스트를 매번 2로 나누어 1이 될 때까지 비교연산을 k회 진행
    - <font size=4em>n X $\frac { 1 }{ 2 }$ X $\frac { 1 }{ 2 }$ X $\frac { 1 }{ 2 }$ ... = 1</font>
    - <font size=4em>n X $\frac { 1 }{ 2 }^k$ = 1</font>
    - <font size=4em>n = $2^k$ = $log_2 n$ = $log_2 2^k$</font>
    - <font size=4em>$log_2 n$ = k</font>
    - 빅 오 표기법으로는 k + 1 이 결국 최종 시간 복잡도임 (1이 되었을 때도, 비교연산을 한번 수행)
    - 결국 O($log_2 n$ + 1) 이고, 2와 1, 상수는 삭제 되므로, O($log n$)

## Breadth-First Search(너비 우선 탐색)
			
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
  <p>

```swift
import Foundation

/*
 그래프는 아래와 같이 입력
 https://www.fun-coding.org/00_Images/bfsgraph.png
 파이썬은 3.6 버전부터 dict가 입력순으로 정렬된다고 한다.
 swift는 정렬되지 않으니 키값을 기준으로 정렬해서 쓰겠다.
 */

var graph: [String: [String]] = [:]

graph["A"] = ["B", "C"]
graph["B"] = ["A", "D"]
graph["C"] = ["A", "G", "H", "I"]
graph["D"] = ["B", "E", "F"]
graph["E"] = ["D"]
graph["F"] = ["D"]
graph["G"] = ["C"]
graph["H"] = ["C"]
graph["I"] = ["C", "J"]
graph["J"] = ["I"]

let startNode = graph.sorted { $0.key < $1.key }.first?.key ?? ""


/*
 swift에서는 popFirst를 Array에는 없다.
 ArraySlice에만 있기 때문이다.
 그래서 Array에서도 사용하려면, Range<T>를 리턴하는 indices를 사용해서 서브스크립트 문법을 통해 사용한다.
 extension 으로도 만들 수 있다.

 //1. ArraySlice 와 서브스크립트를 활용
 var arr = [1,2,3]
 arr[arr.indices].popFirst()
 
 //2. 불편하다면 extension
 extension Array {
     mutating func popFirst() -> Element? {
         return self[self.indices].popFirst()
     }
 }
 
 //3. removeFirst()는 안되나요 ?
 안된다. 빈배열에 removeFirst 를 하면 크래시가 발생하지만,
 빈배열에 popFirst를 하면 nil이 리턴되기 때문에 다르다.
 */


/*
 중요한 포인트가 몇가지 있다.
 1. swift dict는 python처럼 입력순으로 정렬되지 않는다.
 2. 정렬이 필요한가? 필요하다. startNode를 정하기 위해서. 그것 외에는 필요가 없다.
    let startNode = graph.sorted { $0.key < $1.key }.first?.key ?? ""
 3. Array에는 popFirst()가 없기 때문에 ArraySlice를 사용했다.
 4. popFirst()를 하면 마치 디큐처럼 배열에서 첫번째 데이터는 사라지고 사라진 데이터를 리턴한다.
 5. removeFirst()는 사용할 수 없다. 빈배열에서 사용하면 크래시가 발생하기 때문이다 .
 */
func bfs(_ sortedGraph: [String: [String]], _ startNode: String) -> [String] {
    var visitedQueue: [String] = []
    var needVisitedQueue: [String] = []

    needVisitedQueue.append(startNode)
//    var count = 0 //실행횟수, 시간복잗도 확인
    
    while !needVisitedQueue.isEmpty {
//        count += 1
        let node = needVisitedQueue[needVisitedQueue.indices].popFirst() ?? ""
        if !visitedQueue.contains(node) {
            visitedQueue.append(node)
            needVisitedQueue.append(contentsOf: graph[node] ?? [])
        }
    }

//    print("count : \(count)")
    return visitedQueue
}


print(bfs(graph, startNode))
//["A", "B", "C", "D", "G", "H", "I", "E", "F", "J"]
//bfs 의 시간복잡도는 O(V+E)
//V는 노드수, E는 간선수
//따라서 19
//count : 19
```
  </p>
</details>

   
    
- BFS 와 DFS 는 대표적인 그래프 탐색알고리즘
    - 너비 우선 탐색 (Breadth First Search): 노드와 같은 레벨에 있는 노드들 (형제 노드들)을 먼저 탐색하는 방식
    - 깊이 우선 탐색 (Depth First Search): 노드의 자식들을 먼저 탐색하는 방식
    
- BFS/DFS 방식 이해를 위한 예제 
    - BFS 방식: A - B - C - D - G - H - I - E - F - J 
    	- 한 단계씩 내려가면서, 해당 노드와 같은 레벨에 있는 노드들 (형제 노드들)을 먼저 순회함
    - DFS 방식: A - B - D - E - F - C - G - H - I - J 
    	- 한 노드의 자식을 타고 끝까지 순회한 후, 다시 돌아와서 다른 형제들의 자식을 타고 내려가며 순화함
    	- A - B 로 가든, A - C 로 가든 방향은 상관없다. 깊이 부터 순회하는 정책만 잘 따르면 된다.		
			
![](https://www.fun-coding.org/00_Images/BFSDFS.png)			
    
- 딕셔너리와 배열을 통해서 그래프를 표현할 수 있다. 
    			
![](https://www.fun-coding.org/00_Images/bfsgraph.png)		
		
- 큐 2개를 사용해서 아래의 순서에 따라 데이터를 넣으면 그래프를 구현할 수 있다. 
			    
![](https://www.fun-coding.org/00_Images/bfsqueue.png)	
			
> - 큐 2개(visited queue, need_visited queue)를 활용해서 데이터를 넣는 순서 
> 	1. 맨처음 A를 need_visited queue에 넣는다. 
> 	2. need_visited queue에 들어가있는 데이터 중에 맨 앞에있는 데이터를 꺼낸다.( 데이터는 큐에서 없어진다. ) 
> 	3. 꺼낸 데이터가 visited queue에 있는지 확인한다.
>		- 확인해서 있다면 
>			1. visited queue에 꺼낸 데이터를 넣는다. 
>			2. 그리고 visited queue 넣은 데이터의 키값에 해당되는 value를 need_visited queue 에 순서대로 넣는다. 다음턴으로 넘어간다.
>		- 확인해서 없다면 
>			1. 아무것도 하지 않는다. 다음턴으로 넘어간다.
> 	4. need_visited queue에 들어가있는 데이터 중에 맨 앞에있는 데이터를 꺼낸다.( 데이터는 큐에서 없어진다. ) 
> 	5. 꺼낸 데이터가 visited queue에 있는지 확인한다. ( 아래생략 )
> 	6. need_visited queue에 들어가있는 데이터 중에 맨 앞에있는 데이터를 꺼낸다.( 데이터는 큐에서 없어진다. )
> 	7. 꺼낸 데이터가 visited queue에 있는지 확인한다. ( 아래생략 )
> 	...
> 	8. 끝날때까지 진행한다. 
> 	9. need_visited queue 이 비게 된다면 그래프를 다 순회한 것이므로 종료한다. 

- 시간 복잡도
    - O(n)과 같은 n을 사용하는 방법 말고 O(V + E)로 표시하는 시간복잡도를 갖는다. 
    - 노드 수: V
    - 간선 수: E
    - 위 코드에서 while need_visit 은 V + E 번 만큼 수행함
    - 시간 복잡도: O(V + E)
    

## Depth-First Search(깊이 우선 탐색)
    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
  <p>

```swift

import Foundation

/*
 그래프는 아래와 같이 입력
 https://www.fun-coding.org/00_Images/dfsgraph.png
 파이썬은 3.6 버전부터 dict가 입력순으로 정렬된다고 한다.
 swift는 정렬되지 않으니 키값을 기준으로 정렬해서 쓰겠다.
 */

var graph: [String: [String]] = [:]

graph["A"] = ["B", "C"]
graph["B"] = ["A", "D"]
graph["C"] = ["A", "G", "H", "I"]
graph["D"] = ["B", "E", "F"]
graph["E"] = ["D"]
graph["F"] = ["D"]
graph["G"] = ["C"]
graph["H"] = ["C"]
graph["I"] = ["C", "J"]
graph["J"] = ["I"]

let startNode = graph.sorted { $0.key < $1.key }.first?.key ?? ""

/*
 중요한 포인트가 몇가지 있다.
 1. swift dict는 python처럼 입력순으로 정렬되지 않는다.
 2. 정렬이 필요한가? 필요하다. startNode를 정하기 위해서. 그것 외에는 필요가 없다.
    let startNode = graph.sorted { $0.key < $1.key }.first?.key ?? ""
 3. 그림과는 다른 결과값이 나왔는데, 방향의 문제이지 dfs의 정책을 위반하지 않는다.
    무슨 말이냐면 그림은 왼쪽부터 돌아서 ABDEFCGHIJ 순이지만 우리 결과값은 ACIJHGBDFE 이다.
    이것은 오른쪽 먼저하냐 왼쪽먼저 하냐의 방향의 차이니까 dfs의 정책을 무시한 것이 아니다.
    그렇다면 데이터가 들어가 있는 value의 순서는 상관이 없나? 그건 아닌 것 같은데?
 */
func dfs(_ sortedGraph: [String: [String]], _ startNode: String) -> [String] {
    var visitedQueue: [String] = []
    var needVisitedStack: [String] = []

    needVisitedStack.append(startNode)
//    var count = 0 //실행횟수, 시간복잗도 확인
    
    while !needVisitedStack.isEmpty {
//        count += 1
        let node = needVisitedStack.popLast() ?? ""
        if !visitedQueue.contains(node) {
            visitedQueue.append(node)
            needVisitedStack.append(contentsOf: graph[node] ?? [])
        }
    }

//    print("count : \(count)")
    return visitedQueue
}


print(dfs(graph, startNode))
//["A", "C", "I", "J", "H", "G", "B", "D", "F", "E"]
//bfs 의 시간복잡도는 O(V+E)
//V는 노드수, E는 간선수
//따라서 19
//count : 19
```
  </p>
</details>    

- BFS 와 DFS 는 대표적인 그래프 탐색알고리즘
    - 너비 우선 탐색 (Breadth First Search): 노드와 같은 레벨에 있는 노드들 (형제 노드들)을 먼저 탐색하는 방식
    - 깊이 우선 탐색 (Depth First Search): 노드의 자식들을 먼저 탐색하는 방식
    
- BFS/DFS 방식 이해를 위한 예제 
    - BFS 방식: A - B - C - D - G - H - I - E - F - J 
    	- 한 단계씩 내려가면서, 해당 노드와 같은 레벨에 있는 노드들 (형제 노드들)을 먼저 순회함
    - DFS 방식: A - B - D - E - F - C - G - H - I - J 
    	- 한 노드의 자식을 타고 끝까지 순회한 후, 다시 돌아와서 다른 형제들의 자식을 타고 내려가며 순화함
    	- A - B 로 가든, A - C 로 가든 방향은 상관없다. 깊이 부터 순회하는 정책만 잘 따르면 된다.		
					
![](https://www.fun-coding.org/00_Images/BFSDFS.png)			
    
- 딕셔너리와 배열을 통해서 그래프를 표현할 수 있다. 
    					
![](https://www.fun-coding.org/00_Images/dfsgraph.png)		
		
- 큐 1개, 스택 1개를 사용해서 아래의 순서에 따라 데이터를 넣으면 그래프를 구현할 수 있다. 
								    
![](https://velog.velcdn.com/images/dev_kickbell/post/add77935-4cf9-497e-b9cd-47326d19b85c/image.png)						
> - 큐(visited queue) 와 스택(need_visited stack)를 활용해서 데이터를 넣는 순서 
> 	1. 맨처음 A를 need_visited stack에 넣는다. 
> 	2. need_visited stack에 들어가있는 데이터 중에 마지막에 있는 데이터를 꺼낸다.( 스택이니까. ) 
> 	3. 꺼낸 데이터가 visited queue에 있는지 확인한다.
> 		- 확인해서 있다면 
>			1. visited queue에 꺼낸 데이터를 넣는다. 
>			2. 그리고 visited queue 넣은 데이터의 키값에 해당되는 value를 need_visited stack에 넣는다. 다음턴으로 넘어간다.
>		- 확인해서 없다면 
>			1. 아무것도 하지 않는다. 다음턴으로 넘어간다.
> 	4. need_visited stack에 들어가있는 데이터 중에 마지막에 있는 데이터를 꺼낸다.( 스택이니까. ) 
> 	5. 꺼낸 데이터가 visited queue에 있는지 확인한다. ( 아래생략 )
>  	6. need_visited stack에 들어가있는 데이터 중에 마지막에 있는 데이터를 꺼낸다.( 스택이니까. ) 
>  	7. 꺼낸 데이터가 visited queue에 있는지 확인한다. ( 아래생략 )
>  	...
>  	8. 끝날때까지 진행한다. 
>  	9. need_visited stack 이 비게 된다면 그래프를 다 순회한 것이므로 종료한다. 
    
- 시간 복잡도
    - O(n)과 같은 n을 사용하는 방법 말고 O(V + E)로 표시하는 시간복잡도를 갖는다. 
    - 노드 수: V
    - 간선 수: E
    - 위 코드에서 while need_visit 은 V + E 번 만큼 수행함
    - 시간 복잡도: O(V + E)
    
    
## Greedy Algorithm(탐욕 알고리즘)
    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
  <p>

```swift

import Foundation

/*
 ### 문제1: 동전 문제
   - 지불해야 하는 값이 4720원 일 때 1원 50원 100원, 500원 동전으로 동전의 수가 가장 적게 지불하시오.
     - 가장 큰 동전부터 최대한 지불해야 하는 값을 채우는 방식으로 구현 가능
     - 탐욕 알고리즘으로 매순간 최적이라고 생각되는 경우를 선택하면 됨
 */
let coins = [500, 50, 1, 100]

func minCoinCount(price: Int, coins: [Int]) -> (Int, [(Int, Int)]) {
    var totalCoinCount = 0
    var history: [(Int, Int)] = []
    var price = price
    let sortedCoins = coins.sorted(by: >) //[500, 100, 50, 1], reverse
    
    for coin in sortedCoins {
        let coinNum = price / coin
        totalCoinCount += coinNum
        price -= coinNum * coin
        history.append((coin, coinNum))
    }
    
    return (totalCoinCount, history)
}

print("\n===동전 문제===")
print(minCoinCount(price: 4720, coins: coins))


/*
 ### 문제2: 부분 배낭 문제 (Fractional Knapsack Problem)
   - 무게 제한이 k인 배낭에 최대 가치를 가지도록 물건을 넣는 문제
     - 각 물건은 무게(w)와 가치(v)로 표현될 수 있음
     - 물건은 쪼갤 수 있으므로 물건의 일부분이 배낭에 넣어질 수 있음, 그래서 Fractional Knapsack Problem 으로 부름
       - Fractional Knapsack Problem 의 반대로 물건을 쪼개서 넣을 수 없는 배낭 문제도 존재함 (0/1 Knapsack Problem 으로 부름)
     <img src="https://www.fun-coding.org/00_Images/knapsack.png">
 */

typealias Bag = (weight: Double, value: Double)
typealias History = (weight: Double, value: Double, percent: Double)
typealias ReturnValue = (totalValue: Double, history: [History])

func maxValue(_ bags: [Bag],
              _ limitWeight: Double) -> ReturnValue {
    let sortedBags = bags.sorted { $0.value/$0.weight > $1.value/$1.weight } //더 높은 가치순으로 정렬
    var totalValue: Double = 0
    var history: [History] = []
    var limitWeight = limitWeight
    
    for bag in sortedBags {
        if limitWeight - bag.weight >= 0 {
            //더 넣을 공간이 남아있다면
            limitWeight -= bag.weight
            totalValue += bag.value
            history.append((bag.weight, bag.value, 1.0))
        } else {
            //넣을거 다 넣고 쪼개서 넣는거라면
            let fraction = limitWeight / bag.weight //쪼갠부분이 1.0을 기준으로 0.?인지 계산
//            limitWeight -= bag.weight 이건 필요가 없다. 왜? 어차피 마지막이라 0보다 작거나 같을테니까
            totalValue += (bag.value * fraction) // 이 부분 중요
            history.append((bag.weight, bag.value, fraction))
            break //마지막이니 꼭 끝내줘야 한다.
        }
    }
    
    return (totalValue, history)
}

print("\n===부분 배낭 문제===")
let bags: [Bag] = [(10,10), (15,12), (20,10), (25,8), (30,5)]
let limitWeight = 30.0

let result = maxValue(bags, limitWeight)
print(result.totalValue)
print(result.history.forEach { print($0)})



/*
 ATM 인출기
 https://www.acmicpc.net/problem/11399
 
 인하은행에는 ATM이 1대밖에 없다. 지금 이 ATM앞에 N명의 사람들이 줄을 서있다. 사람은 1번부터 N번까지 번호가 매겨져 있으며, i번 사람이 돈을 인출하는데 걸리는 시간은 Pi분이다.
 사람들이 줄을 서는 순서에 따라서, 돈을 인출하는데 필요한 시간의 합이 달라지게 된다. 예를 들어, 총 5명이 있고, P1 = 3, P2 = 1, P3 = 4, P4 = 3, P5 = 2 인 경우를 생각해보자. [1, 2, 3, 4, 5] 순서로 줄을 선다면, 1번 사람은 3분만에 돈을 뽑을 수 있다. 2번 사람은 1번 사람이 돈을 뽑을 때 까지 기다려야 하기 때문에, 3+1 = 4분이 걸리게 된다. 3번 사람은 1번, 2번 사람이 돈을 뽑을 때까지 기다려야 하기 때문에, 총 3+1+4 = 8분이 필요하게 된다. 4번 사람은 3+1+4+3 = 11분, 5번 사람은 3+1+4+3+2 = 13분이 걸리게 된다. 이 경우에 각 사람이 돈을 인출하는데 필요한 시간의 합은 3+4+8+11+13 = 39분이 된다.
 줄을 [2, 5, 1, 4, 3] 순서로 줄을 서면, 2번 사람은 1분만에, 5번 사람은 1+2 = 3분, 1번 사람은 1+2+3 = 6분, 4번 사람은 1+2+3+3 = 9분, 3번 사람은 1+2+3+3+4 = 13분이 걸리게 된다. 각 사람이 돈을 인출하는데 필요한 시간의 합은 1+3+6+9+13 = 32분이다. 이 방법보다 더 필요한 시간의 합을 최소로 만들 수는 없다.
 줄을 서 있는 사람의 수 N과 각 사람이 돈을 인출하는데 걸리는 시간 Pi가 주어졌을 때, 각 사람이 돈을 인출하는데 필요한 시간의 합의 최솟값을 구하는 프로그램을 작성하시오.
 */


let timeList = [3, 1, 4, 3, 2]


/*
 되게 참신?한 방법이 쓰인다. 아래 이중포문 참고
 */
func minTotalTimeUseForLoop(_ timeList: [Int]) -> Int {
    let sortedTimeList: [Int] = timeList.sorted()
    var minTotalTime = 0
    for index in 0...timeList.count - 1 {
        for index2 in 0...index {
            //결국에 [1,2,3,3,4] 라는 리스트를 받았을 떄, 1, 1+2, 1+2+3, 1+2+3+3, 1+2+3+3+4처럼 동작해야 했다.
            //즉 0번째까지 더하기, 0~1번째까지 더하기, 0~2번째까지 더하기, 0~3번째까지 더하기, 0~4번째까지 더하기 처럼 말이다.
            //그래서 여기서는 이중포문에 0...index + 1를 써서 그걸 해결했다. 신박하다?.. 굿잡..
            //print(index, index2)
            minTotalTime += sortedTimeList[index2]
        }
    }
    return minTotalTime
}


//참고로 난 이렇게 풀었는데, 위에 선생님 풀이가 더 나은 것 같다.
func minTotalTime(_ timeList: [Int]) -> Int {
    let sortedTimeList: [Int] = timeList.sorted()
    var totalTime: Int = 0
    var history: [Int] = []
    
    for work in sortedTimeList {
        totalTime += work
        history.append(totalTime)
    }
    
    return history.reduce(0, +)
}

print("\n===ATM 인출기===")
//1. 선생님 풀이
print(minTotalTimeUseForLoop(timeList))
//2. 나의 풀이
print(minTotalTime(timeList))
```
  </p>
</details>
    
    
    

- 최적의 해에 가까운 값을 구하기 위해 사용됨
- 여러 경우 중 하나를 결정해야할 때마다, **매순간 최적이라고 생각되는 경우를 선택**하는 방식으로 진행해서, 최종적인 값을 구하는 방식
- 탐욕 알고리즘의 한계
    - 탐욕 알고리즘은 근사치 추정에 활용
    - 반드시 최적의 해를 구할 수 있는 것은 아니기 때문
    - 최적의 해에 가까운 값을 구하는 방법 중의 하나임				
    
![](https://www.fun-coding.org/00_Images/greedy.png)			
    		
- '시작' 노드에서 시작해서 가장 작은 값을 찾아 leaf node 까지 가는 경로를 찾을 시에
    - Greedy 알고리즘 적용시 시작 -> 7 -> 12 를 선택하게 되므로 7 + 12 = 19 가 됨 
    - 하지만 실제 가장 작은 값은 시작 -> 10 -> 5 이며, 10 + 5 = 15 가 답
    
    
## Dijkstra Algorithm(다익스트라 알고리즘)	
		    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
  <p>

```swift
import Foundation 

let graph: [String: [String: Int]] = [
    "A":["B":8, "C":1, "D":2],
    "B":[:],
    "C":["B":5, "D":2],
    "D":["E":3, "F":5],
    "E":["F":1],
    "F":["A":5]
]


/*
 ### 다익스트라 알고리즘
 - 탐색할 그래프의 시작 정점과 다른 정점들간의 최단 거리 구하기
 */
func dijkstra(graph: [String: [String: Int]], startNode: String) -> [String: Int] {
    //1. 초기화
    var distances: [String: Int] = [:]
    var priorityQueue = PriorityQueue<(distance: Int, node: String)>(sort:<) //우선순위큐의 구현은 github에 첨부되어 있음.
    graph.forEach { distances[$0.key] = Int.max }
    distances[startNode] = 0
    priorityQueue.enqueue((distances[startNode] ?? 0, startNode))
    
    //2. 구현부
    while !priorityQueue.isEmpty {
        let item = priorityQueue.dequeue() ?? (0, "")
        let currentDistance = item.distance
        let currentNode = item.node
        
        if distances[currentNode] ?? 0 < currentDistance { continue }
        
        //adjacent 인접한
        for (adjacentNode, adjacentDistance) in graph[currentNode] ?? ["":0] {
            let distance = currentDistance + adjacentDistance
            
            if distance < (distances[adjacentNode] ?? 0) {
                distances[adjacentNode] = distance
                priorityQueue.enqueue((distance, adjacentNode))
            }
        }
    }
    
    return distances
}

print("\n===dijkstra===")
let result = dijkstra(graph: graph, startNode: "A").sorted { $0.key < $1.key }
print(result.forEach { print($0) })



/*
 ### 참고: 최단 경로 출력
 - 탐색할 그래프의 시작 정점과 다른 정점들간의 최단 거리 및 최단 경로 출력하기
 */
func dijkstra(graph: [String: [String: Int]], startNode: String, endNode: String) -> [String : (distance: Int, node: String)] {
    //1. 초기화
    //위에랑 다른점은 distances의 value값이 Int -> (distance: Int, node: String)로 바뀌면서 초기화 코드가 싹 바뀜.
    var distances: [String: (distance: Int, node: String)] = [:]
    var priorityQueue = PriorityQueue<(distance: Int, node: String)>(sort:<)
    graph.forEach { distances[$0.key] = (Int.max, $0.key) }
    distances[startNode] = (0, startNode)
    priorityQueue.enqueue((distances[startNode]?.distance ?? 0, startNode))
    
    //2. 구현부
    //구현부도 바뀐쪽은 1번과 같음.
    while !priorityQueue.isEmpty {
        let item = priorityQueue.dequeue() ?? (0, "")
        let currentDistance = item.distance
        let currentNode = item.node
        
        if distances[currentNode]?.distance ?? 0 < currentDistance { continue }
        
        //adjacent 인접한
        for (adjacentNode, adjacentDistance) in graph[currentNode] ?? ["":0] {
            let distance = currentDistance + adjacentDistance
            
            if distance < (distances[adjacentNode]?.distance ?? 0) {
                distances[adjacentNode] = (distance, currentNode)
                priorityQueue.enqueue((distance, adjacentNode))
            }
        }
    }
    
    //3. 바뀐 부분을 사용해서 아래와 같이
    //start to end까지 최단 경로를 출력하기 위한 로직이 추가됨.
    var path = endNode
    var pathOutput = endNode + " <- "
    while distances[path]?.node ?? "" != startNode {
        let node = distances[path]?.node ?? ""
        pathOutput += node + " <- "
        path = distances[path]?.node ?? ""
    }
    pathOutput += startNode
    print("1. startNode로 부터 endNode까지의 최단경로")
    print(pathOutput)
    
    return distances
}


print("\n===dijkstra + start to end===")
let result2 = dijkstra(graph: graph, startNode: "A", endNode: "F").sorted { $0.key < $1.key }

print("\n2. startNode로 부터 다른 노드들 사이의 최단거리")
print(result2.forEach { print($0) })

```
  </p>
</details>    
		
		
			

    
- 그래프 자료구조를 사용한 두 노드를 잇는 최단거리를 찾는 알고리즘 
- 가중치(Weight)그래프에서 간선(Edge)의 가중치 합이 최소가 되도록 하는 경로를 찾는 것이 목적이다. 
- 최단 경로 문제의 종류 
    1. 단일 출발 및 단일 도착 최단 경로 문제
    	- 그래프 내의 특정 노드 u 에서 출발, 또다른 특정 노드 v 에 도착하는 가장 짧은 경로를 찾는 문제
    2. 단일 출발 최단 경로 문제(SSP) 
    	- single-source shortest path problem
    	- 그래프 내의 특정 노드 u 와 그래프 내 다른 모든 노드 각각의 가장 짧은 경로를 찾는 문제
		- A, B, C, D 라는 노드가 있고 시작점을 A라고 하면 A - B, A - C, A - D 에 대한 최단경로를 찾는 문제  
    3. 전체 쌍 최단 경로 문제(ASP): 
    	- all pairs shortest path problem
    	- 그래프 내의 모든 노드 쌍 (u, v) 에 대한 최단 경로를 찾는 문제
    	- A - B, A - C, A - D, B - C, B - D... 
- 변형이 많은 알고리즘인데 일반적으로는 2번을 뜻한다. 
- 가장 개선된 변형인 우선순위 큐를 활용한 버전을 볼 것이고, 아래그림에서 A->B,C,D->E,F 식으로 순회하기 때문에 실질적으로 마치 너비우선탐색(BFS)처럼 순회한다. 

- 다익스크라 알고리즘을 이해하기 위한 예제 
		
![](https://www.fun-coding.org/00_Images/dijkstra.png)			
		
- 1. 초기화 
    - 첫 노드를 기준으로 배열을 생성하고, 초기값을 할당한다. 
    - 첫 노드의 초기값은 0, 나머지는 무한대로 저장한다. 
    - 우선순위 큐에 튜플 타입으로 (첫 노드, 0)을 먼저 넣는다.		
			
![](https://www.fun-coding.org/00_Images/dijkstra_initial.png)		

- 2. A 노드 기준 거리 계산 			
    - 우선순위 큐에서 하나를 뺀다. (처음이니 첫 노드가 꺼내진다.) 
    - 꺼낸 노드에서 갈 수 있는 노드는 B, C, D 이다. (F로는 못간다. 방향 그래프라서 화살표가 있다.)
    - 꺼낸 노드에서 각 B, C, D 노드로 갈 수 있는 거리와 생성한 배열의 거리를 비교해서 더 짧으면 바꿔준다. 그렇지 않으면 바꾸지 않는다. 
    - 바꿔지면 큐에 넣는다. 다만 저 큐는 우선순위큐(min heap)이므로 늦게 넣었어도 가중치(weight)를 기준으로 넣을때마다 정렬된다. 
    - 첫 노드 기준 한바퀴를 다 돌았다. 
			
![](https://www.fun-coding.org/00_Images/dijkstra_1st.png)			
				    	
- 3. C 노드 거리 계산 (우선순위 큐에서 꺼낸 데이터) 
	- 우선순위 큐에서 데이터를 하나 꺼낸다. min heap 이므로 (C, 1)이 꺼내진다. 
    - 기존에 A를 기준으로 순회했다면, 이번에는 C를 기준으로 순회하는 것이다. 
    - C를 기준으로 보자. C에서는 C->D, C->B가 있다. 
    	- C->D
    		- 지금 생성한 배열을 기준으로 보면 (A,0) (C,1) 이다. 이 말은 A->C까지의 거리가 1이라는 말이다. 
    		- 그리고 C->D의 거리는 2이다. 그런데, A->D는 (A,0) (D,2)로 2이다. 즉, A->C->D가 A->D 보다 더 늦다.
    		- 이러면 아무것도 하지 않는다. 그냥 큐에서 데이터만 하나 빠진것이다. 
    	- C->B
    		- C->B를 보자. 얘는 A->B는 8인데, A->C->B는 1+5 = 6이다. 
    		- 이러면 아까전에 했던 것처럼 A->C->B 가 A->B 보다 낮으니 배열을 6으로 업데이트하고 (B,6)을 큐에 넣는다. 
    		- 주의할 점은 실질적으로 큐에는 (6,B)로 들어간다. (데이터를 꺼낼때 앞에 거를 기준으로 빼오기 때문에) 또, 지금 그림을 보면 이미 (B,8)이 있다. 그럼에도 (B,6)으로 새로 들어가는 것이다. 
    		- 자, 이제 C에서 갈 수 있는 방향은 다 돌았으니 한바퀴가 또 끝났다. 		
		    		
![](https://www.fun-coding.org/00_Images/dijkstra_2nd.png)			
				    
- 4. D 노드 거리 계산 (우선순위 큐에서 꺼낸 데이터) 		
	- 같은 방식으로 진행한다. 			
    - D에서는 D->E, D->F가 있다. 
    - 배열에 현재 저장된 A->D가 2인데, 그림상으로 E까지는 2+3=5, F까지는 2+5=7이다. 하지만 배열에는 inf(무한대)가 저장되어 있다. 비교해서 당연히 거리가 더 짧으므로 배열을 바꿔주고 우선순위 큐에 넣어준다. 
    - 한바퀴가 또 끝났다. 
			    
![](https://www.fun-coding.org/00_Images/dijkstra_3rd.png)			
				
- 5. E 노드 거리 계산 (우선순위 큐에서 꺼낸 데이터) 	
    - E 기준으로 진행한다. 
    - 배열을 보면 A->E는 5이다. 그리고 그림을 보면 E에서 갈 수 있는 곳은 F밖에 없고 거리는 1이다. 1+5인데 배열에는 A->F가 7로 되어있으므로 배열을 업데이트하고 큐에 데이터를 넣어준다. 
    - 한바퀴가 또 끝났다. 
				
![](https://www.fun-coding.org/00_Images/dijkstra_3-2th.png)				
				    
- 5. B,F 노드 거리 계산 (우선순위 큐에서 꺼낸 데이터) 	
    - B 기준으로 진행한다. B가 2개지만 weight가 제일 낮은 6이 최소힙에서 꺼내진다. 
    - B 는 갈 수 있는 데가 없다 종료한다. 
    - 다시 데이터를 꺼낸다. F, 6이 나왔다. 
    - F는 배열을 보니 A->F가 6인데, F->A 가 5이다. 하지만, A->A는 0이다. 따라서 아무것도 하지 않는다.
    - 한바퀴가 또 끝났다. (사실상 두바퀴였나) 
			    	
![](https://www.fun-coding.org/00_Images/dijkstra_4th.png)			
				
- 6. F,B 노드 거리 계산 (우선순위 큐에서 꺼낸 데이터) 	
    - F 기준으로 진행한다. (F,7)이 나왔다. 어차피 계산을 해도 바로 위에처럼 아무것도 안할 것이지만, 여기선 최적화를 진행한다.
    - 뭐냐면, A->F는 6이다. 그리고 지금 꺼낸 큐에서 나온 데이터는 (F,7)이다. 그러면 큐에서 나온 데이터는 뭔짓을 해도 A->F보다 작을 수 는 없는 거다. 그렇지? 왜냐 A->F는 6이니까. 이후에 계산할 작업인 F->A는 어차피 둘 다 더할 값이기 때문이다. (만약에 F->A가 아니라 F->G 였더라도) 
    - 정리하면, 현재 배열의 거리가 큐에서 꺼낸 거리보다 작다면 그 이후를 계산할 필요없이 바로 다음 작업을 진행하면 된다. 
	- F는 끝났고, 이제 B를 진행한다. 방금 말했던 것처럼 배열의 B는 6이고, 큐에서 꺼낸 데이터는 8이니 아무것도 진행하지 않는다. 
	
- 7. 종료 
    - 우선순위 큐가 비어있으니 종료한다. 
    - 현재 배열의 남아있는 데이터가 바로 첫 노드인 A를 기준으로 각 노드까지의 최단 경로이다. 
    - 이 방법의 장점은 우선순위 큐를 사용하므로, minheap에서 가장 짧은 거리가 먼저 나와서 그거 기준으로 계산하기 때문에 더 긴 거리의 루트에 대해서는 계산을 스킵할 수 있다. 
    - 무슨 말이냐면, 위에서 처럼 짧은 거리부터 계산을 하기 때문에 그것부터 계산해서 배열이 딱 세팅되어 있다. 그런데, 그 배열의 데이터보다 큰 데이터가 큐에서 나오면 그건 계산의 의미가 없으므로 스킵해버릴 수 있는 것이다. 
    
    
- 시간 복잡도
    - 위 다익스트라 알고리즘은 크게 다음 두 가지 과정을 거침
	- 과정1: 각 노드마다 인접한 간선들을 모두 검사하는 과정
    	- A를 기준으로하면 B,C,D를 꼭 다 한번씩 검사하고 C를 기준으로 하면 B,D를 검사하고 하는 식이다. 
    	- 즉 모든 간선(Edge)들은 다 한번씩 검사한다. 그러므로 O(E). 
    - 과정2: 우선순위 큐에 노드/거리 정보를 넣고 삭제(pop)하는 과정
    	- 우선순위 큐에 가장 많은 노드, 거리 정보가 들어가는 시나리오는 그래프의 모든 간선이 검사될 때마다, 배열의 최단 거리가 갱신되고, 우선순위 큐에 노드/거리가 추가되는 것임
    	- 이 때 추가는 각 간선마다 최대 한 번 일어날 수 있으므로, 최대 O(E)의 시간이 걸리고, O(E) 개의 노드/거리 정보에 대해 우선순위 큐를 유지하는 작업은 $ O(log{E}) $ 가 걸림 
    	- 따라서 해당 과정의 시간 복잡도는 $ O(Elog{E}) $ 
		    
- 총 시간 복잡도
    - 과정1 + 과정2 = O(E) + $ O(Elog{E}) $  = $ O(E + Elog{E}) = O(Elog{E}) $
		  
- heap의 시간 복잡도
    - 우선순위 큐는 배열로 구현하면 쉽지만, heap으로 내부적으로 구현하는 것이 제일 효율이 좋다. 
    - depth (트리의 높이) 를 h라고 표기한다면,
    - n개의 노드를 가지는 heap 에 데이터 삽입 또는 삭제시, 최악의 경우 root 노드에서 leaf 노드까지 비교해야 하므로  h=log2n  에 가까우므로, 시간 복잡도는  O(logn)
    
## Minimum Spanning Tree(최소 신장 트리)
- 신장 트리(Spanning Tree)란?
    - 모든 노드가 연결되어 있으면서 트리 자료구조의 속성을 만족하는 그래프 
    - 트리 자료구조의 속성이란, 노드와 브랜치를 이용해서 싸이클을 이루지 않도록 구성한다는 뜻
    - 트리 또한 그래프의 한 종류
    - 트리는 트리자료구조인데, 아래의 조건을 다 만족하는 트리
    	1. 그래프의 모든 노드를 포함해야 한다. 
    	2. 모든 노드가 서로 연결되어야 한다. 
    	3. 싸이클이 존재하지 않아야 한다. 
    - 아래의 그림을 보면 더 이해가 쉽다. 왼쪽의 1개가 오리지널 그래프이고, 오른쪽의 8개가 1개의 오리지널 그래프에서 위의 3가지 조건을 만족하여 나올 수 있는 신장 트리들을 그려놓은 것이다.
    
![](https://www.fun-coding.org/00_Images/spanningtree.png)    
    
- 최소 신장 트리(Minimum Spanning Tree)란? 
    - 그래프에 가중치(weight)가 있는 그래프가 있다고 하자. 
    - 그리고 그 녀석은 신장트리 조건을 만족한다. (모든 노드 포함, 연결, 싸이클 없음) 
    - 그리고 그렇게 가능한 신장트리 중에 가중치의 합이 최소인 신장트리를 최소 신장 트리라고 말한다. 
    - 아래 사진을 보면 오른쪽 MST가 신장 트리 중에 1+2+3으로 최소인 것을 볼 수 있다. 1+5+3 안되고, 1+5+7, 5+3+7도 안된다.
    - 최소 신장 트리를 구하는 대표적인 알고리즘이 크루스칼(Kruskal’s), 프림(Prim's) 알고리즘(algorithm) 이다. 
			    
![](https://www.fun-coding.org/00_Images/mst.png)			
				    
- 최소 신장 트리(Minimum Spanning Tree)는 왜 사용하나요 ? 
    - 우리가 알지 못핧 뿐, 우리 실생활에 아주 밀접하게 사용되고 있다. 가중치(weight)가 늘어난다는 것은 거리가 늘어난다는 것이고, 그것이 지하철이 됐든 전기선이 됐든 터무니 없는 금액의 공사 비용이 증가하는 것을 의미한다. 
    - 어떤 노드와 노드를 전부 연결하되, 가능한 최소의 비용으로 연결해야 하는 경우 
    - 도로 건설: 도시를 모두 연결하여 도로 길이 최소화
    	- 지하철 노선 
    	- 고속도로, 국도 
    	- 기찻길 
    - 통신: 전화선의 길이가 최소화 되도록 케이블망 구축 
    - 전기: 전력선의 길이가 최소화 되도록 전선망을 구축 
    - 네트워크: 라우터 경로를 설정하는데, 최적의 라우팅 경로를 선택 
    - 배관: 파이프를 모두 연결하되 총 길이를 최소로 연결
    	- 도시 하수도 
    	- 대형 선박 내의 배관
					
![](https://velog.velcdn.com/images/dev_kickbell/post/c6220515-e574-4718-b8c4-6c3dd1f3a718/image.png)		
							

## Kruskal's Algorithm(크루스칼 알고리즘)			
    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
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
 그래프는 아래의 링크 그래프를 예시로 작성한다.
 https://velog.velcdn.com/images/dev_kickbell/post/745e799d-2fcd-4c01-a617-e4e788fd369e/image.png
 */


let graph: [String: Any] = [
    "nodes": ["A", "B", "C", "D", "E", "F", "G"],
    "edges": [
        (7, "A", "B"), //노드의 앞 뒤 순서도 중요
        (5, "A", "D"),
        (7, "B", "A"),
        (8, "B", "C"),
        (9, "B", "D"),
        (7, "B", "E"),
        (8, "C", "B"),
        (5, "C", "E"),
        (5, "D", "A"),
        (9, "D", "B"),
        (7, "D", "E"),
        (6, "D", "F"),
        (7, "E", "B"),
        (5, "E", "C"),
        (7, "E", "D"),
        (8, "E", "F"),
        (9, "E", "G"),
        (6, "F", "D"),
        (8, "F", "E"),
        (11, "F", "G"),
        (9, "G", "E"),
        (11, "G", "F")
    ]
]

typealias Edge = (weight: Int, fromNode: String, toNode: String)

var parent: [String : String] = [:] //노드 : 루트노드 을 리턴해주는 딕셔너리
var rank: [String : Int] = [:] //노드 : 랭크값 을 리턴해주는 딕셔너리

//1. 초기화(Union Find 알고리즘)
func makeSet(_ node: String) {
    parent[node] = node
    rank[node] = 0
}

//2. Union((Union Find 알고리즘))
func union(_ fromNode: String,_ toNode: String) {
    /*
     union-by-rank 기법
     */
    let root1 = find(fromNode) //각 루트 노드를 가져오기
    let root2 = find(toNode)
    
    if rank[root1] ?? 0 > rank[root2] ?? 0 {
        //높이가 작은 트리를 높이가 큰 루트 노드에 자식으로 붙임
        parent[root2] = root1
    } else if rank[root1] ?? 0 < rank[root2] ?? 0 {
        //높이가 작은 트리를 높이가 큰 루트 노드에 자식으로 붙임
        parent[root1] = root2
    } else {
        //랭크 값이 같다면 둘 중 하나 골라서 랭크값을 높이고 자식 노드로 붙임
        //여기서는 root2의 랭크값을 높였으므로 root1을 root2로 붙였음
        if rank[root1] ?? 0 == rank[root2] ?? 0 {
            let num = rank[root2] ?? 0
            rank[root2] = num + 1
        }
        
        parent[root1] = root2
    }
}

//3. Find(Union Find 알고리즘)
func find(_ node: String) -> String {
    /*
     path compression 기법
     
     이게 좀 헷갈릴 수 있다. 근데 잘 들어봐.
     밑에 debug 함수 만들어놨으니 그거 보면 더 쉽다.
     일단 초기화를 해. 그러면 parent는 이렇게 되겠지 ? A:A, B:B, C:C, D:D, E:E, F:F
     그런 상태에서 데이터가 가중치별로 정렬이 되어있어.
     
     (weight: 5, fromNode: "A", toNode: "D"),
     (weight: 5, fromNode: "C", toNode: "E"),
     (weight: 5, fromNode: "D", toNode: "A"),
     (weight: 5, fromNode: "E", toNode: "C"),
     (weight: 6, fromNode: "D", toNode: "F"),
     (weight: 6, fromNode: "F", toNode: "D"),
     (weight: 7, fromNode: "A", toNode: "B"),
     (weight: 7, fromNode: "B", toNode: "A"),
     (weight: 7, fromNode: "B", toNode: "E")...
     
     ---0 count (weight: 5, fromNode: "A", toNode: "D")---
     
     그리고 아래 코드로 싸이클 여부를 판단해.
     
     if find(fromNode) != find(toNode) { ... }
     
     parent 에 지금 아무 것도 안해줬으니 parent["A"] = "A"이고, "D"도 "D"겠지.
     그러면 아래 코드에 따라서, parent[node] = find(parent[node] ?? "") 를 한번 더 타고
     parent에 해준게 없으니 리턴은 parent[node] ?? "" 가 되는거야. 뭐 한게 없지?
     
     근데 그러고 다음 코드로 가보자.
     
     저 메인 코드의 union(fromNode, toNode) 이야.
     
     결론적으로 말하면 둘 다 rank가 같으니 toNode의 rank가 +1이 되고,
     (A:chile) - (D:root)로 연결이 될거야.
     
     그리고 parent는 A:D, B:B, C:C, D:D, E:E, F:F로 변하는거지.
     
     ---1 count (weight: 5, fromNode: "C", toNode: "E")---

     그리고 다음 C,E를 넣으면 마찬가지로 (C:chile) - (E:root)
     
     여기까지 하면 아래와 같이 되겠지.
     parent A:D, B:B, C:E, D:D, E:E, F:F
     rank   A:0, B:0, C:0, D:1, E:1, F:0
     
     ---2 count (weight: 5, fromNode: "D", toNode: "A")---
     ---3 count (weight: 5, fromNode: "E", toNode: "C")---

     그리고 D, A를 넣었다.
     find(D), find(A) 둘 다 루트가 D네? 그럼 얘네는 같은 부분집합(싸이클)에 속해있다고 보면 돼.
     그러면 find(fromNode) != find(toNode) 이 아니므로 그냥 다음 for문으로 넘어가.
     
     E,C 도 마찬가지.
     
     ---4 count (weight: 6, fromNode: "D", toNode: "F")---

     D의 루트노드는 D, F는 F로 달라. 그러면 싸이클이 아니겠고
     rank 값은 D가 1, F가 0이네 그러면 F가 D 밑으로 들어가야돼. 그러면 현재까지는 이렇게 되는거지
     
        D   E
      / |   |
     A  F   C

     parent A:D, B:B, C:E, D:D, E:E, F:D
     rank   A:0, B:0, C:0, D:1, E:1, F:0
     
     ---5 count (weight: 6, fromNode: "F", toNode: "D")---

     이러면, F의 루트는 D, D의 루트도 D니까 싸이클이네. 넘어가.
     
     ---6 count (weight: 7, fromNode: "A", toNode: "B")---

     A의 루트는 D, B의 루트는 B네. 싸이클이 아니야. union 해야해.
     중요한건 여기서 rank를 따져야되는데 if rank[root1] ?? 0 > rank[root2] ?? 0 {...} 로직이므로
     루트
     A는 루트인 D의 랭크를 봐야해서 rank는 1, B는 0이야. 그러면 작은애가 큰애 밑으로 들어가야하니. 트리가 이렇게 돼.
     
        D     E
      / | \   |
     A  F  B  C
     
     그리고 B의 루트를 D로 바꿔줘. path compression 기법.
     
     ---7 count (weight: 7, fromNode: "B", toNode: "A")---

     같은 방식으로 B의 루트는 D, A의 루트도 D 무시
     
     ---8 count (weight: 7, fromNode: "B", toNode: "E")---
     
     같은 방식으로 B의 루트는 D, E의 루트는 E야 싸이클이 없네. union 해야해.
     rank를 보는데 B는 D기준이니 1, E는 E인데 E도 랭크가 1이네?
     그러면 둘 중 아무거나 하나 골라서 랭크를 +1 해주고 자식 노드로 들어가야되는데
     우리는 코드에서 toNode가 올라가게 작성했으니까 E의 rank를 +1하고 D 의 트리를 E로 붙여줘 아래처럼.

            E
           /|
          / C
         /
        D
      / | \
     A  F  B
     
     ... 이런식으로 쭉 하면 결국엔 아래와 같은 트리가 완성되고 그게 최소 신장 트리야.

            E
           /|\
          / C G
         /
        D
      / | \
     A  F  B
    */
    if parent[node] != node {
        parent[node] = find(parent[node] ?? "")
    }
    return parent[node] ?? ""
}

/*
 이걸 swift 내장함수 sorted 대신에 써봤는데 오히려 sorted가 시간이 덜걸리더라.
 내부가 quickSort로 되어있던가? 아니면 더 좋은걸로 되어있나보다.
 */
//func quickSortWithEdge(_ edges: [Edge]) -> [Edge] {
//    if edges.count <= 1 { return edges }
//
//    let pivot: Edge = (edges.first?.weight ?? 0,
//                       edges.first?.fromNode ?? "",
//                       edges.first?.toNode ?? "")
//    let left = edges[1...].filter { pivot.weight > $0.weight }
//    let right = edges[1...].filter { pivot.weight <= $0.weight }
//
//    return quickSortWithEdge(left) + [pivot] + quickSortWithEdge(right)
//}

func kruskal(_ graph: [String: Any]) -> [Edge] {
    var mst: [Edge] = []
    
    //1. 초기화
    guard let nodes = graph["nodes"] as? [String] else { return []}
    for node in nodes {
        makeSet(node)
    }
    
    //2. 간선 weight 기반 sorting ( 이 부분의 sortting은 quicksort를 쓰는게 더 좋지 )
    guard let edges = graph["edges"] as? [Edge] else { return []}
    let sortedEdges = edges.sorted { $0.weight < $1.weight }
//    let sortedEdges = quickSortWithEdge(edges)

    //3. 싸이클이 없는 간선만 연결
    for (index, edge) in sortedEdges.enumerated() {
        let fromNode = edge.fromNode
        let toNode = edge.toNode

        if find(fromNode) != find(toNode) { //싸이클이 없는 걸 이 코드만으로 판별
            union(fromNode, toNode)
            mst.append(edge)
        }
        
        //debug(index, edge) //debug 용도 함수
    }
    return mst
}

print("\n===kruskal===")
//processTime {
    kruskal(graph).sorted { $0.weight < $1.weight }.forEach { print($0) }
//}


func debug(_ index: Int, _ edge: Edge) {
    print("\n---\(index) count \(edge)---")
    print(parent.sorted { $0.key < $1.key }.forEach { print($0)})
    print(rank.sorted { $0.key < $1.key }.forEach { print($0)})
}

```
  </p>
</details> 
        

- 최소 신장 트리를 구하기 위한 알고리즘
- 정렬 후에 가중치가 낮은 간선부터 연결한다. (Greedy Algorithm 을 기반으로 하고 있다.) 
- 가중치(weight)를 기준으로 작은 것부터 노드를 연결하되, 싸이클이 생기면 그 노드는 연결하지 않는다. 모든 노드가 다 연결될 때까지 이 작업을 반복한다. 
- 싸이클이 생기지의 여부를 판단하기 위해서 Union Find 알고리즘을 사용한다. 
			    
![](https://www.fun-coding.org/00_Images/kruscal_internal1.png)	![](https://www.fun-coding.org/00_Images/kruscal_internal2.png)		
			    
- 시간복잡도 
    - O(E log E)
    	1. 모든 정점을 독립적인 집합으로 만든다. -> O(V)
    	2. 모든 간선을 비용을 기준으로 정렬하고, 비용이 작은 간선부터 양 끝의 두 정점을 비교한다. -> O(E log E)
     		- 퀵소트를 사용한다면 시간 복잡도는 O(n log n) 이며, 간선이 n 이므로 O(E log E)
    	3. 두 정점의 최상위 정점을 확인하고, 서로 다를 경우 두 정점을 연결한다. (최소 신장 트리는 사이클이 없으므로, 사이클이 생기지 않도록 하는 것임)
    		- union-by-rank 와 path compression 기법 사용시 시간 복잡도가 결국 상수값에 가까움, O(1)
    - 따라서 O(V)보다 O(E)가 많으니 O(V)는 무시하고, O(E)보다는 O(E log E)가 크니까 결국엔 시간복잡도는 퀵소트를 사용하는 시간복잡도인 O(E log E)이다. 		
				
![](https://www.fun-coding.org/00_Images/kruscal_time.png)			
						    
    
## Union Find Algorithm(합집합 찾기 알고리즘) 
- 트리 자료구조를 사용한다. 
- 각자의 노드의 루트 노드를 비교해서 루트 노드가 같다면, 서로 같은 부분 집합 안에 들어가있다라는 것으로 싸이클 여부를 판단한다.
- 트리 구조이고, 항상 루트노드를 찾아 비교해야 하기 때문에 최적화 기법(union-by-rank, path compression)을 사용한다. 
- 여기서는 크루스칼 알고리즘에서 싸이클이 생기는지의 여부를 판단하기 위해 쓰이나, 크루스칼 알고리즘에서만 사용되는 것은 아니다. 
- 정확히는 Disjoint Set(서로소 집합)을 만들 때 사용하는 알고리즘이다. 
- [Disjoint Set(서로소 집합)](https://ko.wikipedia.org/wiki/%EC%84%9C%EB%A1%9C%EC%86%8C_%EC%A7%91%ED%95%A9)이란? 
    - 공통 원소가 없는 두 집합이다.
    - 예를 들어서 {1, 2, 3}과 {4, 5, 6}은 서로소이며 {1, 2, 3}과 {3, 4, 5}는 아니다.
				
- Union Find 알고리즘 프로세스
    - 초기화 
	  - ![](https://www.fun-coding.org/00_Images/initial_findunion.png)				
	  - n개의 원소를 개별집합으로 이뤄지도록 초기화 하는 작업 				
    - Union 
	  - ![](https://www.fun-coding.org/00_Images/union_findunion.png)					
	  - 두 개별 집합을 하나의 집합으로 합치는 작업, 두 트리를 하나의 트리로 합침			
    - Find 
	  - ![](https://www.fun-coding.org/00_Images/find_findunion.png)					
	  - 여러 노드가 있을 때, 두 개의 노드를 선택해서 그 두개의 노드의 루트 노드를 찾는다. 		
	  - 위 그림에서 A, B를 예로 들면 A, B는 D라는 같은 루트 노드를 갖는다. 그러면 얘네들은 서로 같은 부분 집합 안에 들어가있다 라고 판단한다. 		
	  - 이 말은, D와 E를 루트 노드로 갖는 2개의 부분 집합을 합칠 때도 동일하게 적용된다. 루트 노드가 같지 않으면 싸이클이 없는 것으로 판단한다는 뜻이다. 							
					    
- Union Find 알고리즘 최적화 
    - 기본적으로 트리구조를 사용하기 때문에 성능을 고려할 필요가 있다.
    - 각자의 노드의 루트 노드를 비교해서 루트 노드가 같다면, 서로 같은 부분 집합 안에 들어가있다라는 것으로 싸이클 여부를 판단한다.
    - Union의 순서에 따라서 최악의 경우 아래의 그림과 같이 링크드 리스트 형태가 될 수 있다. 
    - 그러면 시간복잡도가 O(n)이 될 수 있으므로 최적화 기법으로 union-by-rank 기법, path compression 기법을 사용한다. 
			
![](https://www.fun-coding.org/00_Images/worst_findunion.png)		
								
- Union By Rank 기법
    - Union Find 알고리즘 중에 Union 작업할 때 사용된다.
    - Union/Find 연산시의 시간복잡도를 O(n)에서 O(logN)으로 낮추기 위해서 사용한다. 
    - 밑에 있는 Path Compression 기법과 같이 사용하면 O(1)까지 낮출 수 있다. 
    - 2개의 부분 집합이 합쳐질 때, 어떻게 하면 높이를 작게하느냐에 초점을 맞춘 기법이다. (루트 노드를 찾는 시간을 줄이기 위해서) 
    - Union By Rank 기법 프로세스 
	  1. 각 트리에 대해 rank(높이)를 저장해둔다. 
	  2. 두 개의 트리를 합칠 때, 두 트리의 rank가 다르다면 ? 
	      - ![](https://www.fun-coding.org/00_Images/unionbyrank_findunion.png)			
	      - rank가 작은 트리를 rank가 높은 트리의 자식노드로 붙인다. 
	      - 이진트리가 아니기 때문에 어떻게 붙이든 상관이 없다.
	      - (A,B,F)-(D)를 (C)-(E)-(G)에 그냥 붙였으면 최악의 경우 (A,B,F)-(D)-(C)-(E)-(G)로 rank가 4인 트리가 되지만, 이 기법을 쓰면 rank가 2로 만들 수 있다. (rank는 0부터) 
	  3. 두 트리의 rank가 같다면 ? 
	      - ![](https://www.fun-coding.org/00_Images/unionbyranksame_findunion.png)			
	      - 둘 중에 아무거나 한 쪽의 rank를 1 증가 시켜주고, 반대 쪽의 트리를 자식노드로 붙여준다. 
    - 초기화시, 모든 원소는 높이(rank) 가 0 인 개별 집합인 상태에서, 하나씩 원소를 합칠 때, union-by-rank 기법을 사용한다면,
	  - 높이가 h 인 트리가 만들어지려면, 높이가 h - 1 인 두 개의 트리가 합쳐져야 함
	  - 높이가 h - 1 인 트리를 만들기 위해 최소 n개의 원소가 필요하다면, 높이가 h 인 트리가 만들어지기 위해서는 최소 2n개의 원소가 필요함
	  - 따라서 union-by-rank 기법을 사용하면, union/find 연산의 시간복잡도는 O(N) 이 아닌, $ O(log{N}) $ 로 낮출 수 있음
				    
- Path Compression(경로 압축) 기법
    - ![](https://www.fun-coding.org/00_Images/pathcompression_findunion.png)			
    - Union Find 알고리즘 중에 Find 작업할 때 사용된다.
    - Find 작업을 한번 했다면, 그렇게 거쳐간 노드들을 루트 노드에 다이렉트로 연결되게 변경시키는 기법이다. 
    - 이진 트리가 아니기 때문에 자식 노드의 갯수는 상관이 없고, 이렇게 변경하면 이후부터는 Find시 1회만에 루트 노드를 찾을 수 있다. 
				    
- Union By Rank 와 Path Compression 기법 사용시의 시간복잡도 
    - O(1)이다. 수학적으로 이미 증명되었다. 
    - $O(M log^*{N})$
    	- $log^*{N}$ 은 다음 값을 가짐이 증명되었음
    	- N이 $2^{65536}$ 값을 가지더라도, $log^*{N}$ 의 값이 5의 값을 가지므로, 거의 O(1), 즉 상수값에 가깝다고 볼 수 있음
    - <div style="text-align:left">
      <table>
        <tr>
          <th style="text-align:center">N</th>
          <th style="text-align:center">$log^*{N}$</th>
        </tr>
        <tr>
          <td style="text-align:left">1</td>
          <td style="text-align:left">0</td>
        </tr>
        <tr>
          <td style="text-align:left">2</td>
          <td style="text-align:left">1</td>
        </tr>
        <tr>
          <td style="text-align:left">4</td>
          <td style="text-align:left">2</td>
        </tr>
        <tr>
          <td style="text-align:left">16</td>
          <td style="text-align:left">3</td>
        </tr>
        <tr>
          <td style="text-align:left">65536</td>
          <td style="text-align:left">4</td>
        </tr>
        <tr>
          <td style="text-align:left">$2^{65536}$</td>
          <td style="text-align:left">5</td>
        </tr>
      </table>
      </div>   
 
  
    
  
  
## Prim's Algorithm(프림 알고리즘)			
    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
  <p>

```swift

import Foundation

/*
 그래프는 아래의 링크 그래프를 예시로 작성한다.
 https://velog.velcdn.com/images/dev_kickbell/post/745e799d-2fcd-4c01-a617-e4e788fd369e/image.png
 */

let edges = [
    (7, "A", "B"), (5, "A", "D"),
    (8, "B", "C"), (9, "B", "D"), (7, "B", "E"),
    (5, "C", "E"),
    (7, "D", "E"), (6, "D", "F"),
    (8, "E", "F"), (9, "E", "G"),
    (11, "F", "G")
]

typealias Edge = (weight: Int, fromNode: String, toNode: String)

func prim(_ startNode: String, _ edges: [Edge]) -> [Edge] {
    var mst: [Edge] = []
    var adjacentEdges: [String: [Edge]] = [:]
    
    //1. 초기화
    //이유는 모르겠지만, adjacentEdges[edge.toNode]?.append(edge)가 안되서 두개로 나누었다.
    //밑에 for문도 튜플이 아니라 edge로 바꾸면 오동작하는데 이유를 모르겠네 진짜로..
    for (_, n1, n2) in edges {
        adjacentEdges[n1] = []
        adjacentEdges[n2] = []
    }
    for (weight, n1, n2) in edges {
        adjacentEdges[n1]?.append((weight, n1, n2))
        adjacentEdges[n2]?.append((weight, n2, n1))
    }
    
    //2. 연결된 노드 리스트 생성
    var connectedNodes: Set = [startNode]
    
    //3. 간선 리스트 생성
    var candidateEdges: [Edge] = adjacentEdges[startNode] ?? []
    
    
    while !candidateEdges.isEmpty {
        //강의에서는 candidateEdges가 힙구조가 되어서 바로 제일 작은걸 꺼내왔지만,
        //swift에서는 그런 라이브러리를 아직 못찾아서 일단 sorted를 사용.
        //빈배열에서 removeFirst()를 하면 크래쉬가 발생하지만, while구문에서 통과되지 않을 것이므로 고고함.
        candidateEdges.sort(by: { $0.weight < $1.weight })
        let poppedEdge = candidateEdges.removeFirst()
        
        if !connectedNodes.contains(poppedEdge.toNode) { //싸이클이 없는 경우
            connectedNodes.insert(poppedEdge.toNode) //연결된 노드 리스트에 추가
            mst.append(poppedEdge) //결과값인 최소 신장 트리에도 추가
            
            //A->D라고 치면 D를 연결된 노드 리스트에 넣어줬고, 이제 D에 연결된 간선들을
            //간선 리스트에 넣어줘야 한다. 여기가 그 작업이다.
            for newEdge in adjacentEdges[poppedEdge.toNode] ?? [] {
                /*
                 여기의 if문은 무슨 뜻이냐면,
                 음, 예를들어
                   A
                  / \
                 B   E-F 가 있다고 치자. 근데 F가 이제 싸이클이 없어서 연결된 노드 리스트에 넣고
                 
                 간선 리스트에 F와 연결된 간선들을 넣으려고 하는 상황인거야. 근데 F를 보니 아래와 같이 생긴거지.
                   F
                  /|\
                 B H G  이래버리면 이중에 B는 어차피 넣을 필요가 없어. 왜? 연결된 노드 리스트에 이미 있으니까.
                 이 상황에서는 H, G만 넣으면 되는거지. B를 굳이 넣어서 괜히 연산을 더 할필요가 없는 것.
                 */
                if !connectedNodes.contains(newEdge.toNode) {
                    candidateEdges.append(newEdge)
                }
            }
        }
    }
    return mst
}

print("\n===prim=== ")
prim("A", edges).forEach { print($0) }

/*
 강사님 결과값이랑 약간 다른데, (7, 'B', 'E') 이부분이 다름.
 근데 그래프 그려보면 어차피 7로 같아서 로직은 맞는 것 같다.
 그냥 최소 신장 트리인데 여러 개중에 하나로 그려진 듯..?
 
 [(5, 'A', 'D'),
  (6, 'D', 'F'),
  (7, 'A', 'B'),
  (7, 'B', 'E'),
  (5, 'E', 'C'),
  (9, 'E', 'G')]
 */


/*
 개선된 프림 알고리즘
 - 간선이 아니라 노드를 기준으로 하기 때문에 시간복잡도가 𝑂(𝐸𝑙𝑜𝑔𝑉) 로 줄어든다.
 
 from heapdict import heapdict

 def prim(graph, start):
     mst, keys, pi, total_weight = list(), heapdict(), dict(), 0
     for node in graph.keys():
         keys[node] = float('inf')
         pi[node] = None
     keys[start], pi[start] = 0, start

     while keys:
         current_node, current_key = keys.popitem()
         mst.append([pi[current_node], current_node, current_key])
         total_weight += current_key
         for adjacent, weight in mygraph[current_node].items():
             if adjacent in keys and weight < keys[adjacent]:
                 keys[adjacent] = weight
                 pi[adjacent] = current_node
     return mst, total_weight
 
 
 
 mygraph = {
     'A': {'B': 7, 'D': 5},
     'B': {'A': 7, 'D': 9, 'C': 8, 'E': 7},
     'C': {'B': 8, 'E': 5},
     'D': {'A': 5, 'B': 9, 'E': 7, 'F': 6},
     'E': {'B': 7, 'C': 5, 'D': 7, 'F': 8, 'G': 9},
     'F': {'D': 6, 'E': 8, 'G': 11},
     'G': {'E': 9, 'F': 11}
 }
 mst, total_weight = prim(mygraph, 'A')
 print ('MST:', mst)
 print ('Total Weight:', total_weight)
 */

```
  </p>
</details>
    
    


- 최소 신장 트리를 구하기 위한 알고리즘
- 크루스칼 알고리즘과 다른 점은 똑같이 Greedy Algorithm 을 기반으로 하는데, 주체가 다르다. 
- 크루스칼 알고리즘이 단순 가중치를 기준으로 정렬을 해서 낮은 가중치부터 연결하고 그 다음 낮은 가중치를 연결 하는 식으로 동작했다고 하면, 프림 알고리즘은 시작할 첫번째 노드를 선택하고 선택한 노드를 기준으로 연결된 간선 중에 낮은 가중치가 있는 것 부터 연결, 그리고 새롭게 연결된 노드가 가지고 있는 간선들 중에 가중치가 낮은 것을 연결하는 식으로 동작한다. 

- 프로세스 
    1. 배열 2개를 만든다. '연결된 노드 리스트', '간선 리스트' 
    2. 알고리즘을 시작한다. 여기서는 A로 시작했다. 처음이라 아무것도 없으니 '연결된 노드 리스트'에 A를 삽입한다.
    3. '간선 리스트'에 A에 연결된 간선인 (5, A, D)와 (7, A, B)를 추가한다. 
    4. '간선 리스트'에서 데이터를 하나 추출(간선 리스트에서 사라짐)에서 하는데, 최소힙처럼 동작해서 가중치가 낮은 (5, A, D)가 추출된다. 
    5. 그러면 목표는 D인데, D가 '연결된 노드 리스트'에 들어있으면? 싸이클로 판단하고 스킵한다. 없다면 '연결된 노드 리스트' 삽입한다. 그리고 '간선 리스트'에 D와 연결되어있는 간선들을 삽입한다. (6, D, F), (7, D, E), (8, D, B) 
    6. 다시 '간선 리스트'에서 데이터를 하나 추출한다. 가중치가 가장 낮은게 나오니까 (6, D, F)이 추출된다. 
    7. 5 번 작업을 진행한다. F는 없으니 추가한다. 간선 리스트에도 F와 연결된 간선들을 추가해준다. 
    8. 6 번 작업을 진행한다. 가중치 7이 두 개지만, 그림 상에서는 (7, A, B)가 선택되었다. 
    9. B 추가, 간선리스트 추가 
    10. 또 7이 두 개지만, 그림 상에서는 (7, B, E)가 선택되었다. 
    11. E 추가, 간선리스트 추가 
    12. 가중치 5인 (5, E, B)가 선택되었다. 
    13. B 추가, 간선리스트 추가
    14. 가중치 7인 (7, D, E)가 나오지만 E는 이미 '연결된 노드 리스트'에 있다. 스킵된다. 
    15. 가중치 8인 (8, F, E)가 나오지만 E는 이미 '연결된 노드 리스트'에 있다. 스킵된다. 
    16. 가중치 8인 (8, B, C)가 나오지만 C는 이미 '연결된 노드 리스트'에 있다. 스킵된다.
    17. 이런 식으로 반복된다. 언제까지? 간선리스트가 빌 때까지. 
    18. 간선리스트가 비었다. 최소 신장 트리가 완성되었고 알고리즘이 종료된다. 		
			
![](https://www.fun-coding.org/00_Images/prim1.png) ![](https://www.fun-coding.org/00_Images/prim2.png)![](https://www.fun-coding.org/00_Images/prim3.png)			
			
- 시간복잡도 
	- 최악의 경우, while 구문에서 모든 간선에 대해 반복하고, 최소 힙 구조를 사용하므로 O($ElogE$) 시간 복잡도를 가짐

- 개선된 프림 알고리즘 
    - 코드는 위의 링크 참고(파이썬)
    - 간선이 아닌 노드를 중심으로 우선순위 큐를 적용하는 방식
    - 초기화 - 정점:key 구조를 만들어놓고, 특정 정점의 key값은 0, 이외의 정점들의 key값은 무한대로 놓음.  모든 정점:key 값은 우선순위 큐에 넣음
    - 가장 key값이 적은 정점:key를 추출한 후(pop 하므로 해당 정점:key 정보는 우선순위 큐에서 삭제됨), (extract min 로직이라고 부름)
    - 해당 정점의 인접한 정점들에 대해 key 값과 연결된 가중치 값을 비교하여 key값이 작으면 해당 정점:key 값을 갱신
    - 정점:key 값 갱신시, 우선순위 큐는 최소 key값을 가지는 정점:key 를 루트노드로 올려놓도록 재구성함 (decrease key 로직이라고 부름)
    - 개선된 프림 알고리즘 구현시 고려 사항
    	- 우선순위 큐(최소힙) 구조에서, 이미 들어가 있는 데이터의 값 변경시, 최소값을 가지는 데이터를 루트노드로 올려놓도록 재구성하는 기능이 필요함
    	- 구현 복잡도를 줄이기 위해, heapdict 라이브러리를 통해, 해당 기능을 간단히 구현
    
- 개선된 프림 알고리즘의 시간 복잡도: $O(ElogV)$
    - 최초 key 생성 시간 복잡도: $O(V)$ 
    - while 구문과 keys.popitem() 의 시간 복잡도는 $O(VlogV)$
    - while 구문은 V(노드 갯수) 번 실행됨
    - heap 에서 최소 key 값을 가지는 노드 정보 추출 시(pop)의 시간 복잡도: $O(logV)$ 
    - for 구문의 총 시간 복잡도는 $O(ElogV)$
    - for 구문은 while 구문 반복시에 결과적으로 총 최대 간선의 수 E만큼 실행 가능 $O(E)$
    - for 구문 안에서 key값 변경시마다 heap 구조를 변경해야 하며, heap 에는 최대 V 개의 정보가 있으므로 $O(logV)$
    - 일반적인 heap 자료 구조 자체에는 본래 heap 내부의 데이터 우선순위 변경시, 최소 우선순위 데이터를 루트노드로 만들어주는 로직은 없음. 이를 decrease key 로직이라고 부름, 해당 로직은 heapdict 라이브러리를 활용해서 간단히 적용가능    
    - 따라서 총 시간 복잡도는 $O(V + VlogV + ElogV)$ 이며,
    	- O(V)는 전체 시간 복잡도에 큰 영향을 미치지 않으므로 삭제,
    	- E > V 이므로 (최대 $V^2 = E$ 가 될 수 있음), $O((V + E)logV)$ 는 간단하게 $O(ElogV)$ 로 나타낼 수 있음
    
    
## Kruskal's(크루스칼) vs Prim's(프림)
- 사용 용도가 다를 수 있다. 
- 크루스칼 알고리즘은 일단 전체 간선의 가중치를 다 알고 있다는 가정 하에 그걸 정렬해서 연결하는 방식 
- 프림 알고리즘은 일부 가중치는 모를 때? 또는 한정된 정보 내에서 하나씩 선택해가면서 추가되는 정보에 따라서 연결하는 방식  
- 프림 알고리즘은 개선된 버전이 있다. 크루스칼 알고리즘 시간복잡도(O(E log E)), 일반 프림 알고리즘 시간복잡도(O(E log E))에 비해 개선된 버전은 (𝑂(𝐸𝑙𝑜𝑔𝑉))로 더 효율적이다. (E > V 이므로 (최대  𝑉2=𝐸  가 될 수 있음))
    
    
## Backtracking(백트래킹)
    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm"></a></summary>
  <p>

```swift

import Foundation

func isAvailable(_ currentCandidate: [Int],_ column: Int) -> Bool {
    //현재 퀸이 2번째까지 되어있다면 currentCandidate.count 는 2이다.
    //그런데 배열은 0부터 시작하므로 currentRow는 0,1,2해서 3번째 이다.
    //그리고 매개변수로 현재 열(column)이 넘어온다. 그러면 이것을 바탕으로 계산을 할 수 있다.
    let currentRow = currentCandidate.count
    
    for queenRow in 0..<currentRow {
        if currentCandidate[queenRow] == column ||
            abs(currentCandidate[queenRow] - column) == (currentRow - queenRow) {
            return false
        }
    }
    return true
}


func dfs(_ n: Int, _ currentRow: Int, _ currentCandidate: [Int], _ finalResult: [Int] ) {
    var currentCandidate = currentCandidate //현재까지 퀸이 들어간 결과
    var finalResult = finalResult //최종 결과
    
    if currentRow == n { //재귀적으로 사용되므로 종료조건
        finalResult.append(contentsOf: currentCandidate)
        print("n: \(n) -> answer:", finalResult)
        return
    }

    //가로줄을 행(行, row), 세로줄을 열(列, column)
    for column in 0..<n {
        //isAvailable -> Promising
        //현재까지 퀸이 들어간 결과와 현재 열을 넣으면 그걸 기반으로 조건에 부합하는지 체크
        if isAvailable(currentCandidate, column) {
            //여기까지 왔다는 건 조건을 통과했다는 거니까 퀸 후보자 배열에 컬럼을 추가해준다.
            currentCandidate.append(column)
            //그리고 이제 다음행을 봐야겠지? 그래서 재귀적으로 +1 해서 다시 호출한다.
            dfs(n, currentRow + 1, currentCandidate, finalResult)
            /*
             Prunning(가지치기), 이거 재귀라 되게 헷갈린다. 자 봐봐.
             
             자 생각해봐. 저 dfs 함수에서 if currentRow == n {...} 종료조건에도 해당이 되지 않고,
             isAvailable()에도 true가 아니면 어떻게 될까 ?
             
             그럼 그냥 리턴한다. 즉, 항상 저 두 조건이 true 일리가 없지 않나? 그러면 리턴이 되서 아래로 내려간다.
             그러면 여기서 가지치기를 하는 건데, 재귀함수 잖아? 그러면 한뎁스씩 내려갔다가 다시 스택처럼 올라온다.
             
             f(5)->f(4)->f(3)->f(2)->f(1)->f(종료조건:0)
             f(종료조건:0)->f(1)->f(2)->f(3)->f(4)->f(5) 처럼 말이다.
             
             그러면, 저기서는 리턴조건은 최종 조건이 아니라 isAvailable() 일 것이고, 현재의 column이
             currentCandidate 에 적합하지 않으니 다시 돌아가서 다른 후보자를 탐색해야 한다.
             그래서 아래처럼 popLast()를 해주면 조건을 만족하지 않는 곳 까지 쭉쭉 리턴되고, 다음 for문을 돌겠지?
             그러면 다음 녀석이 isAvailable()를 만족하면 이제 다음 currentCandidate 를 탐색하는 식이다.
             
             좀 헷갈릴 수 있다. 아니 지금도 매우 헷갈리다. 이래서 backtracking 이다.
             */
            _ = currentCandidate.popLast()
        }
    }
    
}


func nQueens(_ n: Int) -> [Int] {
    let finalResult: [Int] = []
    dfs(n, 0, [], finalResult)
    return finalResult
}


print("\n===nQueens===")
_ = nQueens(4)
```
  </p>
</details>

- 알고리즘은 아니고 일종의 문제를 푸는 전략이다. 
- 제약 조건 만족 문제에서 사용한다. 
- 모든 경우의 수(후보군)을 상태 공간 트리(State Space Tree)로 표현한다. 
    - 상태 공간 트리란, 문제 해결 과정의 중간 상태를 각각의 노드로 나타낸 트리인데 몰라도 된다. 전혀. 아래 그림이 상태 공간 트리. 
- 컨셉이 트리 느낌으로 한다는 것이지 실제 코드 구현에서 트리를 만들거나 하지는 않는다. 
- 각 후보군을 DFS 방식으로 확인한다. 
- 조건이 맞는지를 검사하는 것을 Promising, 조건이 맞지 않으면 해당 트리를 끊어버리는 것을 Pruning(가지치기)라고 한다. 
- 제약 조건이 전부 다 맞아야만 하는 문제에서 Pruning 함으로써 그 아래의 트리는 탐색해도 되지 않아 시간을 절약할 수 있다. 
- 프로세스 설명 ( 문제와 같이 이해하는 것이 더 쉽다. 아래로 내려가자. )
    1. 예를 들어, 조건이 이런식이다 a, b, e, h가 다 홀수인지 확인해라. 
    2. 그러면 a를 검사하고 b를 검사하고 e를 검사하고 h를 검사하는 식이 제일 효율적이다. (DFS 깊이 우선 탐색)
    3. 그런데 a는 홀수인데, b는 짝수이다? 그러면 정답이 아니다. 그러면 a-b 사이의 간선을 끊어버린다. 그럼 b 이하 트리는 검색할 필요도 없어진다. 
    4. 이게 전부다. 검사하는걸 Promising, 가지치기 하는 것을 Pruning 이라고 부른단다. 
    5. 이것을 Backtracking(백트래킹 또는 퇴각검색)전략이라고 부르는 이유이다. 
			    
![](https://www.fun-coding.org/00_Images/statespacetree.png)			
				
- N Queen 문제 이해 
    - ![](https://www.fun-coding.org/00_Images/queen_move.png)			
    - 대표적인 백트래킹 문제이다. 		
    - 뭐냐면, 체스에서 퀸은 아래 그림과 같이 굉장히 유능하게 움직일 수 있다. 그리고 문제는 "NxN 크기의 체스판에서 N개의 퀸을 배치한다고 했을 때, 서로 공격할 수 없도록 배치할 수 있는 경우의 수를 구해라." 이다. 
    - 화살표 오른쪽 사진이 하나의 예시이다.  
    
- Prunning(가지치기) 
    - ![](https://www.fun-coding.org/00_Images/backtracking.png)			
    - 막연하나? 그럴 수 있다. 근데 이렇게 생각해보자. 저 문제를 배열로 표현하면 오른쪽 사진과 같다. 
    - 그리고 또 왼쪽 사진처럼 트리로 표현할 수도 있을 것 같다. 그럼 배운 것처럼 회색 동그라미는 조건을 만족하지 않으므로 무시하고 가지를 쳐버릴 수 있을 것이다. 이 부분은 그냥 저걸 배열로 표현할 수 있고, 그렇게 되면 가지치기를 할 수 있다 정도 까지만 이해하면 되겠다. 
    - 컨셉이 트리 느낌으로 한다는 것이지 실제 코드 구현에서 트리를 만들거나 하지는 않는다. 괜히 어렵게 표현하는 것이다. 
    - 그러면 제약 조건을 만족하는 방법이 뭘까? 내려가보자. 
    
- Promising(조건체크) 
    - ![](https://www.fun-coding.org/00_Images/nqueen.png)			
    - 퀸은 겹치지 않아야 하므로 한줄에는 1개만 있어야 하고, 수직/대각선 이동이 다 가능하다. 
    - 그렇다면, 위에 만든 배열로 조건을 생성할 수 있다. 
    - 수직체크 : current_column - queen_column != 0 이어야 한다.
    - 대각선체크 : abs(queen_column - current_column) != current_row - queen_row 이어야 한다. 
    	- 이 부분 헷갈릴 수 있어서 따로 적어보겠다. 위 사진을 예로 들어보자. 퀸 위치(0,1), 내 위치(1,2)라고 하자. 
    	- 그러면 튜플의 앞에 것의 차이(음수가 나올 수 있으므로 절대값 사용)인 abs(queen_column - current_column) 와 뒤에 것의 차이 current_row - queen_row 가 같으면 겹친다는 거다. 즉 달라야 된다는 거다. 
    	- 공식으로 굳이 따지자면, abs(0 - 1) != 2 - 1 이어야 하는데 1 == 1 로 같다. 그러면 조건을 만족하지 못하는 것이다. 

## Reference			
[https://velog.velcdn.com/images/dev_kickbell/post/f4c3753a-b41a-43f7-86ab-f9ee661ae089/image.png](https://velog.velcdn.com/images/dev_kickbell/post/f4c3753a-b41a-43f7-86ab-f9ee661ae089/image.png)	
			
[https://visualgo.net/en/sorting](https://visualgo.net/en/sorting)			

[https://fastcampus.co.kr/dev_online_devjob/?gclid=Cj0KCQjwqc6aBhC4ARIsAN06NmOdZUJGwc8x-w8ht0jS3idCSNhUZ49m--cQdDsVqIZWSnVmrBz5zxMaAtTBEALw_wcB](https://fastcampus.co.kr/dev_online_devjob/?gclid=Cj0KCQjwqc6aBhC4ARIsAN06NmOdZUJGwc8x-w8ht0jS3idCSNhUZ49m--cQdDsVqIZWSnVmrBz5zxMaAtTBEALw_wcB)

[https://www.fun-coding.org/python-question1-answer.html](https://www.fun-coding.org/python-question1-answer.html)				
			    
[https://github.com/kickbell/DataStructure_and_Algorithm](https://github.com/kickbell/DataStructure_and_Algorithm)	
