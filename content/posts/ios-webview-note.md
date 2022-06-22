---
title: 幾個 WebView 互動功能筆記
date: 2022-03-22
tags: ["ios", "webview"]
---

好像只有在亞洲地區才流行這樣的組合，也就是從 native app 畫面開 web view 切到行銷活動網頁，再透過 javascript 方式跟 app 互動之後回到 app 裡進行後面的導頁與串行銷資料。

### 同步切換 app 畫面標題

為了要讓 webView 載入的網頁標題也能貼回到 App 裡的 navigation bar 上，所以在 `viewDidLoad()` 的時候，加上 `addObserver`：

```swift
webView.addObserver(self, forKeyPath: #keyPath(WKWebView.title), options: .new, context: nil)
```

為了避免 leak，若有開啟 `addObserver` 更新 title 的話，在 ViewController dismiss 時要移除掉：

```swift
webView.removeObserver(self, forKeyPath: "title")
```

### 顯示與取消 Loading

對應到 `WKNavigationDelegate` 可以透過下方兩個 methods 顯示或取消自訂的 loading view：

```swift
 func webView(_ webView: WKWebView, didStartProvisionalNavigation navigation: WKNavigation!) {
        guard isBeingDismissed || isMovingFromParent else {
            // present loading view
            return
        }
 }
 func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!) {
        // dismiss loading view
 }
```

同理，通常也會在 `didFinish` 的時候埋一個 callback function 用來觸發自訂的行為：

```swift
var pageIsLoaded: (() -> Void)?

func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!) {
        // dismiss loading view
        self.pageIsLoaded?()
}
```

### 檢查回應 status code

如下例檢查 status code 不是 200 類則進行後續行為：

```swift
func webView(_ webView: WKWebView, decidePolicyFor navigationResponse: WKNavigationResponse, decisionHandler: @escaping (WKNavigationResponsePolicy) -> Void) {
        if let response = navigationResponse.response as? HTTPURLResponse {
            if (200 ..< 300 ~= response.statusCode) == false {
                // present alert view
            }
        }
        decisionHandler(.allow)
}
```

### 處理自訂錯誤

如下例處理自訂錯誤：

```swift
func webView(_ webView: WKWebView, didFailProvisionalNavigation navigation: WKNavigation!, withError error: Error) {
        // handle error
}
```

### 加上自訂 WKWebViewConfiguration

需要在 `WKWebView` 生成之前先將 `WKWebViewConfiguration` 建立好，才能夠在 `WKWebView` 建立時候傳入。

建立的方式如下：

```swift
private var customConfiguration: WKWebViewConfiguration?
private weak var scriptMessageHandler: WKScriptMessageHandler?

private func setupWebViewConfiguration() {
    self.scriptMessageHandler = self
    if let handler = scriptMessageHandler {
        let contentController = WKUserContentController()
        contentController.add(handler, name: WebViewScriptHandlerName.ncbmb.rawValue)

        self.customConfiguration = WKWebViewConfiguration()
        self.customConfiguration?.userContentController = contentController
    }

    webView = WKWebView(frame: .zero, configuration: self.customConfiguration ?? WKWebViewConfiguration())
    webView.clearCache()
    webView.backgroundColor = .white
    webView.scrollView.backgroundColor = .white
    webView.uiDelegate = self
    webView.navigationDelegate = self
}
```

會需要把 `scriptMessageHandler` 另外拉出來是因為會有 leak 問題，所以 ViewController 在 dismiss 前要先把 handler 釋放掉，通常我們是做成 func 讓 coordinator 呼叫：

```swift
func dismissWebVC() {
    self.dismiss(animated: false) { [weak self] in
        self?.webView?.configuration.userContentController.removeScriptMessageHandler(forName: WebViewScriptHandlerName.ncbmb.rawValue)
        self?.scriptMessageHandler = nil

        if self?.intent?.followWebTitle == true {
            self?.webView?.removeObserver(self!, forKeyPath: "title")
        }
    }
}
```

### 與 WebView 裡的 Javascript 互動

由 App 觸發執行 javascript 給 WebView 裡的網頁方式如下，就會觸發 WebView 裡的 `sendMsg` function：

```swift
func sendMessage(message: ScriptBaseMessage) {
    guard let json = message.toJsonString() else { return }

    self.webView.evaluateJavaScript("sendMsg('\(json)')",
        completionHandler: { _, _ in
    })
}
```

而從 WebView 端收回 json 資料則需要 `WKScriptMessageHandler` protocol 的實作：

```swift
func userContentController(_ userContentController: WKUserContentController, didReceive message: WKScriptMessage) {
    if let msg = self.getData(ScriptCommandMessage.self, message: message) {
        self.receivedCommand(postMessage: msg)
    }
}

private func getData<T: ScriptBaseMessage>(_ type: T.Type, message: WKScriptMessage) -> T? {
    guard let body = message.body as? String else { return nil }
    guard let postMessage = try? JSONDecoder().decode(T.self, from: Data(body.utf8)) else { return nil }

    return postMessage
}

private func receivedCommand<T: ScriptBaseMessage>(postMessage: T) {
    // your message here
}
```

然後因為收到的訊息為了保持彈性設計，所以使用了 Generic 泛型來處理不同情境的資料。

大概就是這樣，實務上會碰到的眉角其實也不少，很多狀況也都是碰到了才有處理的經驗。
