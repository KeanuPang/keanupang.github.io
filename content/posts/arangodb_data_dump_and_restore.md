---
title: "ArangoDB 的資料檔備份與還原"
date: 2021-12-09
draft: false
tags: ["arrangodb"]
---

之前在測試 ArangoDB 操作特別註記下來的小筆記。

### 建立 dump

```
 unix> arangodump --server.endpoint tcp://<host>:<port> --server.database <database> --include-system-collections --collection _graphs --collection imdb_vertices --collection imdb_edges --compress-output false --envelope true
```

### 還原資料

準備好之前 dump 的資料，目前名為 `dump`，再執行如下指令，預設會找當前目錄下的 `dump` 目錄：

```
 unix> arangorestore --server.endpoint tcp://<host>:<port> --server.database IMDB --create-database --include-system-collections
```
