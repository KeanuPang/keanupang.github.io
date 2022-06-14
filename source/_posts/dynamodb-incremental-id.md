---
title: 建立 DynamoDB 的 Incremental ID 機制
date: 2017-12-12
tags: ["aws", "dynamodb", "webservice"]
---

需要在 DynamoDB 的某個 table 的 Hash Key 是用 auto increment 的方式儲存 id，不過如果只單靠一張 table 有點難完成這樣的機制，所以需要再生另一張 table 用來存放目前的值，讓原本的 table 可以去參考以取得新增後的數值回來儲存。

可以參考這篇：[How to make a UUID in DynamoDB?](https://stackoverflow.com/a/24346125)，做法大概就是像這樣：

> Each time you want to generate a new id, you would do the following:
>
> -   Do a GetItem on NextIdTable to read the current value of Counter → curValue
>
> -   Do a PutItem on NextIdTable to set the value of Counter to curValue + 1. Make this a conditional > PutItem so that it will fail if the value of Counter has changed.
>
> -   If that conditional PutItem failed, it means someone else was doing this at the same time as you were. Start over.
>
> -   If it succeeded, then curValue is your new unique ID.

為了日後的彈性，所以做了一張叫 `NextId` table，裡面的資料格式長這樣：

```json
{
    "NextKey": "user",
    "NextId": 0
}
```

其中 NextKey 是 hash key 這樣未來方便做不同資料類型可以共用 NextId table 來儲存/產生 專屬的數值 NextId。

然後因為專案是用 LoopBack 的架構，所以 NextId model 會做一個 API 讓其它 model 可以一次完成取得最新的 NextId。做法其實就是叫用 DynamoDB 的 Conditional Update：[UpdateItem - Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html)

如果是用 [dynogels](https://github.com/clarkie/dynogels) 這個套件的話，事情會比較簡單，把 expressions 準備好後再傳進去：

```javascript
NextId.update(
    { NextKey: data_type },
    "SET #nextId=:nextId",
    "#nextId = :current",
    { ":nextId": current + 1, ":current": data_current },
    { "#nextId": "NextId" },
    (err, result) => {
        if (err) {
            winston.error("=== Update NextId (%s) failed: %j", data_type, err);
            return done(err);
        }
        return done(null, result);
    }
);
```

然後回到原本的 table，接下來是要把這個 model 在每次新增資料時能夠將新的 nextId 塞入，而 LoopBack 有提供 model 操作時的 [各項 hook 串接](https://loopback.io/doc/en/lb2/Operation-hooks.html)，方便我們可以在儲存資料先做些前處理。

只要在這個 model 裡補上 before save 的這個 hook 定義即可，對應的實作則是呼叫剛剛的 NextId model 以取得新的 nextId 值回來，再修補即將要存入的 model instance，實作如下：

```javascript
const DATA_TYPE = "user";
module.exports = (UserNew) => {
    UserNew.observe("before save", (ctx, next) => {
        if (!ctx.instance) {
            return next();
        }
        let user = ctx.instance;
        UserNew.app.models.NextId.getNextId(DATA_TYPE, (err, result) => {
            if (err) {
                winston.error("=== Calling nextId failed: %j", err);
                return next();
            }
            user.Id = result.get("NextId") + "";
            next();
        });
    });
};
```

最後提一下這個方式的缺點，即是無法保證每次的 nextId 的值是連續性的，因為在每次取得 NextId model 後，後續的儲存主體資料可能是失敗或中斷；雖然如此，但只要能滿足每次的 NextId 值是 unique 這個基本需求我想就足夠應付了。
