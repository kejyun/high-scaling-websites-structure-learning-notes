# 程式語法

## 指定使用索引

```sql
SELECT id
FROM data USE INDEX(type)
WHERE type=12345 AND level > 3
ORDER BY id
```

在下SQL語法的時候，有時某些語法使用某些索引執行效率會比較好，可是有時候MySQL沒辦法替我們選擇一個最適合的索引，導致執行的效率很慢（slow query），所以我們必須使用USE INDEX去指定執行效率好的索引，以提升效率。

## 少用 JOIN，多用幾次 SELECT 撈取大量資料

```sql
SELECT user.id , user.name , post.id , post.content
FROM user , post
WHERE user.id = post.user_id ......
```

我們在撈取使用者的文章資訊的時候，我們可能會用 JOIN 去撈取我們要的資料，這樣很直覺，只是當使用者資料有`10000`筆，而文章有`99999`筆，像這種有大量資料時候的話，使用 JOIN 對於資料存取真的是惡夢，因為 JOIN 過後表示會有 `10000x99999=999990000` 筆資料，然後再從這麼大量的資料中去撈取 WHERE 判斷式中指定的資料，在資料庫伺服器記憶體不夠的時候鐵定會炸掉。

解決方式是我們可以分批撈取使用者的資料，以及文章的資料

```sql
SELECT user.id , user.name
FROM user
```

```sql
SELECT id , content , user_id
FROM post
```

在使用者資料資料撈取出來之後，使用迴圈將使用者存成陣列，但是「陣列的索引」是使用可以識別的「使用者編號（user.id）」當作索引值。

```php
<?php
$user['user.id'] = array();
```

在撈取文章資料後，必須將撈取的文章指定給該文章的作者（使用者），而我們撈取的使用有撈取「使用者編號（user.id）」，所以可以透過這個資料，將文章存到使用者資料下的文章陣列。

```php
<?php
$user[$post['user_id']]['post'] = $post_array;
```

透過分次撈取，本來的 `10000x99999=999990000` 筆資料，就會變成 `10000+99999=109999` 筆資料，記憶體消耗降低極大下，也可以達到同樣的效果

## 更新或刪除大量範圍的資料，請用主鍵去更新

我們可能會想要一次異動大範圍的資料，若以社群網站的`通知（Notification）`來說，我們希望在使用者點選通知列表後，將所有的通知從未讀狀態變更為已讀，我們可以很容易地想到用這樣的 SQL 語法去進行更新：

```sql
UPDATE `notification` SET status="已讀" WHERE created_at < '現在時間';
```

若我們要刪除大量的資料時，我們也可能用這樣的 SQL 語法去刪除資料：

```sql
DELETE FROM `backup` WHERE created_at < '現在時間';
```

但是在這樣的條件為範圍的語法，資料庫會需要一筆一筆的去比對資料是否為設定條件的範圍，若為`交易`的資料，資料庫會將資料進行鎖定禁止讀取，有可能鎖定一些非我們要處理的資料，若處理時間過久，可能會導致撈取資料的反應時間過久。

所以通常為了避免`鎖定不必要資料`的情況發生，我們可以試著先把資料撈取出來，再透過`明確的主鍵`去指定哪些異動的資料需要被鎖定，而不影響其他的資料。

### 更新資料（較佳作法）

```sql
-- 先撈取資料
SELECT `notification` WHERE status="未讀" AND created_at < '現在時間';

-- 指定要更新資料的主鍵
UPDATE `notification` SET status="已讀" WHERE  id IN (1, 2, 3);
```

### 刪除資料（較佳作法）

```sql
-- 先撈取資料
SELECT `backup` WHERE created_at < '現在時間';

-- 指定要刪除資料的主鍵
DELETE FROM `backup` WHERE id IN (1, 2, 3);
```

在服務會有大量的人撈取資料的時候，這樣可以避免不必要的資料鎖定，也可以讓存取速度加快喔～

## 參考資料
* [KeJyun學習日誌: 提高存取MySQL效率小技巧](http://blog.kejyun.com/2012/12/Tips-For-Use-MySQL-With-High-Performance.html)
