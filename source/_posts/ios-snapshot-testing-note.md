---
title: Snapshot Testing 的調整經驗筆記
date: 2022-03-02
tags: ["ios", "unit-test"]
---

我們的專案已經有 816 snapshot tests 並且有 600 張 snapshots，對專案的 coverage 約 47%，對產品的品質信心算是滿足夠的。

常被抱怨的點是在 CI 上跑 unit test 的時間太久，以專案 800 多個 snapshot tests 來說需要花上 15 mins 才能跑完，有時候開的 PR 一多幾乎都是在等待 unit test 跑結束，反而有點浪費在等待，目前只能採取折衷的作法，在特定的 branch 才啟動 unit test。

另外遇到的問題是不同機器配上對應的模擬器有時候會發生圖片”看起來”是一樣內容，但檔案大小卻差了幾個 bytes，所以程式比對圖片就直接認定 assert failed。原因是畫面在截取的當下可能仍會因為動畫等視覺效果，而造成產生的 snapshot 有些微色彩的差異。

這類問題的解法目前是走 duplicate image detection 的做法，也就是算出 snapshot 對應的 hash 值，然後將兩張圖片的 hash 根據 Hamming distance 來評估是不是一樣的圖片。

本來是在這邊 [GitHub – benhoyt/dhash](https://github.com/benhoyt/dhash) 看到這個，查了一下大概就是這幾種 hash 方法的比較，來源是 [GitHub – ameingast/cocoaimagehashing](https://github.com/ameingast/cocoaimagehashing) 這裡：

| Name  | Performance | Quality   |
| ----- | ----------- | --------- |
| aHash | good        | bad       |
| dHash | excellent   | good      |
| pHash | bad         | excellent |

其實滿推崇 pHash 的方法，看 [別人分享 iOS 上測試的過程](https://medium.com/swlh/ios-snapshot-testing-with-perceptual-hash-16b23aadf6c3) 具有很多優勢；但因為 dHash 實作上相對容易，也滿多實作的作品可以參考。自己參考的實作是這兩篇：

1. [DHash/DHash.swift at master · AndreasVerhoeven/DHash · GitHub](https://github.com/AndreasVerhoeven/DHash/blob/master/Sources/DHash.swift)
2. [hamming-distance-8.swift · GitHub](https://gist.github.com/VojtaStavik/35ad31e37c3a34ee930685f9dbc36bf1)

套用在專案裡做出來的效果算是有滿足需求，但因為還是會有遇到色彩偏差的圖片，所以調整了預設的 `treshold` 為 6，目前整個 816 snapshot tests 跑完在不同機器都不會發生類似的問題。

稍微會影響到 CI 的時間，比之前整個跑完 unit tests 費時多增加了 1min，是還算在忍受範圍裡，起碼不用再人工介入處理跟檢查 CI 上的圖片為什麼又有不同的問題了。
