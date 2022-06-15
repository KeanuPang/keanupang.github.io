---
title: Create a Self-Signed Certificate
date: 2016-04-30
tags: ["Certificate"]
---

在工作上因為每過一陣子就會用到，但又常常忘記步驟所以筆記一下。

先用 opeenssl 指令產生私鑰跟 CSR 檔︰

```
openssl req -x509 -sha256 -days 3652 -newkey rsa:2048 -keyout server.key -out server.csr
```

建立憑證檔案︰

```
openssl x509 -sha256 -days 3652 -in server.csr -signkey server.key -out selfsigned.crt
```

將剛剛的憑證檔案轉成 P12 格式︰

```
openssl pkcs12 -export -name cert_server -in selfsigned.crt -inkey server.key -out keystore.p12
```

最後透過 Java 的 keytool 工具轉成適合 Java 環境使用的 JKS 格式憑證檔案︰

```
keytool -importkeystore -destkeystore keystore.jks -srckeystore keystore.p12 -srcstoretype pkcs12 -alias cert_server
```

完成收工。
