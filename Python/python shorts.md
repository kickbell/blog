## for 문 
> ### python
```python
list = [4,5,6,7,8]
length = len(list)
for i in range (0, length): 
    print(i)   
0
1
2
3
4
```
> ### swift
```swift
let list = [4,5,6,7,8]
let length = list.count
print("type 1")
for i in Range(0...length-1) { //..< 하면 컴파일 에러 발생 
    print(i)
}
print("type 2")
for i in 0..<length{
    print(i)
}
type 1
0
1
2
3
4
type 2
0
1
2
3
4
```
