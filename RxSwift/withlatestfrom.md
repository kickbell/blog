# withLatestFrom

### withLatestFrom(second:)

![](https://images.velog.io/images/dev_kickbell/post/a44bee84-5979-4322-9cbb-78f804cced84/image.png)
```swift
let numberSubject = PublishSubject<Int>()
let emojiSubject = PublishSubject<String>()

print("\n---< withLatestFrom(second:) >---\n")
//파라미터로 넣은 emojiSubject의 마지막 이벤트가 호출하는 녀석인 numberSubject가 onNext 될 때마다 방출

numberSubject
  .withLatestFrom(emojiSubject)
  .subscribe(onNext: { print($0) })

emojiSubject.onNext("🐰")
emojiSubject.onNext("🙉")
emojiSubject.onNext("🐷")
emojiSubject.onNext("🐽")
emojiSubject.onNext("🐯")

numberSubject.onNext(1)
numberSubject.onNext(2)
numberSubject.onNext(3)
numberSubject.onNext(4)
```

>  🐯
🐯
🐯
🐯






### withLatestFrom(second:, resultSelector: {})

![](https://images.velog.io/images/dev_kickbell/post/be80e715-5c42-40f8-911a-1235b09c5776/image.png)

```swift
print("\n---< numberSubject.withLatestFrom(second:, resultSelector: {}) >---\n")
//resultSelector를 추가해서 내가 원하는 대로 가공해서 방출

numberSubject
  .withLatestFrom(emojiSubject) { num, emoji in "\(num) \(emoji)" }
  .subscribe(onNext: { print($0) })

emojiSubject.onNext("🐰")
emojiSubject.onNext("🙉")
emojiSubject.onNext("🐷")
emojiSubject.onNext("🐽")
emojiSubject.onNext("🐯")

numberSubject.onNext(1)
numberSubject.onNext(2)
numberSubject.onNext(3)
numberSubject.onNext(4)
````

> 1 🐯
2 🐯
3 🐯
4 🐯

