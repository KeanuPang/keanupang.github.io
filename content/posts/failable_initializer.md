---
title: "Failable Initializer 筆記"
date: 2021-06-01
draft: false
---

之前在 [這篇文章](https://serialcoder.dev/text-tutorials/swift-tutorials/failable-initializer-in-swift/) 看到的。

將自己的 class 來做出一個可以是 optional 的物件，這樣一來使用的時候就必須用 `if-let` 或 `guard` 來讀取，在程式上會比較好理解跟處理。

直接上 code：

```swift
class FilteredImage {
    ...

    init?(with imageName: String) {
        guard let image = UIImage(named: imageName) else {
            return nil
        }
        self.image = image
    }
}
```

也就是說在初始化時是用 `init?` 的方式，確保回傳得到的物件要嘛是 nil 要嘛真的有值，而且對應的屬性也保證有值。

使用上也因為初始化回傳的是 optional 物件，限制了使用者必須用 `if-let` 或 `guard` 來拿取：

```swift
guard let filteredImage = FilteredImage(with: "bird") else {
    // filteredImage is nil
    return
}

// filteredImage is initialized and we use it normally
```

然後要保持一個原則，回傳是 nil 一定是因為初始化失敗而得到的，如果是後面的行為造成的話等於權責不分無法抓住問題：

> In case where the initialization does not fail, then there is nothing to return; just handle everything as you would do in a normal initializer.
