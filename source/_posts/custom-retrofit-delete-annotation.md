---
title: 自定 Retrofit 的 DELETE Annotation
date: 2016-05-04
tags: ["retrofit", "web-service"]
---

一般使用 Jersey 或 RESTEasy 所建立的 JAX-RS Web Service，是允許 client 呼叫 DELETE method 時可以加上 Message Body，就跟 POST method 一樣我們可以取得 Body 裡的內容。

但 Client 如果是使用 Retrofit 來呼叫 Web Service 的 DELETE method 並且加上 Body annotation 夾帶資料過去時，卻會得到如下的訊息而被中止︰

```
Non-body HTTP method cannot contain @Body or @TypedOutput.
```

對 Retrofit 來說是照著 W3C 的標準去實作，於是這樣的行為就跟一般坊間的 http client 不太一樣。

最快的方法就是自定一個可以允許夾帶資料的 DELETE Annotation，讓 Retrofit 可以參照︰

```java
import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import retrofit.http.RestMethod;

@Target(METHOD)
@Retention(RUNTIME)
@RestMethod(value = “DELETE”, hasBody = true)
public @interface DELETE {
 String value();
}
```

然後在自己的 Retrofit Service 使用這個 DELETE annotation 就可以了︰

```java
@DELETE("/remove")
 public OutputJson deleteFormType(@Body FormType form_type);
 ```

搞定收工。
