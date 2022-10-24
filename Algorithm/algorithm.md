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
## Insertion Sort(삽입 정렬)
## Selction Sort(선택 정렬) 
## Recursive Call(재귀 용법, 재귀 호출) 
## Dynamic Programming(동적 계획법) 
## Divide and Conquer(분할 정복)

## Merge Sort(병합 정렬)
## Quick Sort(퀵 정렬) 


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
