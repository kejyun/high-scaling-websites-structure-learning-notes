# 增加更多的 Web Server

在做完分散式資料庫及分散式資料表之後，又開始看到流量暴增的的時候，發現系統的回應時間又開始變慢了，你可能會看到 Web Server（Apache、Nginx...etc）那邊阻塞了很多的請求，而導致每個存取的使用者需要時間等待而導致回應時間增加了，這個時候我們就可能需要增加更多的 Web Server 去應付這些大量的請求。

在做完這些工作之後，會進到一個像是可以無限擴充的階段，網站流量增加時，就是不斷的增加 Server （$$$） 去應付大量的流量。

在做 Web Server 負載平衡時可能會遇到下列問題：

* 基本的負載平衡（Apache 負載平衡、LVS...etc）無法應付巨量的請求量

如果經費允許的話（都流量暴增了，會不想要投入更多的 $ 去讓自己的事業擴大嗎 XD），可以採購負載平衡的硬體設備

> e.g. [F5 Load Balancer](https://f5.com/glossary/load-balancer)、[Netsclar Load Balancer](http://www.citrix.com.tw/products/netscaler-application-delivery-controller/overview.html)...etc

若真的經濟不允許，可以將應用從邏輯上做分類，不同類型的應用分散給不同的負載平衡群集處理。

* 訊息同步的文件分享方法出現瓶頸

這時可以根據不同的網站業務需求去做不同的分散式文件系統

## 參考資料
* [一步步構建大型網站架構- 架構設計- | 九街| 白開水的博客](http://www.9streets.cn/art-php-489.html)
* [F5 Load Balancer](https://f5.com/glossary/load-balancer)
* [Netsclar Load Balancer](http://www.citrix.com.tw/products/netscaler-application-delivery-controller/overview.html)
