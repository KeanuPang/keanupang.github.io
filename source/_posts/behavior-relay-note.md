---
title: BehaviorRelay 筆記
date: 2021-10-26
tags: ["rxswift"]
---

因為是單向的資訊流，在每次更新資訊時都是用 ``.accept` 送出最近資訊，所以最好的做法是如果本身的資訊是組合性的 struct 則拆開成數個 BehaviorRelay 各別更新會比較好，這樣綁定在不同的 UIView 元件會比較好處理。

例如本來是長這樣：

```swift
struct Info {
  var propertyA: Bool = false
  var propertyB: Bool = false
  var propertyC: Bool = false
}
```

就拆成三個 property 各自維護：

```swift
let propertyA = BehaviorRelay<Bool>(value: false)
let propertyB = BehaviorRelay<Bool>(value: false)
let propertyC = BehaviorRelay<Bool>(value: false)
```

然後就可以在 ViewController 層各別 Driver 綁定；
在最後如果需要組合判斷時才在用 `Observable.combineLatest` 來處理，例如：

```swift
var isEnable: Driver<Bool> {
  return Observable.combineLatest(propertyA, propertyB, propertyC)
    .map { $0 && $1 && $2 }
    .asDriver(onErrorJustReturn: false)
}
```

這樣就可以做到資訊分流，最終統一控管的效果。
