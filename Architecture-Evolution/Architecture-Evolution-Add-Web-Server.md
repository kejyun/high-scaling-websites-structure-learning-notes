# 增加 Web Server

隨著網站應用使用者增加，我們會發現 Web Server 機器的壓力變得比較高了，這個時候就會開始考慮增加 Web Server 了。增加 Web Server 除了可以降低機器的壓力外，同時也可以在 Web Server 掛掉後，有備援機器可以使用，避免整個服務完全中斷。

在增加 Web Server 之後，會碰到一些問題：

* 如何將流量平均分配到不同的 Web Server

> 通常我們會使用 Apache 的負載平衡方法，或是 LVS 相關的負載平衡方法去分配這些流量

* 如何讓 Web Server 的資料同步

> 像是使用者的 session 在不同機器要如何同步，可能會考慮使用資料庫、cookie 或同步 Session 的機制

* 快取（cache）資料同步

> 像是快取會員的個人資料，可能會使用快取同步的機制，或者是分散式快取的方式

* 上傳的檔案同步

> 可能會使用檔案共享的方式

***架構圖：***

![增加 Web Server](http://i.imgur.com/RA6cJgk.png)


## 參考資料
* [一步步構建大型網站架構- 架構設計- | 九街| 白開水的博客](http://www.9streets.cn/art-php-489.html)
