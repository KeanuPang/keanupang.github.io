---
title: "快速實驗與驗證功能的小工具 swift-sh"
date: 2021-12-03
draft: false
---

OK, 我們現在可以用 [swift-sh](https://github.com/mxcl/swift-sh) 工具預先安裝到系統裡，這樣子之後只要做小型測試時只要單一個 swift 檔案，裡面標註 import 的敘述，就可以直接執行它。

先安裝好 swift-sh 工具：

```
brew install swift-sh
```

然後就可以開始編寫 swift 檔案做小型的驗證：

```swift
#!/usr/bin/swift sh

import Foundation
import PromiseKit  // @mxcl ~> 6.5

firstly {
    after(.seconds(2))
}.then {
    after(.milliseconds(500))
}.done {
    print("notice: two and a half seconds elapsed")
    exit(0)
}

RunLoop.main.run()
```

要記得檔案最前面的 shebang 要寫明 `#!/usr/bin/swift sh` 的敘述，這樣系統才會去觸發 swift-sh 工具來處理；以及 import 第三方套件時，要在該行的最後加上註解是哪個 SwiftPM 的來源，甚至可以標註版本。

最後只要把這個檔案加上可執行權限就可以直接執行了。
