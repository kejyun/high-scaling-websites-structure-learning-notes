# 壓力測試

在我們寫完程式並上了伺服器後，若我們的網站需要服務很多人，我們可能會想要這個服務能夠服務多少人，通常我們會對伺服器進行壓力測試。

壓力測試的軟體有很多，我比較常用的是 `Apache AB test` ，簡單的了解伺服器在多少連線下，處理請求（Request）的速度及效能，而同樣的機器在不同的程式處理之下，會有不同的效能，所以沒辦法統一的斷定哪一種機器只能服務多少人，若程式寫的好的話，可能同時可以應付 4、500 人同使請求，但也有程式可能因為小能不佳，僅能服務 4、50 人（伺服器回應時間標準一樣），所以要對自己的應用服務做壓力測試後才知道狀況。

也有很多其他的壓力測試的軟體，只要找出一套適合自己的壓測工具就可以了。

## 參考資料
* [ab - Apache HTTP server benchmarking tool](http://httpd.apache.org/docs/2.2/programs/ab.html)
* [The Will Will Web | 使用 ApacheBench 進行網站的壓力測試](http://blog.miniasp.com/post/2008/06/30/Using-ApacheBench-ab-to-to-Web-stress-test.aspx)
* [Web server benchmarking - Wikipedia, the free encyclopedia](http://en.wikipedia.org/wiki/Web_server_benchmarking)
