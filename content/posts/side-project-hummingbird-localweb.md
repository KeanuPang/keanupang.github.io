---
title: "試玩 Hummingbird 的 middleware"
date: 2022-06-28T02:05:11+08:00
draft: false
tags: ["swift", "web-service"]
---

之前為了測試 [Hummingbird](https://github.com/hummingbird-project/hummingbird) 這個輕量 server framework 所以做了一個叫 LocalWeb 的 web project，主要是用來讓使用者在開發環境下方便地瀏覽與下載檔案；結果沒想到內建的 middleware 就幾乎把所有事情都搞定了。

當然還是要再加點工才可以方便使用，例如當我點進目錄時，希望可以一覽目錄下的檔案清單，所以就客製化做了個 middleware 掛上來，把目錄下的檔案展開成清單做成連結讓使用者可以下載，當使用者點到檔案時就直接串到 `HBFileMiddleware` 下載檔案這樣。

第一次嘗試實作 Hummingbird 的 middleware 所以花了點時間研究，目前實作版本可以參考這 [DirectoryIndexMiddleware](https://github.com/KeanuPang/LocalWeb/blob/main/Sources/LocalWeb/Middleware/DirectoryIndexMiddleware.swift) ，還好進入門檻不高就是，花最多時間反而是在刻呈現頁面的 html。

因為想呈現那種傳統 Apache web server 檢視目錄下的復古感，於是參考 `HBResponseGenerator` 這個 protocol，是用來產生 response 結果頁，基本上只要實作 `func response(from request: HBRequest)` 把刻好的 html 產生成 `HBResponse` 結果就可以。

成品像是這樣 [DirectoryIndexPage](https://github.com/KeanuPang/LocalWeb/blob/main/Sources/LocalWeb/Middleware/DirectoryIndexPage.swift) 會在剛剛的 `DirectoryIndexMiddleware` 裡被用到回傳 response。

為了求快所以先不套用什麼 template engine，直接用 String 拼出來，之後有時間再研究看看 [Stencil](https://github.com/stencilproject/Stencil) 還是 [Leaf](https://github.com/vapor/leaf) 哪個好用。

雛型差不多完成之後，想說要掛個 HTTPS 功能結果好像沒有那麼直覺。本來以為只要把 [Configuration](https://github.com/hummingbird-project/hummingbird/blob/main/Sources/Hummingbird/Configuration.swift) 裡的 `tlsOptions` 設定上去就好，然後就會被噴 warning 訊息：

```
tlsOptions set in Configuration will not be applied to a BSD sockets server.
Please use NIOTSEventLoopGroup, to run with the Network framework.
```

所以 README.md 要認真看，是寫說 TLS 功能被放在 `HummingbirdTLS` 這個模組下，要先把這個模組掛上來，再將憑證檔案設定進去，HTTPS 才能成功啟用。

調整好的 dependencies 會像如下這樣子，也就是說雖然 `hummingbird` 已經包了` hummingbird-core` 可是為了要掛上 `HummingbirdTLS`，所以還是要特別指明 dependency，然後也是要小心兩邊的版本一致問題：

```swift
dependencies: [
    .package(url: "https://github.com/hummingbird-project/hummingbird-core.git", from: "0.13.5"),
    .package(url: "https://github.com/hummingbird-project/hummingbird", from: "0.16.0"),
]
```

這樣子才可以在 target dependency 裡掛成功 `HummingbirdTLS`：

```swift
dependencies: [
    .product(name: "Hummingbird", package: "hummingbird"),
    .product(name: "HummingbirdFoundation", package: "hummingbird"),
    .product(name: "HummingbirdTLS", package: "hummingbird-core"),
]
```

接下來就是把憑證載入，把準備好的憑證檔案與私鑰轉成 utf8 binary 透過 `TLSConfiguration.makeServerConfiguration()` 建立 `TLSConfiguration`：

```swift
let certificate = try NIOSSLCertificate(bytes: [UInt8](certFile.utf8), format: .pem)
let privateKey = try NIOSSLPrivateKey(bytes: [UInt8](keyFile.utf8), format: .pem)

TLSConfiguration.makeServerConfiguration(certificateChain: [.certificate(certificate)], privateKey: .privateKey(privateKey))
```

註記一下，在 Local development 環境用的憑證檔案我是用懶人包 [mkcert](https://github.com/FiloSottile/mkcert) 不用一秒就可以生出憑證檔，很方便很好用。

然後回到 server 這邊用 `addTLS()` 把剛剛的 configuration 餵進去就完成了：

```swift
try app.server.addTLS(tlsConfiguration: tlsConfiguration)
```

會這麼說不直覺是因為整個 Hummingbird Project 都沒有明顯的地方或文字告訴你要怎麼把憑證與設定掛上去，花了不少時間摸索才找到正確的方式，踩過的路才會印象深刻。

最後完整的 project 可以參考這裡：[LocalWeb](https://github.com/KeanuPang/LocalWeb)
