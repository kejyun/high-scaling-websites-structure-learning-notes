# 伺服器

## 設定


### max_allowed_packet：允許最大封包

在INSERT或UPDATE的時候，因為封包資料過大，導致錯誤發生

### max_connect_errors：最大連線錯誤

設 4294967295，可以避免當 client (像是 php) 發生太多錯誤時被 block 住。

> default 0，請設定一個最大的值即可，32位元的系統 最大是 4bytes 4294967295、64位元的系統最大是 18446744073709547520

## 參考資料
* [note: mysql 跟效能有關的設定 @ mini box 迷你小盒子 :: nidBox親子盒子](http://mini.nidbox.com/diary/read/7451092)
