---
title: Connect-Redis 套件更新 Session 過期時間的方式
date: 2016-11-14
tags: ["redis", "web-service", "nodejs"]
---

在使用 Express 架設網站時，且因為 cluster 的考量，會將 Session cookie 的資訊往外拉，通常都是使用 Redis 來儲存使用者的 Session。在 Express 裡會透過 connect-redis 的套件來串接兩者，以及使用者登入的檢查就使用 passport 套件來幫我們完成。

在 Express 裡的設定結構大概會是這樣︰

```javascript
let sessionMiddleware = session({
  store: new RedisStore({
  host: hostname,
  port: port,
  db: db * 1,
  ttl: 3600
  }),
  resave: false,
  saveUninitialized: false
});
app.middleware(‘session’, sessionMiddleware);
```

這時候的困難點會是當未登入的使用者可能需要 session cookie 的 maxAge 會比較短期，例如需要是處在 recaptcha 的驗證階段；直到使用者順利登入後，才需要更新 session cookie 的 maxAge 為合理的時間。也就是 Redis 的 TTL 設定是動態的方式調整。

參考這裡的 [討論](https://github.com/tj/connect-redis/issues/92) 的做法則會是固定將 TTL 設為 0，並在使用者登入後才更新 maxAge。所以 RedisStore 的設定將會是︰

```javascript
store: new RedisStore({
  host: hostname,
  port: port,
  db: db * 1,
  ttl: 0
})
```

接者程式只要控制 request 的 session.cookie 物件，將 maxAge 時間設定上去即可，Redis 就會反應出真實的 TTL 狀況了︰

```javascript
request.session.cookie.maxAge = COOKIE_CAPTCHA_MAX_AGE;
```

以上報告完畢。
