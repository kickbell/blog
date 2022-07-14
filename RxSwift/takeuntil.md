
# TakeUntil


-> 여기서는 action 이 리액터킷인데, 일반 enum으로 만들어도 원리는 같을거같은데 ? 


스트림을 방출할 것인데,
cancel previous request when the new `.updateQuery` action is fired
take(until:)을 이용해서 새로운 요청이 들어오면 이전 요청을 취소할거야. 즉 종료시켜버리는거지.
https://reactivex.io/documentation/operators/takeuntil.html
그래서 until에 두번째 옵저버블을 넣을건데 action도 ActionSubejct, 즉 옵저버블이란 소리지. 그리고 filter가 있어.
그 말은 여기에 정의된 특정 액션에 따라서 필터링을 해줄 수 있다는 말과 같아.

```swift
1. 이렇게 말이야. 근데 이건 축약 전이야.
self.search(query: query, page: 1)
    .take(until: self.action.filter { action in
        guard case .updateQuery = action else { return false }
        return true
    })
    .map { Mutation.setRepos($0, nextPage: $1) },


2. 아래 메소드처럼 정의하면 이렇게 훨씬 더 줄일 수 있어.
self.search(query: query, page: 1)
    .take(until: self.action.filter(Action.isUpdateQueryAction))
    .map { Mutation.setRepos($0, nextPage: $1) }

extension GitHubSearchViewReactor.Action {
  static func isUpdateQueryAction(_ action: GitHubSearchViewReactor.Action) -> Bool {
    if case .updateQuery = action {
      return true
    } else {
      return false
    }
  }
}


.do(onError: { error in
    if case let .some(.httpRequestFailed(response, _)) = error as? RxCocoaURLError, response.statusCode == 403 { //굳.
        print("⚠️ GitHub API rate limit exceeded. Wait for 60 seconds and try again.")
    }
})
```
