---
title: "套件組合技經驗：JSON Server"
date: 2022-07-04T00:00:18+08:00
draft: false
tags: ["nodejs", "web-service"]
---

前陣子因為在開發環境需要有個簡易又不會太粗糙的 mock server，所以快速用短短的時間把幾個套件組合串起來，成為基本可用的迷你 project，最主要還是拜豐富 NodeJS 的生態系所賜，很多東西都有現成可用幾乎不用多寫幾行程式碼，原則上就是可以推到開發環境。

一開始先找到最熱門的 [json server](https://github.com/typicode/json-server) 就是 typicode 的專案，跑起來基本上已經符合需求，所以從這邊開始來組合。

如果是用程式碼方式建立 json server 來做客製化，其實骨子裡就是 express web framework，所以用法都是一樣：

```swift
const https = require("https");

const devcert = require("devcert");
const jsonServer = require("json-server");

const server = jsonServer.create();
server.use(jsonServer.defaults());

server.use(jsonServer.rewriter(require("./routes.json")));
server.use(jsonServer.router("api.json"));

async function start() {
  let ssl = await devcert.certificateFor("api.dev");
  https.createServer(ssl, server).listen(8443);
}
start();

```

開發環境希望也是走 HTTPS 方式所以用到 [devcert](https://github.com/davewasmer/devcert) 掛了一個自簽的開發用憑證就可以跑 HTTPS 了，如上幾乎就是開箱即用的 server。

由於需要 db.json 檔裡面就是擺好被視為資料庫的 JSON 內容，然後依 request 的 HTTP method 進行資料的 CRUD，但也因為這個很棒的特性所以為了我自己的需求只好魔改原本程式變成 [json-server-deformation](https://github.com/KeanuPang/json-server-deformation)。

調整的地方主要是多加了一個 router 設定 `_isReadOnly`，因為我會希望無論是哪種 HTTP Method 都被當成 Get Method 的方式唯讀 db.json 的 JSON 內容；在設定 router 的時候就把這個選項打開即可：

```swift
let routes = require("./routes.json");
server.use(jsonServer.rewriter(routes));
server.use(
  jsonServer.router(`${__dirname}/api.json`, { _isReadOnly: true })
);
```

最後一個需求是如果上來的 request 發現無法處理時，server 可以把自己變成 proxy 角色將 request 過水到後面實際的 legacy endpoint 來處理並回傳結果。以過去的 express 經驗就是找 [http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware) 掛上去就成了。

而如何判斷是無法處理的 request 主要就是如果 request path 不在 server 設定的 routing 集合裡，就視為不能處理直接 pass 給 proxy middleware，大概像這樣：

```swift
const { createProxyMiddleware } = require("http-proxy-middleware");

let proxy = createProxyMiddleware({
  target: "https://legacy.api.dev",
  changeOrigin: true,
});

let routes = require("./routes.json");
server.use((req, res, next) => {
  if (req.path.startsWith("/legacy") && routes[req.path] === undefined) {
    proxy(req, res, next);
  } else {
    next();
  }
});
```

如前所述，基本上平日會遇到的工程需求，在 NodeJS 生態圈裡都可以找到對應的套件，以這個專案為例：

1. Mock data: JSON Server
2. HTTPS: devcert
3. Proxy: http-proxy-middleware

將這些套件加以組合幾乎不用寫到多少程式碼就可以在短時間建立雛型快速驗證功能，沒問題就可以先推到開發環境。最後在開發環境用 [PM2](https://pm2.keymetrics.io/) 管理這些 projects 也很方便不用花太多心力。
