# Explain 做 SQL SELECT 語法效能測試

在MySQL我們在使用 SELECT 做撈取資料的時候，有時候常常會效能低落，撈取資料需要很長的時間，有時候是 SQL 語法下得不好導致沒有使用到正確的索引去撈資料，我們這個時候就必須要檢查我們下的 SQL 語法到底有哪些地方需要改善，我建立的 comment 的資料表並新增幾筆假資料去做示範

```sql
-- 建立資料表

-- 留言
CREATE TABLE IF NOT EXISTS `comment` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '編號',
  `content` varchar(50) COLLATE utf8_unicode_ci NOT NULL COMMENT '留言',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB  DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci AUTO_INCREMENT=1 ;

-- 使用者
CREATE TABLE IF NOT EXISTS `user` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '編號',
  `name` varchar(30) COLLATE utf8_unicode_ci NOT NULL COMMENT '姓名',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB  DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci AUTO_INCREMENT=3 ;

-- 使用者的留言
CREATE TABLE IF NOT EXISTS `user_comment` (
  `user_id` int(10) unsigned NOT NULL COMMENT '使用者編號',
  `comment_id` int(10) unsigned NOT NULL COMMENT '評論編號',
  PRIMARY KEY (`user_id`,`comment_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;



-- 新增資料

-- 留言
INSERT INTO `comment` (`id`, `content`) VALUES
(1, '留言1'),
(2, '留言2');

-- 使用者
INSERT INTO `user` (`id`, `name`) VALUES
(1, '使用者1'),
(2, '使用者2');

-- 使用者的留言
INSERT INTO `user_comment` (`user_id`, `comment_id`) VALUES
(1, 1),
(1, 2);




-- 解釋MySQL語法效能

-- 撈取留言資料
EXPLAIN SELECT * FROM  `comment` WHERE  id` =2;

-- 撈取使用者的留言資料
EXPLAIN SELECT *
FROM  `comment` c,  `user` u,  `user_comment` uc
WHERE u.`id` = uc.`user_id`
AND uc.`comment_id` = c.`id`
```

## 解釋 MySQL 語法效能：撈取留言資料

![Explain MySQL SQL](http://3.bp.blogspot.com/-YagV7ODyhPI/UNLeKtvO_uI/AAAAAAAAC8k/v4NrdlhZnCo/s1600/2012-12-20_174153.png)

## 解釋MySQL語法效能：撈取留言資料

![解釋MySQL語法效能：撈取留言資料](http://4.bp.blogspot.com/-7EtC6Ep0cbY/UNLjeYCIEPI/AAAAAAAAC88/chZ1hCB5rrs/s1600/2012-12-20_180223.png)


而EXPLAIN後的資料有下面這些欄位

### select_type
### table

關連到的資料表

### type

使用關聯查詢的類型(效率由好至壞排序)

* System
* const
* eq_ref
* ref
* fulltext
* ref_or_null
* index_merge
* unique_subquery
* index_subquery
* range
* index
* ALL

### possible_keys

可能使用到的索引，從WHERE語法選擇出一個適合的欄位

### key

實際使用到的索引，如果為NULL，則是沒有使用索引

### key_len

使用索引的長度，長度越短 準確性越高

### ref

顯示那一列的索引被使用，一般是一個常數(const)

### rows

MySQL用來返回資料的筆數，可以簡單的把rows視為執行效能，越少越好

### Extra

MySQL用來解析額外的查詢訊息


- Distinct

當MySQL找到相關連的資料時，就不再搜尋。

- Not exists

MySQL優化 LEFT JOIN，一旦找到符合的LEFT JOIN資料後，就不再搜尋。

- Range checked for each Record(index map:#)

無法找到理想的索引。此為最慢的使用索引。

- Using filesort

當出現這個值時，表示此SELECT語法需要優化。因為MySQL必須進行額外的步驟來進行查詢。

- Using index

返回的資料是從索引中資料，而不是從實際的資料中返回，當返回的資料都出現在索引中的資料時就會發生此情況。

- Using temporary

同Using filesort，表示此SELECT語法需要進行優化。此為MySQL必須建立一個暫時的資料表(Table)來儲存結果，此情況會發生在針對不同的資料進行ORDER BY，而不是GROUP BY。

- Using where

使用WHERE語法中的欄位來返回結果。

- System

system資料表，此為const連接類型的特殊情況。

- Const

資料表中的一個記錄的最大值能夠符合這個查詢。因為只有一行，這個值就是常數，因為MySQL會先讀這個值然後把它當做常數。

- eq_ref

MySQL在連接查詢時，會從最前面的資料表，對每一個記錄的聯合，從資料表中讀取一個記錄，在查詢時會使用索引為主鍵或唯一鍵的全部。

- ref

只有在查詢使用了非唯一鍵或主鍵時才會發生。

- range

使用索引返回一個範圍的結果。例如：使用大於>或小於<查詢時發生。

- index

此為針對索引中的資料進行查詢。

- ALL

針對每一筆記錄進行完全掃描，此為最壞的情況，應該盡量避免。

## 結論

MySQL 的 Explain 可以分析大部份的 SQL 語法效能，但有些語法像是 WHERE IN 則會被歸類為 range 的語法，但實際上則是 Using Where，所以確切的語法分析要再看看文件真正的用法去決定

## 參考資料
* [KeJyun學習日誌: 在MySQL使用Explain做SQL SELECT語法效能測試](http://blog.kejyun.com/2012/12/Using-EXPLAIN-SQL-To-Analysis-Efficient-On-MySQL.html)
