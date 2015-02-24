# 索引

## 索引順序

在所有的資料庫內，建立索引是提昇資料庫資料存取效率的很重要的方式，但是錯誤的`索引順序`或是 `SQL 語法` 都可能讓查詢語法變成了 slow query。

假設我們有一個部落格文章的資料表，裡頭存放所有使用者的部落格文章，資料表資料如下：

### posts

|post_id  | post_status   | author | content  |
| ------------- |:-------------:|:-------------:|:-------------:|
| 1  |	正常  | kejyun | 文章 1 |
| 2  |	刪除  | kejyun | 文章 2 |
| 3  |	正常  | kejyun | 文章 3 |
| 4  |	正常  | jimmy | 文章 4 |
| 5  |	刪除  | jimmy | 文章 5 |

如果我們要撈取 `作者為 kejyun` 且 `狀態正常` 的文章，我們可能會用下列語法去撈取

### SQL 語法 1

```sql
SELECT *
FROM posts
WHERE post_status = '正常'
AND author = 'kejyun'
```

或者用這個語法撈取

### SQL 語法 2

```sql
SELECT *
FROM posts
WHERE author = 'kejyun'
AND post_status = '正常'
```

這兩句 SQL 語法都可以撈出我們想要的結果，但是對於不同的索引執行的效率卻是差異很多

如果我們的索引是 `post_status`、`author` 的順序，`SQL 語法 1` 則會正確的使用索引來做查詢，執行的效率會很快。

但是對於`SQL 語法 2`，會因為找不到適合該語法的索引去做查詢，所以會變成不使用索引，而對整個資料表作完整的檢索，去查找出資料來。

#### 主要原因是

> 在 SQL 語法 WHERE 的順序會影響查詢索引的順序

> 同樣的索引資料在不同的順序，表示為不同的索引，所以 `post_status、author` 與 `author、post_status` 這兩個索引雖然是使用相同的資料欄位，但因為順序不同所帶來的查詢效果也不同

> 同一句 SQL 語法只能使用單一個索引，所以不同的索引沒辦法共用

資料庫沒有這麼人工智慧，會去判斷兩個 SQL 語法可以使用相同的索引去查找出相同的資料，他會依照程式設計師給的語法，依序去查找是否有可用的索引。

#### 分析 SQL 語法 1

在 `SQL 語法 1` 中，我們 WHERE 的第 1 個條件是 `post_status = '正常'`，所以資料庫會去查找有沒有索引開頭是使用 `post_status` 的索引。

若有該索引，則再判斷 WHERE 的第 2 個條件 `author = 'kejyun'`，所以資料庫會去查找有沒有索引開頭是使用 `post_status` 的索引，且該索引的第 2 個索引是使用 `author` 的索引。

資料庫會一直判斷比較 SQL 語法 WHERE 條件與索引之間的順序關係，直到沒辦法匹配後，後面的沒辦法匹配索引則使用完整比對的方式去進行查詢

#### 分析 SQL 語法 2

在 `SQL 語法 2` 中，我們 WHERE 的第 1 個條件是 `author = 'kejyun'` 但我們資料庫沒有建立索引開頭是使用 `author` 的索引，所以只能對資料庫使用完整比對。

### 原理

索引就像是書籍的目錄一樣，若以童話故事書為例，我們書中會收錄世界各國的故事，且每個故事有他自己的類型，像是奇幻、驚悚、傳說、神話。

如果我們以國家當做大標題（e.g. 第 1 索引），以故事類型當作小標題（e.g. 第 2 索引），那麼書籍目錄會像：

  - 台灣
    - 奇幻
    - 驚悚
    - 傳說
    - 神話
  - 日本
    - 奇幻
    - 驚悚
    - 傳說
    - 神話
  - 歐美
    - 奇幻
    - 驚悚
    - 傳說
    - 神話
  - 印度
    - 奇幻
    - 驚悚
    - 傳說
    - 神話
  - etc...

如果我們要找`台灣`且類型為`傳說`的童話故事，我們的目光會先移動到台灣的區段，然後在這個區塊下找到類型為`傳說`的故事

- 台灣
  - 奇幻
  - 驚悚
  - ***傳說***
  - 神話


但是如果我們要找所有類型為`傳說`的故事中，發生在`台灣`的故事，依照步驟我們會希望先把所有`傳說`的故事先列出來，再從這個目錄下去找屬於`台灣`的故事，所以我們希望會看到像這樣的目錄（索引）：

  - 傳說
    - ***台灣***
    - 日本
    - 歐美
    - 印度

但是在這本故事書的目錄（索引）中，我們沒有看到這樣的目錄結構，所以我們沒辦法透過目錄快速的找到我們要看的所有類型為`傳說`的章節資料，只好從頭到尾的去翻閱整本書，直到找到全部我們`傳說`章節的故事，然後再從這些找出來的`傳說`故事中，再去區別出哪些為`台灣`的故事。

所以目錄（索引）的規則就是希望看故事書的人要怎麼快速找到他想要的東西，當沒有我們可以參考的目錄的話，就像資料庫沒有可參考的索引一樣，就會找得比較慢（但還是找得出來）。


### 結論

因為索引順序的不同，以及 SQL WHERE 條件順序的不同，會使得資料庫在使用索引進行查詢有不同的效率，所以要謹慎的使用索引及 SQL 語法，才能達到高效率的查詢結果。


## 控制索引更新


### 關閉索引更新：

```sql
ALTER TABLE table_name DISABLE KEYS;
```
### 開啟索引更新：

```sql
ALTER TABLE table_name ENABLE KEYS;
```

MySQL在新增（INSERT）、刪除（DELETE）、更新（UPDATE）的時候會去更新現有的索引表，而更新索引表也需要花費一些時間，當異動一筆資料的時候，索引表也做一次的異動，但當在做大量資料異動的時候，例如異動1000筆資料，索引表也需要異動1000次，而其實我們只需要最後一次（最新）的異動就好了，前面的999次都是不需要做的索引表異動更新，所以在異動大量資料前，可以使用指令 `ALTER TABLE table_name DISABLE KEYS;` 關閉索引更新，等異動完成後，再使用指令 `ALTER TABLE table_name ENABLE KEYS;` 開啟索引更新。

```sql
ALTER TABLE users DISABLE KEYS;
異動（INSERT、DELETE、UPDATE）大量資料SQL語法
ALTER TABLE users ENABLE KEYS;
```

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
* [KeJyun學習日誌: MySQL效率調校](http://blog.kejyun.com/2012/12/MySQL-Efficiency-Adjustment.html)
* [MySQL Indexing: Best Practices Slide PDF](http://www.percona.com/files/presentations/WEBINAR-MySQL-Indexing-Best-Practices.pdf)
* [Tools and Techniques for Index Design PDF Slide](http://www.percona.com/files/presentations/WEBINAR-tools-and-techniques-for-index-design.pdf)
* [EXPLAIN Demystified PDF slide](http://www.percona.com/files/presentations/WEBINAR2012-02-Explain-Demystified.pdf)
* [Optimizing MySQL Configuration PDF Slide](http://www.percona.com/files/presentations/percona-live/london-2012/PLUK2012-optimizing-mysql-configuration.pdf)
* [PHP手冊 - crc32](http://por.tw/Website_Design/PHP5/function.crc32.html)
