# 增加 Database Server

在網站成長之後一段時間後，發現資料庫在查詢（SELECT）及資料異動（INSERT、UPDATE、DELETE）之間，因為查詢處理變得太多，使得互相競爭資料庫的資源非常嚴重，進而導致系統回應時間變得很慢。

這時候我們可能會想要把不同的資料表分散在不同的資料庫當中，分散查詢處理的資源，但可能會需要更改程式讀取資料庫的邏輯架構。

***架構圖：***

![增加 Database Server](http://i.imgur.com/zxlXQLx.png)


## 參考資料
* [一步步構建大型網站架構- 架構設計- | 九街| 白開水的博客](http://www.9streets.cn/art-php-489.html)
