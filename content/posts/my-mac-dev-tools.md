---
title: "My Mac Dev Tools"
date: 2022-08-05T10:10:35+08:00
draft: false
---

_update 2022-09-02: Edge Extension 部份加上 SwissTransfer 服務_

最近正在整理公司電腦的作業環境，先集結成工具清單這樣方便之後換電腦就可以快速 setup 好。做程式開發常用的工具軟體就不列上去了，反正該做什麼工作就會對應到什麼軟體，基本上大家會使用的都是那幾套，所以這邊只記錄我自己偏好使用的工具集。

### Menu Bar

- [Shuttle](https://fitztrev.github.io/shuttle/)

非常老牌的 SSH 快速啟動器，只要一個簡單的設定檔就可以把常連的 ssh 登入資訊建立成清單方便快速點選就可以開 terminal 連上；開發環境通常自己會在遠端再設定好 passwordless 登入。這種小工具其實也不用常常更新只要專做好這個重點功能就很好用了。

- [Input Source Pro](https://inputsource.pro)

在不同 Apps 間切換常會遇到輸入法的干擾，之前看到有人分享這個工具就是解決輸入法的問題，可以為每個 App 設定專門的規則把鍵盤鎖在指定的輸入法，真的是完全解決我們東方人在切換輸入法的困擾。

- [Raycast](https://www.raycast.com/)

用來取代以前在用的 [Alfred](https://www.alfredapp.com/)，對我來說只要能快速開啟要操作的 Apps 這個功能就好。

- [Dato](https://sindresorhus.com/dato)

在角落顯示日期時間等資訊，這很重要。點了日期之後會下拉出 calendar 的資訊，這更重要更方便。所以自然也成為我常用的工具之一。

- [Shottr](https://shottr.cc/)

用來截圖之後可以有基本工具對截圖內容做標註，是目前找到算美觀且簡單方便操作的截圖工具。

- [Magnet](https://magnet.crowdcafe.com/)

在接大螢幕時比較常會將重點的視窗都排列好，所以都是靠這個工具來快速設定。

- [MonitorControl](https://monitorcontrol.app/)

也是在外接螢幕時很方便可以設定顯示亮度跟音量調整，必備的基本重要工具之一。

- [coconutBattery](https://www.coconut-flavour.com/coconutbattery/)

可以在 menu bar 上顯示比較直覺得的電池電量的基本資訊，比原本 MacOS 陽春的電量顯示好用多了。

- [Folder Peek](https://sindresorhus.com/folder-peek)

這是最近才掛上來的工具，主要是可以把工作上常會用到的幾個工作目錄都設定快捷入口，方便在 menu bar 上直接切換過去。

### Desktop Tools

- [SmartGit](https://www.syntevo.com/smartgit/)

Git 工具的唯一首選，我有付費 lifetime 的授權，用了七八年吧我想完全沒有缺點可講。每每看到同事還在為了 merge/rebase 跟檔案 conflict 問題在 Sourcetree 裡胡搞我就內心一把冷汗。

SmartGit 有最直觀簡潔的操作介面，切換 branch、做 merge/rebase、處理 conflict 問題都用很人性化的方式處理，夠穩定夠方便，買 lifetime 絕對是程式開發工作職涯最值得投資的 App 之一。

- [Proxyman](https://proxyman.io/)

不要再聽信同事八股文在用的 Charles 了，介面跟功能都不直覺操作也不便利，完全不是 Proxyman 的對手。

因為好用所以有付費過購買 Proxyman 的個人授權，無論是要 debug HTTP/HTTPS 的內容或做 map remote/local 甚至是 scripting 都沒問題，強大的功能絕對是值得投資的 App 之一。

- [Insomnia](https://insomnia.rest/)

不要再用肥到炸點的 Postman 了，我們不就是要個好用簡單直覺操作的 REST client 嗎。

- [DevCleaner](https://github.com/vashpan/xcode-dev-cleaner)

因為 Xcode 的濫觴所以時常要清理一些垃圾檔案以節省硬碟空間，相信有在靠 Xcode 開發工作的應該都會必備這套小工具。

- [OpenInTerminal](https://github.com/Ji4n1ng/OpenInTerminal)

主要就是在 Finder 裡可以有個 icon 直接開 terminal app 然後切到當前目錄下。平常存在感很低，但臨時需要的時候就幫的上大忙，是個不可或缺的小工具之一。

- [NetNewsWire](https://netnewswire.com)

因為工作 context switch 切換太累了，所以可以摸魚一下逛逛訂閱的 RSS 內容。

### Edge Extensions

- [AdGuard](https://microsoftedge.microsoft.com/addons/detail/adguard-%E5%BB%A3%E5%91%8A%E5%B0%81%E9%8E%96%E5%99%A8/pdffkfellgipmhklpdmokmckkkfcopbh)

擋網頁裡的一堆煩人廣告，目前應該是最乾淨比較沒有問題的廣告封鎖器吧。必裝工具。

- [Decentraleyes](https://microsoftedge.microsoft.com/addons/detail/decentraleyes/lmijmgnfconjockjeepmlmkkibfgjmla)

保護自己不被 CDN 追蹤。免費的 CDN 太多了，還是要保護一下自己才安全。

- [Ghostery](https://microsoftedge.microsoft.com/addons/detail/ghostery-%E2%80%93-%E9%9A%B1%E7%A7%81%E5%BB%A3%E5%91%8A%E6%94%94%E6%88%AA%E5%99%A8/fclbdkbhjlgkbpfldjodgjncejkkjcme)

保護自己的隱私，不被網頁內容給追蹤。必裝工具。

- [HTTPS Everywhere](https://microsoftedge.microsoft.com/addons/detail/https-everywhere/fchjpkplmbeeeaaogdbhjbgbknjobohb)

將 HTTP 自動切換至 HTTPS，不過現在大多數的瀏覽器應該都進步很多了，理論上會動用到這個工具的機率應該會越來越少。

- [Popup View for Google Translate](https://microsoftedge.microsoft.com/addons/detail/popup-view-for-google%E2%84%A2-tr/cpogidebfcfffnbjlmoknfpemngaijdj)

就英翻中的 google 翻譯功能，應該是畢生找到最簡單方便的翻譯小工具吧，其它找到的不是包山包海要不就是操作太繁瑣。

- [PrettyJSON](https://microsoftedge.microsoft.com/addons/detail/prettyjson/jilmabbdmkbakhjganilpihpakkielnl)

就把 JSON 內容弄漂亮一點美觀一點的小工具，必裝。

- [Privacy Badger](https://microsoftedge.microsoft.com/addons/detail/privacy-badger/mkejgcgkdlddbggjhhflekkondicpnop)

一樣是保護自己隱私不被網頁內容追蹤的小工具，但已經有一段時間沒在更新了，列入觀察名單中。

- [Reopen closed tab Button](https://microsoftedge.microsoft.com/addons/detail/reopen-closed-tab-button%E2%84%A2/ffikmgfojmfconjpbcflembkiclgfgeo)

很重要的工具，不小心把網頁 tab 關掉的時候可以一鍵救回來，多按幾次還可以再救更多 tab，必裝工具。

- [Matter](https://chrome.google.com/webstore/detail/matter/knjbgabkeojmfdhindppcmhhfiembkeb)

對我來說就是看到長文內容的網頁，一律按下 Matter 的 Save 存起來，等未來有緣之時再叫出來慢慢讀就是。

所以我覺得要是有人開發出 TLDR 的工具就更棒了，這樣以後看到長文內容網頁就可以一鍵按下生出三五句的結論摘要好像很棒。到時候就可以把 Matter 丟掉了。

- [Minimal Theme for Twitter](https://chrome.google.com/webstore/detail/minimal-theme-for-twitter/pobhoodpcipjmedfenaigbeloiidbflp)

單純就是不喜歡 Twitter 的介面，所以靠這個小工具美化一下，看推的時候心情就好點了。

- [Modern for Wikipedia](https://chrome.google.com/webstore/detail/modern-for-wikipedia/emdkdnnopdnajipoapepbeeiemahbjcn)

也是因為不喜歡 Wikipedia 亂亂的內容長相，所以靠這個小工具美化一下。

- [uBlacklist](https://chrome.google.com/webstore/detail/ublacklist/pncfbmialoiaghdehhbnbhkkgmjanfhe)

這很重要的小工具，在 google 查詢結果可以把看起來討厭或可疑的網站直接點掉 block 起來。這在對付中國一堆內容流量詐騙網站很有用，然後台灣的 Pixnet 我也是一律 block。

- [SwissTransfer](https://chrome.google.com/webstore/detail/swisstransfer-by-infomani/fgnamjpfdanadgjchlokpiclkdgnnkdj)

還不錯的網站服務，不用登入或註冊直接上傳大檔案並分享給他人。以前有知道 WeTransfer 這個服務，但 SwissTransfer 的上限是 50G 單檔而且介面比較簡易操作，所以就加入最愛了。
