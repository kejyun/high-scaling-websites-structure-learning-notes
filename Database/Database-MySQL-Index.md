# 索引

## 自定義Hash Index做字串完整比對

我們知道在對`字串（CHAR或VARCHAR）`去做查找的時候效率會遠比對`整數（INT）`查找還慢，因CRC32對字串做校驗後會回傳`整數的校驗碼`，我們在資料表增加一個整數型態欄位，儲存要比對字串的校驗碼。

### crc32

> 建立str 的 32 位迴圈冗余校驗碼多項式。這通常用於檢查傳輸的資料是否完整。

> 由於 PHP 的整數是帶符號的，許多 crc32 校驗碼將返回負整數，因此你需要使用 sprintf() 或 printf() 的「%u」格式符來取得表示無符號 crc32 校驗碼的字串。
 在原本實作email登入時會對email欄位做索引，所以會先去查找email字串欄位的資料，之後再去比對密碼是否正確，但若資料過多字串比對的效率會降低很多

```sql
SELECT id,name,email
FROM user
WHERE email = "kejyun@gmail.com"
AND password = "xxx"
```

我們加入了 `emailcrc` 的欄位去儲存對 email 字串的校驗碼，再查找 email 字串欄位的資料前，先透過crc整數校驗碼快速過濾掉不可能的資料，之後再從少數的資料中做 email 字串欄位字串比對，如果資料量很大，這樣的效率會提升很多。

```sql
SELECT id,name,email
FROM user
WHERE emailcrc = CRC32("kejyun@gmail.com")
AND email="kejyun@gmail.com"
AND password="xxx"
```

這邊要注意的是沒辦法只使用 crc 校驗碼去當作唯一的條件，`不同的字串可能會出現相同的校驗碼`，所以最後還是要對你要比對的字串做比對，避免查詢發生錯誤。

|email  | emailcrc  |
| ------------- |:-------------:|
|kejyun1@gmail.com  |	1234567890  |
|kejyun2@gmail.com  | 1234567890  |


## 參考資料
* [KeJyun學習日誌: 提高存取MySQL效率小技巧](http://blog.kejyun.com/2012/12/Tips-For-Use-MySQL-With-High-Performance.html)
* [PHP手冊 - crc32](http://por.tw/Website_Design/PHP5/function.crc32.html)
