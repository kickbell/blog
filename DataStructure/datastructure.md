## Array(배열)
- 어떤 데이터를 연결된 공간에 넣을 때 사용한다. 
- 장점 
	- index가 있기 때문에 내가 원하는 데이터로 직접적으로 빠른 접근이 가능하다.
- 단점 
	- index가 있기 때문에 중간 데이터를 삭제했을 때, 뒤에 있는 데이터들을 다 한칸씩 앞으로 이동해줘야 한다. index가 비어있으면 그건 배열이 아니기 때문이다. 
 	- 배열을 생성시에 미리 길이를 지정해야 하기 때문에 데이터 추가, 삭제가 어렵다. 

![](https://velog.velcdn.com/images/dev_kickbell/post/a010b946-ce2d-4e26-a1d4-9653966b5581/image.png)


## Queue(큐)

<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드</a></summary>
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

- 가장 먼저 넣은 데이터를 가장 먼저 꺼낼 수 있는 자료구조(FIFO)
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


## LifoQueue(리포큐)
- 나중에 입력한 데이터가 가장 먼저 꺼내지는 정책을 가지고 있는 큐 
- LIFO(Last In Fast Out)
		
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드</a></summary>
  <p>

```swift

import Foundation

struct LifoQueue<T> {
    private var queue: [T] = []

    mutating func enqueue(_ data: T) {
        queue.append(data)
    }

    mutating func dequeue() -> T? {
        if let _ = queue.last {
            return queue.removeLast()
        }
        return nil
    }
}


var queue = LifoQueue<String>()

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

    
## PriorityQueue(우선순위 큐) 				    
- FIFO, LIFO 가 아니라 데이터를 넣을 때 우선순위를 같이 넣어서 그 순위를 기준으로 인큐/디큐하는 구조 
- 튜플(priority: Int, data: Int) 형태로 사용되며 5등이 10등보다 좋은 것처럼 숫자가 낮은 것이 우선순위가 높은 것이다.
    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드</a></summary>
  <p>

```swift
import Foundation

struct PriorityQueue<T> {
    
    private var priorityQueue: [(priority: Int, data: T)] = []

    mutating func enqueue(_ tuple: (priority: Int, data: T)) {
        priorityQueue.append(tuple)
    }

    mutating func dequeue() -> (Int, T)? {
        guard !priorityQueue.isEmpty else { return nil }
        priorityQueue.sort { $0.priority < $1.priority }
        return priorityQueue.removeFirst()
    }
}

var priorityQueue = PriorityQueue<String>()

priorityQueue.enqueue((priority: 5, data: "hello"))
priorityQueue.enqueue((priority: 15, data: "bear"))
priorityQueue.enqueue((priority: 3, data: "zz"))

print(priorityQueue.dequeue() ?? (0, ""))
print(priorityQueue.dequeue() ?? (0, ""))
print(priorityQueue.dequeue() ?? (0, ""))
print(priorityQueue.dequeue() ?? (0, ""))

```
  </p>
</details>



## Stack(스택) 
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드</a></summary>
  <p>

```swift
import Foundation


struct Stack<T> {
    private var stackArray: [T] = []

    mutating func push(data: T) {
        stackArray.append(data)
    }

    mutating func pop() -> T? {
        let data = stackArray.last ?? nil
        stackArray.removeLast()
        return data
    }
}


var stack = Stack<Int>()

stack.push(data: 1)
stack.push(data: 2)
stack.push(data: 3)

print(stack.pop() ?? 0)
print(stack.pop() ?? 0)
print(stack.pop() ?? 0)

```
  </p>
</details>

- 데이터를 제한적으로 접근할 수 있는 구조
- 한쪽 끝에서만 자료를 넣거나 뺄 수 있는 구조
- 넣을때는 push, 뺄때는 pop
- 큐: FIFO <-> 스택: LIFO 
- 주로 사용 용도
    - 스택 구조는 우리가 사용하는 운영체제의 프로세스 실행 구조의 가장 기본이다.
    - 함수 호출시 프로세스 실행 구조를 스택과 비교해서 이해 필요
			
![](https://velog.velcdn.com/images/dev_kickbell/post/c95de4c6-dac0-48a0-9d6c-52b3320dcdcc/image.png)				
					
- 장점
    - 구조가 단순해서, 구현이 쉽다.
    - 데이터 저장/읽기 속도가 빠르다.
- 단점 (일반적인 스택 구현시)
    - 데이터 최대 갯수를 미리 정해야 한다.
    - 파이썬의 경우 재귀 함수는 1000번까지만 호출이 가능함
    - 저장 공간의 낭비가 발생할 수 있음
    - 미리 최대 갯수만큼 저장 공간을 확보해야 함(스택은 단순하고 빠른 성능을 위해 사용되므로, 보통 배열 구조를 활용해서 구현하는 것이 일반적임. 이 경우, 위에서 열거한 단점이 있을 수 있음)


## Linked List(링크드 리스트, 연결 리스트) 

<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드 1</a></summary>
  <p>

```swift
import Foundation

//왜 구조체로 안하고 클래스로 만들까?
//포인터 때문이 확실.. 구조체는 복사되니까.
class Node {
    var data: Double
    var next: Node?
    
    init(data: Double, next: Node? = nil) {
        self.data = data
        self.next = next
    }
}

var node1 = Node(data: 1)
var node2 = Node(data: 2)
node1.next = node2
var head = node1


//입력
func add(data: Double) {
    var node: Node? = head
    
    while node!.next != nil {
        node = node?.next
    }

    node?.next = Node(data: data)
}

for index in 3...10 {
    add(data: Double(index))
}


//출력
var node: Node? = head

while node!.next != nil {
    print(node?.data ?? 0)
    node = node!.next
}
print(node?.data ?? 0)




//중간에 데이터 추가하기
//1. 추가할 데이터 생성, 헤드값 초기화. 맨처음부터 다시 돌아야 되니까
var node3 = Node(data: 3.5)
node = head

//2. 어디에 넣을지 찾는 변수 추가
var search = true

//3. 어디에 넣을지 찾기.
// 그 이전까지는 주소값을 넣어주다가 3을 찾으면 종료시켜버림.
// 중요한 것은 1 + 주소값, 2 + 주소값, 3 -> 종료이기 때문에
// 4,5,6,7,8.. 같은애들애는 주소값이 없어. 즉, 미리 데이터가 다 들어가있는 상황에서
// 중간에 데이터를 삽입할때 쓰는 방법이라는 말임.
while search {
    if node?.data == 3.0 {
        search = false
    } else {
        node = node?.next
    }
}

//4. 새로 넣을 데이터에 주소값 연결하기
// 자, 보면
// 지금 node.next는 원래 3->4 를 가리키던 주소값임.
// 그래서 일단 그걸 다른 변수에 담아놓는다.
let lastNext = node?.next
node?.next = node3 // 그리고 가리키는 값을 node3이 가리키도록 바꿔.
node3.next = lastNext // 그리고 마지막으로 node3이 4(lastNext)를 가리키도록 바꿔준다.

print("==================================================================")


//출력
node = head

while node!.next != nil {
    print(node?.data ?? 0)
    node = node!.next
}
print(node?.data ?? 0)

```
  </p>
</details>
    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드 2</a></summary>
  <p>

```swift
import Foundation

class Node<T> {
    var data: T
    var pointer: Node<T>?
    
    init(data: T, pointer: Node<T>? = nil) {
        self.data = data
        self.pointer = pointer
    }
}

class NodeManager<T> {
    var head: Node<Int>?
    var node: Node<Int>?
    
    init(data: Int) {
        self.head = Node(data: data)
    }
    
    func add(_ data: Int) {
        node = self.head //처음 노드 할당
        
        while node?.pointer != nil { //마지막 노드를 찾을때까지 반복
            node = node?.pointer
        }
        
        node?.pointer = Node(data: data) //마지막 노드찾음 ㅇㅋ
    }
    
    //특정 데이터 삭제하기
    func delete(_ data: Int) {
        if self.head!.data == data { //1. 맨앞에 있는 애를 삭제하는 경우 (헤드를 삭제하는 경우)
            self.head = self.head!.pointer
        } else { //2. 마지막에 있는 애를 삭제하는 경우, 3. 중간에 있는 애를 삭제하는 경우
            node = self.head
            while node?.pointer != nil {
                if node!.pointer!.data == data {
                    node?.pointer = node?.pointer?.pointer
                } else {
                    node = node?.pointer
                }
            }
        }
    }
    
    func search(_ data: Int) -> Node<Int>? {
        node = self.head
        
        while node != nil {
            if node?.data == data {
                return node
            } else {
                node = node?.pointer
            }
        }
        
        return nil
    }
    
    //단순 전체 데이터 출력 용도 메소드
    func desc() {
        node = self.head // add하면서 node값이 달라졌으므로 첫노드로 다시 할당
        
        while node != nil { //노드가 비어있지 않다면 반복
            guard let unwrapNode = node else { return }
            print(unwrapNode.data)
            node = node?.pointer
        }
    }
}

let linkedList = NodeManager<Int>(data: 0)

print("데이터 추가 후 출력======================================================")

for data in 1...10 {
    linkedList.add(data)
}
linkedList.desc()

print("4 삭제 확인======================================================")

linkedList.delete(4)

linkedList.desc()

print("10 삭제 확인======================================================")

linkedList.delete(10)

linkedList.desc()

print("3 노드 찾기======================================================")

let searchNode = linkedList.search(3)
print(searchNode!.data)


//while 문
//var count = 0
//while count < 10 { // 이 조건인 동안만 아래 스코프를 타라.
//    print(count)
//    count += 1
//}

```
  </p>
</details>

- 배열의 최대 단점이 미리 길이를 지정해야 된다는 것이다. 새로운 공간을 만들어서 데이터를 추가한다고 해도 index는 4,5,6...이 아니라 0,1,2...부터 다시 시작하기 때문이다. 링크드 리스트는 그런 배열의 단점을 해결할 수 있다. 
- 구성 
    - 노드(Node): 데이터값 + 포인터(다음데이터가 어딨는지 가리키는 주소) 
    - 포인터(pointer): 각 노드 안에서, 다음이나 이전의 노드와의 주소값을 가지고 있는 공간
- 제일 첫번째 노드를 head이라고 한다. 더블 링크드 리스트가 아니므로 tail은 없다.
			
![](https://velog.velcdn.com/images/dev_kickbell/post/988be38e-fba5-4d63-8cf4-8faa93626b44/image.png)				
				
- 장점
    - 미리 데이터 공간을 미리 할당하지 않아도 됨
- 단점
    - 데이터와는 별개로 포인터를 저장하기 위한 별도의 공간이 필요하므로 저장공간 효율이 좋진 않음.
	- 앞에서 하나하나 찾아서 가야하므로 접근 속도가 느림.(이것 때문에 생긴 것이 더블 링크드 리스트) 
	- 상대적으로 복잡함. 중간 데이터 삭제시, 앞뒤 데이터의 포인터를 재연결해줘야 하는 부가적인 작업이 필요하기 때문.


## Double Linked List(더블 링크드 리스트)
		    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드 1</a></summary>
  <p>

```swift
import Foundation


class Node<T> {
    var data: T
    var prev: Node<T>?
    var next: Node<T>?
    
    init(data: T, prev: Node<T>? = nil, next: Node<T>? = nil) {
        self.data = data
        self.prev = prev
        self.next = next
    }
}

class NodeManager<Int: Equatable> {
    var head: Node<Int>?
    var node: Node<Int>?
    
    init(data: Int) {
        self.head = Node(data: data)
    }
    
    func add(_ data: Int) {
        //1. 포인터를 타고 넘어갈 시작부분 할당
        self.node = self.head
        //2. 마지막 노드를 찾아감. while문을 통과하면 node는 데이터는 있고 다음 next는 nil인 마지막 노드를 가리키게 됨.
        while self.node?.next != nil {
            self.node = self.node?.next
        }
        //3. 마지막 노드에 데이터를 추가함. 생성자에 next = nil이 초기값으로 지정되어 있으므로 data만 추가하면 됨.
        self.node?.next = Node(data: data)
    }
    
    func delete(_ data: Int) {
        //1. 포인터를 타고 넘어갈 시작부분 할당
        self.node = self.head
        
        while self.node?.next != nil {
            //2. 삭제하고자 하는 노드 찾기
            if let unwrapData = self.node?.next?.data, unwrapData == data {
                self.node?.next = self.node?.next?.next
                return
            }
            self.node = self.node?.next
        }
    }
    
    func search(_ data: Int) {
        self.node = self.head
        
        while self.node?.next != nil {
            if let unwrapData = self.node?.data, unwrapData == data {
                print("\(unwrapData) 에 해당하는 데이터값이 있습니다. ")
                return
            }
            self.node = self.node?.next
        }
        
        print("해당하는 데이터값이 없습니다.")
    }
    
    func desc() {
        //순회 전에 제일 처음 초기값 할당
        self.node = self.head
        
        //가장 마지막 데이터 이전까지 출력
        while self.node?.next != nil {
            if let data = self.node?.data {
                print(data)
            }
            self.node = self.node?.next
        }
        
        //while문을 빠져나와서 마지막 데이터까지 출력하기
        if let data = self.node?.data {
            print(data)
        }
    }
}


let linkedList = NodeManager(data: 0)

print("\n==추가==")
(1...10).forEach { linkedList.add($0) }
linkedList.desc()

print("\n==검색==")
linkedList.search(4)

print("\n==삭제==")
linkedList.delete(3)
linkedList.desc()
```
  </p>
</details>
    
<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드 2</a></summary>
  <p>

```swift
import Foundation


class Node<T> {
    var data: T
    var prev: Node<T>?
    var next: Node<T>?
    
    init(data: T, prev: Node<T>? = nil, next: Node<T>? = nil) {
        self.data = data
        self.prev = prev
        self.next = next
    }
}

class NodeManager<Double: Equatable> {
    var head: Node<Double>?
    var tail: Node<Double>?
    var node: Node<Double>?
    
    init(data: Double) {
        self.head = Node(data: data)
    }
    
    func add(_ data: Double) {
        //1. 포인터를 타고 넘어갈 시작부분 할당
        self.node = self.head
        //2. 마지막 노드를 찾아감. while문을 통과하면 node는 데이터는 있고 다음 next는 nil인 마지막 노드를 가리키게 됨.
        while self.node?.next != nil {
            self.node = self.node?.next
        }
        //3. 마지막 노드에 데이터를 추가함. 생성자에 next = nil이 초기값으로 지정되어 있으므로 data만 추가하면 됨.
        let newNode = Node(data: data)
        self.node?.next = newNode
        //4. 앞뒤로 포인터를 연결해줘야 하므로 prev에도 node를 할당
        newNode.prev = self.node
        //5. tail도 할당
        self.tail = newNode
    }
    
    //연습3: 위 코드에서 노드 데이터가 특정 숫자인 노드 앞에 데이터를 추가하는 함수를 만들고, 테스트해보기</font></strong><br>
    //- 더블 링크드 리스트의 tail 에서부터 뒤로 이동하며, 특정 숫자인 노드를 찾는 방식으로 함수를 구현하기<br>
    //- 테스트: 임의로 0 ~ 9까지 데이터를 링크드 리스트에 넣어보고, 데이터 값이 2인 노드 앞에 1.5 데이터 값을 가진 노드를 추가해보기
    func insert(_ data: Double, beforeData: Double) {
        self.node = self.tail //1. 뒤에서 부터 갈거니까 tail 할당
        
        //2. beforeData앞에 넣을거니까 그걸 먼저 찾는다. 다르다면 한칸 앞으로 이동
        while self.node!.data != beforeData {
            self.node = self.node?.prev
            if node?.prev == nil { //맨 앞까지 왔다면 해당 데이터가 없는거니까 그냥 break
                print("해당 beforeData : \(beforeData) 는 존재하지 않습니다.")
                return
            }
        }
        
        //3. 여기까지 왔다면 self.node == beforeData를 갖는 노드라는 말이야. 이 애 앞에 새로운 데이터를 넣을거야.
        let preNode = node?.prev
        let newNode = Node(data: data)
        
        //4. 새로만든 노드의 next 포인터 수정작업
        preNode?.next = newNode
        newNode.prev = preNode
        
        //5. 새로만든 노드의 prev 포인터 수정작업
        newNode.next = self.node
        self.node?.prev = newNode
    }
        
    enum Direction {
        case forward
        case backward
    }
    
    func search(_ direction: Direction, _ data: Double) {
        switch direction {
        case .forward:
            self.node = self.head
            
            while self.node?.next != nil {
                if let unwrapData = self.node?.data, unwrapData == data {
                    print("\(unwrapData) 에 해당하는 데이터값이 있습니다. ")
                    return
                }
                self.node = self.node?.next
            }
        case .backward:
            self.node = self.tail
            
            while self.node?.prev != nil {
                if let unwrapData = self.node?.data, unwrapData == data {
                    print("\(unwrapData) 에 해당하는 데이터값이 있습니다. ")
                    return
                }
                self.node = self.node?.prev
            }
        }
        
        print("해당하는 데이터값이 없습니다.")
    }
    
    func desc() {
        //순회 전에 제일 처음 초기값 할당
        self.node = self.head
        
        //가장 마지막 데이터 이전까지 출력
        while self.node?.next != nil {
            if let data = self.node?.data {
                print(data)
            }
            self.node = self.node?.next
        }
        
        //while문을 빠져나와서 마지막 데이터까지 출력하기
        if let data = self.node?.data {
            print(data)
        }
    }
}


let linkedList = NodeManager(data: 0.0)

print("\n==추가==")
(1...10).map { Double($0)}
    .forEach{ linkedList.add($0) }
linkedList.desc()

print("\n==검색==")
linkedList.search(.forward, 5)
linkedList.search(.backward, 8)

print("\n==중간에 데이터 추가==")
linkedList.insert(5.5, beforeData: 6)
linkedList.desc()
```
  </p>
</details>

- 변형된 형태의 링크드 리스트
- 링크드리스트의 구성이 노드와 포인터로 되어있다면, 더블 링크드 리스트는 포인터가 앞뒤로 2개다. 네이밍은 보통 prev, next 라는 식으로 한다. 포인터가 2개인 것만 생각하면 된다.
- 제일 첫번째 노드를 head, 제일 마지막 노드를 tail 이라고 한다.
- 기존의 링크드리스트의 단점이 head에서부터 쭉 찾아가야했음. 만약 정렬된 1만개의 데이터가 있는데 내가 찾는 데이터는 9999 라면, 기존의 링크드리스트는 1부터 찾아가야함. 그래서 이 문제를 해결하기 위해 더블 링크드 리스트가 나옴. tail이 있어 뒤에서부터 찾아갈 수가 있음.
- 장점
    - 포인터가 양방향으로 연결되어 있어서 노드 탐색이 양쪽으로 모두 가능.
- 단점
    - tail이 추가되었고, 중간에 데이터를 삭제/수정할 때 prev/next 포인터를 재연결해줘야 하는 작업이 링크드리스트보다 2배이므로 코드가 상대적으로 복잡함. 
  			
![](https://velog.velcdn.com/images/dev_kickbell/post/ce743fee-47c9-4536-ae39-80f02f97faa4/image.png)				
				    
## Hash Table(해쉬 테이블) 

<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드 1</a></summary>
  <p>

```swift
import Foundation

/*
struct Car: Hashable {
    var name: String
    var price: Int
    var codeName: String
}
struct Human: Hashable {
    var age: String
    var name: String
}
let car = Car(name: "그랜져", price: 30_000_000, codeName: "DH330")
let car2 = Car(name: "그랜져", price: 30_000_000, codeName: "DH330")
let human = Human(age: "35살", name: "김개똥")
//또 Hashable를 준수하면 구조체끼리도 비교할 수 있다.
print(car.hashValue) //-5235493752956059578
print(car2.hashValue) //-5235493752956059578, 내부데이터가 같기때문에 해시값도 똑같다.
print(human.hashValue) //1058167045924687262
print(car.hashValue == human.hashValue)
 */


//데이터를 저장할 해시 테이블 생성
var hashTable = (0..<8).map { _ in "" }
print(hashTable)

//데이터 값을 통해 키값을 반환
func getKey(data: String) -> Int {
    return data.hashValue // String, Int등은 다 Hashable을 준수하고 있어서 hashValue라는 걸로 바로 만들 수 있다. 고정된 길이의 Int를 반환
}

//해시함수를 통해 저장할 해시 주소를 반환
func hashFunction(key: Int) -> Int {
    return abs(key) % 8 //abs는 절대값. 음수키가 들어오면 양수로 바꿔주기 위해
}

func saveData(data: String, value: String) {
    let key = getKey(data: data) //데이터 값을 통해 키값을 반환
    let hashAddress = hashFunction(key: key) //해시함수를 통해 저장할 해시 주소를 반환
    hashTable[hashAddress] = value //생성한 해시 테이블에 해시 주소를 index 값으로 해서 데이터를 저장
}

func readData(data: String) -> String {
    let key = getKey(data: data)
    let hashAddress = hashFunction(key: key)
    return hashTable[hashAddress]
}

saveData(data: "사과", value: "10_000")
saveData(data: "포도", value: "20_000")
saveData(data: "수박", value: "30_000")

print(readData(data: "수박"))
print(hashTable)



typealias HashAddress = Int
```
  </p>
</details>

<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드 2</a></summary>
  <p>

```swift
import Foundation



/*
 #### 6.1. Chaining 기법
 - **개방 해슁 또는 Open Hashing 기법** 중 하나: 해쉬 테이블 저장공간 외의 공간을 활용하는 기법
 - 충돌이 일어나면, 링크드 리스트라는 자료 구조를 사용해서, 링크드 리스트로 데이터를 추가로 뒤에 연결시켜서 저장하는 기법
 */

typealias hashKey = Int
typealias hashValue = String
typealias HashAddress = Int

//데이터를 저장할 해시 테이블 생성
var hashTable: [[(key: hashKey, value: hashValue)]] = (0..<8).map { _ in [(0, "")] }
hashTable.forEach { print($0) }

//데이터 값을 통해 키값을 반환
func getKey(data: String) -> Int {
    return data.hashValue // String, Int등은 다 Hashable을 준수하고 있어서 hashValue라는 걸로 바로 만들 수 있다. 고정된 길이의 Int를 반환
}

//해시함수를 통해 저장할 해시 주소를 반환
func hashFunction(key: Int) -> Int {
    return abs(key) % 8 //abs는 절대값. 음수키가 들어오면 양수로 바꿔주기 위해
}







func saveData(data: String, value: String) {
    let key = getKey(data: data) //데이터 값을 통해 키값을 반환
    let hashAddress = hashFunction(key: key) //해시함수를 통해 저장할 해시 주소를 반환
    
    if let firstKey = hashTable[hashAddress].first?.key, firstKey == 0 {
        //hashTable -> [[(key: hashKey, value: hashValue)]] 이고,
        //hashTable[hashAddress] -> [(key: hashKey, value: hashValue)] 이다.
        //첫번쨰 데이터의 키값이 0 이라는 것은 초기값 [(0, "")] 이라는 것이므로 아직 데이터가 들어가기 전이라는 말이다.
        //그래서 데이터를 추가해준다.
        hashTable[hashAddress] = [(key, value)]
    } else {
        //그렇지 않다는 것은 이미 데이터가 1개 들어가있다는 소리다. 충돌 Collision 난다는 거다.
        //그리고 그걸 Chaining 방법으로 해결하려고 한다.
        //원래대로라면 여기가 링크드리스트를 따로 구현해서 한다고 하는데, 지금은 hashTable -> [[(key: hashKey, value: hashValue)]]
        //와 같은 식으로 그냥 배열과 튜플을 이용해서 for 문으로 같은 효과를 보면서 작업해 주려고 한다.
        hashTable[hashAddress].append((key: key, value: value))
    }
}

func readData(data: String) -> String {
    let key = getKey(data: data)
    let hashAddress = hashFunction(key: key)
    
    if let firstKey = hashTable[hashAddress].first?.key, firstKey == 0 {
        return "" //데이터 없음
    } else {
        for index in 0..<hashTable[hashAddress].count {
            if hashTable[hashAddress][index].key == key {
                return hashTable[hashAddress][index].value
            }
        }
        return ""
    }
}


/*
 해시값이 실행할 떄마다 달라지기 떄문에 충돌을 내려면 여러번 실행해야하는데 정상적으로 충돌이 나면 아래처럼 나오게 된다.
 ====totalData====
 [(key: 0, value: "")]
 [(key: 0, value: "")]
 [(key: -7067232576260731650, value: "10_000"), (key: 8145960162044253770, value: "20_000")]
 [(key: 0, value: "")]
 [(key: 0, value: "")]
 [(key: 0, value: "")]
 [(key: 0, value: "")]
 [(key: 0, value: "")]
 */


saveData(data: "사과", value: "10_000")
saveData(data: "포도", value: "20_000")

print("\n====readData====")
print(readData(data: "사과"))

print("\n====totalData====")
hashTable.forEach { print($0) }


```
  </p>
</details>

<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드 3</a></summary>
  <p>

```swift

import Foundation



/*
 #### 6.2. Linear Probing 기법
 - **폐쇄 해슁 또는 Close Hashing 기법** 중 하나: 해쉬 테이블 저장공간 안에서 충돌 문제를 해결하는 기법
 - 충돌이 일어나면, 해당 hash address의 다음 address부터 맨 처음 나오는 빈공간에 저장하는 기법
   - 저장공간 활용도를 높이기 위한 기법
 */

typealias hashKey = Int
typealias hashValue = String
typealias HashAddress = Int

//데이터를 저장할 해시 테이블 생성
var hashTable: [(key: hashKey, value: hashValue)] = (0..<8).map { _ in (0, "") }
hashTable.forEach { print($0) }

//데이터 값을 통해 키값을 반환
func getKey(data: String) -> Int {
    return data.hashValue // String, Int등은 다 Hashable을 준수하고 있어서 hashValue라는 걸로 바로 만들 수 있다. 고정된 길이의 Int를 반환
}

//해시함수를 통해 저장할 해시 주소를 반환
func hashFunction(key: Int) -> Int {
    return abs(key) % 8 //abs는 절대값. 음수키가 들어오면 양수로 바꿔주기 위해
}







func saveData(data: String, value: String) {
    let key = getKey(data: data) //데이터 값을 통해 키값을 반환
    let hashAddress = hashFunction(key: key) //해시함수를 통해 저장할 해시 주소를 반환
//    print(hashAddress, "Address")
    if hashTable[hashAddress].key == 0 {
        //데이터가 비어있다면
        hashTable[hashAddress] = (key, value)
    } else {
        //데이터가 비어있지 않다면 충돌 Collision 난다는 거다.
        //그리고 그걸 Linear Probing 방법으로 해결하려고 한다.
        //선형 탐색?너낌으로 이후를 돌아주면 되는데.
        for index in (hashAddress...hashTable.count-1) {
            if hashTable[index].key == 0 {
                //빈칸을 발견했으므로 여기다 충돌되는 데이터를 넣어주면 되겠다.
                hashTable[index] = (key, value)
                return //리턴 키워드 꼭 필요하다. 안해주면 이후 배열에 데이터 다들어감.
            } else if hashTable[index].key == key {
                //키가 같다는 것은 해당 데이터를 업데이트 한다는 것이다.
                hashTable[index].value = value
                return
            }
        }
    }
}

func readData(data: String) -> String {
    let key = getKey(data: data)
    let hashAddress = hashFunction(key: key)
    
    if hashTable[hashAddress].key == 0 {
        return ""
    } else {
        for index in (hashAddress...hashTable.count-1) {
            if hashTable[index].key == 0 {
                return ""
            } else if hashTable[index].key == key {
                return hashTable[index].value
            }
        }
        return ""
    }
}


/*
 4 Address
 4 Address
 
 ====readData====
 10_000
 ====totalData====
 (key: 0, value: "")
 (key: 0, value: "")
 (key: 0, value: "")
 (key: 0, value: "")
 (key: -6740742047845731564, value: "10_000")
 (key: 3996458706203432476, value: "20_000")
 (key: 0, value: "")
 (key: 0, value: "")
 */


saveData(data: "사과", value: "10_000")
saveData(data: "포도", value: "20_000")

print("\n====readData====")
print(readData(data: "사과"))

print("\n====totalData====")
hashTable.forEach { print($0) }

```
  </p>
</details>

  - 키(Key)에 데이터(Value)를 저장하는 데이터 구조, Swift에서는 딕셔너리로 바로 사용가능 	
  - 보통 배열로 미리 Hash Table 사이즈만큼 생성 후에 사용 (공간과 탐색 시간을 맞바꾸는 기법 -> 해시테이블의 공간을 늘림으로써 키값이 중복되는 것에 대한 충돌(Collision)을 줄여서 추가적인 키값중복에 대한 자료구조나 로직을 사용하지 않게 한다는 말이다. 그래서 공간을 + 해서 시간을 - 한다는 뜻.)
  - 구조 
      * 해쉬(Hash): 임의 값을 고정 길이로 변환하는 것을 말함. 
    	- SHA(Secure Hash Algorithm, 안전한 해시 알고리즘)
    	- SHA256: 256비트로 항상 64자길이 값으로 리턴해준다.
      * 해쉬 값(Hash Value): 해쉬(Hash) 과정으로 얻는 값. 
      * 해쉬 함수(Hash Function): 해쉬 값(Hash Value)을 원하는 대로 구현해 놓은 해시 함수에 넣으면 해시 테이블의 몇번째 index에 넣을지를 결정하는 해쉬 주소(Hash Address)를 반환한다. 
      * 해쉬 주소(Hash Address): 해쉬 테이블에 데이터를 넣을 index번호 
      * 해쉬 테이블(Hash Table): 키 값의 연산에 의해 직접 접근이 가능한 데이터 구조 (예를 들면 딕셔너리) 
      * 슬롯(Slot): 한 개의 데이터를 저장할 수 있는 공간
		
![](https://velog.velcdn.com/images/dev_kickbell/post/96df6055-9f71-4f9e-b964-2ffd3a3dbc77/image.png)				

 ```swift 
      import Foundation 
	  
      typealias HashKey = Int
      typealias HashAddress = Int
      typealias Value = String

      //1. 배열로 미리 지정된 크기의 공간을 생성 
      var hashTable: [Value] = (0..<8).map { String($0) } 
      
	  //2. 데이터를 지정된 해싱함수(데이터를 넣으면 고정된 길이를 반환하는 함수)로 넣어 해시키를 반환
      func hashKey(data: String) -> HashKey {
          return data.hashValue
      }
	  //3. 해시 키를 넣으면 배열의 어느 index에 있는지 HashAddress를 반환함 . 
      func hashFunction(key: HashKey) -> HashAddress {
          return key % 8
      }
	  //4. data -> key, value -> 저장할 값이 된다. data로 key를 생성하고 그 key로 저장할 배열의 index를 반환하며, 해당 index에 미리 생성한 해시테이블에 value값을 넣는다. 
      func save(data: String, value: Value) {
          let key = hashKey(data)
          let hashAddress = hashFunction(key)
          hashTable[hashAddress] = value
      }      
    
```
해시 함수는 임이의 길이를 갖는 메시지를 입력받아 고정된 길이의 해시값을 출력하는 함수입니다. 암호 알고리즘에는 키가 사용되지만, 해시 함수는 키를 사용하지 않으므로 같은 입력에 대해서는 항상 같은 출력이 나오게 됩니다.     
	
- 충돌(Collision) 
    - 어떤 키값에 2개 이상의 데이터가 들어가는 경우를 말한다. 
    - 데이터를 덮어쓴다면 충돌하지 않겠지만, 그렇지 않은 경우를 말한다. 예를 들면, data1 = 'Andy'와 data2 = 'Andrew'라는 데이터가 있을 때 파이썬의 ord() 함수를 이용해서 ord(data1[0
]), ord(data2[0])를 실행하면 같은 A에 해당하는 65라는 아스키코드 값이 리턴된다. 이렇게 되면 다른 데이터에 같은 key값이 리턴되고 하나의 key값에 2개의 데이터가 들어가는 것이다. 이걸 충돌이라고 한다. 
    - 이런 문제를 해결하기 위해서 별도 자료구조가 필요하다. 
    

- 충돌(Collision) 해결하기 
    - 1. Chaining 기법 
        - 충돌이 일어나면, 링크드 리스트라는 자료 구조를 사용해서, 링크드 리스트로 데이터를 추가로 뒤에 연결시켜서 저장하는 기법이다. 공간이 추가로 들어간다. 
    - 2. Linear Probing 기법
        - 충돌이 일어나면, 해당 hash address의 다음 address부터 맨 처음 나오는 빈공간에 저장하는 기법
        - 저장공간 활용도를 높이기 위한 기법
        
- 빈번한 충돌을 개선하는 기법 
    - 해쉬 함수을 재정의 및 해쉬 테이블 저장공간을 확대
    - 예: 4개의 데이터를 저장한다고 했을때 보통 key % 8, rage(8)로 하면 충돌이 일어날 확률이 높으니 그 2배인 16으로 공간을 더 많이 잡는다. 
    - 공간으로 시간을 사는 셈이지만, 해쉬 테이블을 쓰는 이유가 충돌을 최대한 지양하고 O(1)로 쓰려고 하는것이므로 이게 제일 가장 간단하면서도 효율적인 방법이다. 
    ```python
    hash_table = list([None for i in range(16)])

    def hash_function(key):
        return key % 16
    ```

- 장점
    - 어떤 키값에 대한 데이터가 있는지 여부만 판단하면 되기 때문에 데이터를 저장, 읽기 속도가 빠르다.
    - 웹 사용시 사용하는 캐시메모리같은 것도 해시테이블을 사용하는 것이다. 해당 키값에 대응하는 데이터가 있는지 여부(중복확인)이 빠르기 때문에 어떤 이미지를 이미 다운받았으면 새로요청하지않고 변경된 데이터만 요청해서 웹브라우저의 사용성을 높이는 그런것들 말이다. 이걸 배열로 해야된다고 하면 0번 index부터 차례차례 데이터가 중복인지 순회해야 한다.
- 단점 
    - 충돌(Collision)이 발생할 수 있어 일반적으로 저장공간이 더 필요하다.
    - **여러 키에 해당하는 주소가 동일할 경우 충돌을 해결하기 위한 별도 자료구조가 필요함** 
- 주요 용도
    - 검색이 많이 필요한 경우
    - 저장, 삭제, 읽기가 빈번한 경우
    - 캐쉬 구현시 (중복 확인이 쉽기 때문) 

- 시간 복잡도
    - 일반적인 경우(Collision이 없는 경우)는 O(1)
    - 최악의 경우(Collision이 모두 발생하는 경우)는 O(n)
    - 해쉬 테이블의 경우, 일반적인 경우를 기대하고 만들기 때문에, 시간 복잡도는 O(1) 이라고 말할 수 있음

## Tree(트리) 

## Heap(힙) 

## Reference
[https://fastcampus.co.kr/dev_online_devjob/?gclid=Cj0KCQjwqc6aBhC4ARIsAN06NmOdZUJGwc8x-w8ht0jS3idCSNhUZ49m--cQdDsVqIZWSnVmrBz5zxMaAtTBEALw_wcB](https://fastcampus.co.kr/dev_online_devjob/?gclid=Cj0KCQjwqc6aBhC4ARIsAN06NmOdZUJGwc8x-w8ht0jS3idCSNhUZ49m--cQdDsVqIZWSnVmrBz5zxMaAtTBEALw_wcB)

[https://www.fun-coding.org/python-question1-answer.html](https://www.fun-coding.org/python-question1-answer.html)


<details>
  <summary><a href="https://github.com/kickbell/DataStructure_and_Algorithm">스위프트 코드</a></summary>
  <p>

```swift

```
  </p>
</details>
    
