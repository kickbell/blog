## Array(배열)
어떤 데이터를 연결된 공간에 넣을 때 사용한다. 
- 장점 
	- index가 있기 때문에 내가 원하는 데이터로 직접적으로 빠른 접근이 가능하다.
- 단점 
	- index가 있기 때문에 중간 데이터를 삭제했을 때, 뒤에 있는 데이터들을 다 한칸씩 앞으로 이동해줘야 한다. index가 비어있으면 그건 배열이 아니기 때문이다. 
 	- 배열을 생성시에 미리 길이를 지정해야 하기 때문에 데이터 추가, 삭제가 어렵다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/a010b946-ce2d-4e26-a1d4-9653966b5581/image.png)


## Queue(큐)

가장 먼저 넣은 데이터를 가장 먼저 꺼낼 수 있는 자료구조(FIFO) 

<details>
  <summary>Code(Swift)</summary>
  <p>

```swift
import Foundation

struct Queue<T> {
    private var queue: [T] = []

    mutating func enqueue(_ data: T) {
        queue.append(data)
    }

    mutating func dequeue() -> T? {
        if let _ = queue.first {
            return queue.removeFirst()
        }
        return nil
    }
}


var queue = Queue<String>()

(1...10).map { String($0)}
    .forEach {
        queue.enqueue($0)
    }

print(queue)
print(queue.dequeue() ?? "")
print(queue.dequeue() ?? "")
print(queue.dequeue() ?? "")
```
  </p>
</details>

- 은행의 번호표, 버스 줄서서 타기 등을 예로 들 수 있다. 
- 데이터를 넣을때는 Enqueue, 꺼낼때는 Dequeue라고 한다. 
![](https://velog.velcdn.com/images/dev_kickbell/post/db888b71-6569-4038-9971-c57c99991c76/image.png)

- 기본 정책은 FIFO이지만, 다른 정책이 적용된 종류의 큐도 있다.  
	1. Queue(기본 큐) 
    	- 가장 일반적인 큐, FIFO 
	2. LifoQueue 
    	- 나중에 입력한 데이터가 가장 먼저 꺼내지는 구조(Stack처럼) 
        - LIFO(Last In Fast Out)
	3. PriorityQueue(우선순위 큐) 
    	- FIFO, LIFO 가 아니라 데이터를 넣을 때 우선순위를 같이 넣어서 그 순위를 기준으로 인큐/디큐하는 구조 
        - 튜플(priority: Int, data: Int) 형태로 사용되며 5등이 10등보다 좋은 것처럼 숫자가 낮은 것이 우선순위가 높은 것이다. 
- Use 
	- 멀티 태스킹을 위한 프로세스 스케쥴링 방식을 구현하기 위해 많이 사용된다.(운영체제) 아마도 운영체제에서 자체적으로 PriorityQueue 같은걸 통해 프로세스의 우선순위를 배정하고 최대한의 스케쥴링 효율을 높이기 위해 사용하는 것 같다.





## Stack(스택) 

## Linked List(링크드 리스트) 

## Hash Table(해쉬 테이블) 

## Tree(트리) 

## Heap(힙) 

## Reference
[https://fastcampus.co.kr/dev_online_devjob/?gclid=Cj0KCQjwqc6aBhC4ARIsAN06NmOdZUJGwc8x-w8ht0jS3idCSNhUZ49m--cQdDsVqIZWSnVmrBz5zxMaAtTBEALw_wcB](https://fastcampus.co.kr/dev_online_devjob/?gclid=Cj0KCQjwqc6aBhC4ARIsAN06NmOdZUJGwc8x-w8ht0jS3idCSNhUZ49m--cQdDsVqIZWSnVmrBz5zxMaAtTBEALw_wcB)

[https://www.fun-coding.org/python-question1-answer.html](https://www.fun-coding.org/python-question1-answer.html)



```swift

```
```swift

```
```swift

```
```swift

```
```swift

```
```swift

```
