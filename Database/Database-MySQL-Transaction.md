# 交易


## 控制InnoDB Transaction

### 關閉自動提交：

```sql
SET autocommit=0;
```

### 開啟自動提交：

```sql
SET autocommit=1;
```

在 MySQL InnoDB 預設所有的資料異動都是 `Transaction（交易）`，當與資料庫做連線的時候，InnoDB 會採用`自動提交（autocommit）`的方式，所以除非使用 `BEGIN;` 及 `COMMIT;` 將異動語法包覆起來***設定為同一個  Transaction，否則任何一個資料異動的語法（INSERT、DELETE、UPDATE）會認為是一個獨立的 Transaction***。

所以資料表每次做資料異動的時候會一直`提交 (COMMIT)` 去更新日誌，若有 1000筆 獨立的SQL要執行就會被 COMMIT 1000 次，所以在下異動語法之前，可以使用指令 `SET autocommit=0;` 關閉自動提交，等異動完成後，再使用指令 `SET autocommit=1;` 開啟自動提交。

```sql
SET autocommit=0;
異動（INSERT、DELETE、UPDATE）大量資料SQL語法
SET autocommit=1;
```

## 參考資料
* [KeJyun學習日誌: MySQL效率調校](http://blog.kejyun.com/2012/12/MySQL-Efficiency-Adjustment.html)
