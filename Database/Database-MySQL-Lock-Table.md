# 鎖表 (Lock Table)

## 定義

在要更新資料表的資料時，`MySQL`會將表資料鎖定無法讀取，直到資料異動完畢，`MyISAM` 預設支援 `Table-level lock`，而 `InnoDB` 預設支援 `Row-level lock`

### Table-level lock
資料表資料異動時，將「`整個資料表（Table）`」都鎖定住無法讀取

### Row-level lock
資料表資料異動時，將「`要更新的資料列（row）`」都鎖定住無法讀取

## 注意事項

在使用 `Row-level lock` 時必需要 `明確指定要異動資料的主鍵（Primary Key）`，否則將會改用 `Table-level lock` 去做資料表的異動

## 範例

假設有 user 資料表，裡面有 id 與 name 的欄位，id 是主鍵

| SQL	| Table lock | Row lock |	No lock	| 備註
| ------------- |:-------------:|:-------------:|:-------------:|:-------------:|
| SELECT * FROM user WHERE id='1' FOR UPDATE; | v | - | - | 明確指定主鍵，並且有此筆資料，row lock |
| SELECT * FROM user WHERE id='-1' FOR UPDATE; | - | - | v | 明確指定主鍵，若查無此筆資料，無 lock |
| SELECT * FROM user WHERE name='KeJyun' FOR UPDATE; | v | - | - |  無主鍵，table lock |
| SELECT * FROM user WHERE id<>'1' FOR UPDATE; | v | - | - | 主鍵不明確，table lock |
| SELECT * FROM user WHERE id LIKE '3' FOR UPDATE; | v | - | - | 主鍵不明確，table lock |

## 備註

FOR UPDATE 僅適用於 InnoDB，且必須在交易區塊(BEGIN/COMMIT)中才能生效。

## 參考資料
* [KeJyun學習日誌: MySQL鎖表(Lock Table)Table-level與Row-level比較](http://blog.kejyun.com/2012/12/Compare-Tabel-And-Row-Level-Lock-On-MySQL.html)
