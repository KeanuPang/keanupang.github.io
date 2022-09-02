---
title: "Rust Extensions with VS Code"
date: 2022-09-02T10:26:31+08:00
---

最近在初學 Rust 然後為了要在 VS Code 裡能有較好的開發體驗，所以找了幾個還不錯的 extensions 方便使用。

1. [rust-analyzer](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer)

   必裝的工具，我自己是安裝 Pre-Release 的版本先享受比較新的功能，目前使用上算穩定。

2. [Event Better TOML](https://marketplace.visualstudio.com/items?itemName=tamasfe.even-better-toml)

   會幫忙將 toml 檔案內容檢查語法跟 syntax highlighting，另外也有貼心的 formatting 跟 folding 功能，在編輯 toml 檔案會方便很多。

3. [crates](https://marketplace.visualstudio.com/items?itemName=serayuzgur.crates)

   這工具最讓我驚豔的功能是在編輯 Cargo.toml 檔案的 dependencies 時，可以做版本的檢查，引用的版本如果沒問題就會顯示綠色勾勾，這其實會給開發者比較大的信心確認 dependency 版本：
   ![](/images/rust-development-vscode-01.png)

4. [CodeLLDB](https://marketplace.visualstudio.com/items?itemName=vadimcn.vscode-lldb)

   為了能 debug Rust 所以需要裝上這套工具，就可以在指定的中斷點上查看變數跟 call stack 等 debug 操作。

就目前來說，這幾個工具已經有滿足需求，在開發上的體驗也滿正面，已經很久沒有花時間力氣在 VS Code 上面，希望這個過程能一切順利。
