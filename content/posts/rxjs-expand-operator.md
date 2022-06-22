---
title: 在 Observable 裡做 recursive 操作
date: 2017-08-31
tags: ["rxjs"]
---

請參考 [expand operator](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html#instance-method-expand) 的說明：

```
Recursively projects each source value to an Observable which is merged in the output Observable.
```

在 expand operator 裡主要的判斷是如果離開這個 recursion 就回傳 `Rx.Observable.empty()`；反之就把需要 recursive 的值修改後再回傳 Observable 物件，於是你的 Observable 物件就可以做類似 loop 的叫用串接了。

小範例如下，是用 AVA 做 test runner：

```javascript
const Rx = require('rxjs');
const winston = require('winston');
const test = require('ava');

const fetchObs = (count) => {
  return Rx.Observable.of({value: count, count: count*2});
};

test('test expand', (t) => {
  return fetchObs(1)
    .expand((data) => {
      if (5 <= data.value) {
        return Rx.Observable.empty();
      }
      return fetchObs(data.value+1);
    }).map((result) => {
      winston.info('=== result: %j', result);
      t.pass();
    });
});
```

執行 test case 後預期會得到如下的輸出結果：

```
info: === result: {"value":1,"count":2}
info: === result: {"value":2,"count":4}
info: === result: {"value":3,"count":6}
info: === result: {"value":4,"count":8}
info: === result: {"value":5,"count":10}
  ✔ test expand

  1 test passed [18:11:47]
```

今日任務完成。

