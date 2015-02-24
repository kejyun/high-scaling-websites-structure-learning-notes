# 程式語法

## 指定使用索引

```sql
SELECT id
FROM data USE INDEX(type)
WHERE type=12345 AND level > 3
ORDER BY id
```

在下SQL語法的時候，有時某些語法使用某些索引執行效率會比較好，可是有時候MySQL沒辦法替我們選擇一個最適合的索引，導致執行的效率很慢（slow query），所以我們必須使用USE INDEX去指定執行效率好的索引，以提升效率。

## 少用JOIN，多用幾次SELECT撈取大量資料

```sql
SELECT user.id , user.name , post.id , post.content
FROM user , post
WHERE user.id = post.user_id ......
```

我們在撈取使用者的文章資訊的的時候，我們可能會用 JOIN 去撈取我們要的資料，這樣很直覺，只是當使用者資料有`10000`筆，而文章有`99999`筆，像這種有大量資料時候的話，使用 JOIN 對於資料存取真的是惡夢，因為 JOIN 過後表示會有 `10000x99999=999990000` 筆資料，然後再從這麼大量的資料中去撈取 WHERE 判斷式中指定的資料，在資料庫伺服器記憶體不夠的時候鐵定會炸掉。

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
$user['user.id'] = array()
```

在撈取文章資料後，必須將撈取的文章指定給該文章的作者（使用者），而我們撈取的使用有撈取「使用者編號（user.id）」，所以可以透過這個資料，將文章存到使用者資料下的文章陣列。

```php
<?php
$user[$post['user_id']]['post'] = $post_array
```

透過分次撈取，本來的 `10000x99999=999990000` 筆資料，就會變成 `10000+99999=109999` 筆資料，記憶體消耗降低極大下，也可以達到同樣的功能

## 參考資料
* [KeJyun學習日誌: 提高存取MySQL效率小技巧](http://blog.kejyun.com/2012/12/Tips-For-Use-MySQL-With-High-Performance.html)
