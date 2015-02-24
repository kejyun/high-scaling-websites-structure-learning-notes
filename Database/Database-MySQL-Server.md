# 伺服器

## 設定


### max_allowed_packet：允許最大封包

在INSERT或UPDATE的時候，因為封包資料過大，導致錯誤發生

### max_connect_errors：最大連線錯誤

設 4294967295，可以避免當 client (像是 php) 發生太多錯誤時被 block 住。
