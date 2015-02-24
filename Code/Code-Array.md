# 陣列

## 鍵值（key）

在撈取資料庫的資料後，我們會把資料存在陣列中，等之後比對資料時可以當作查詢之用，若可以的話建議陣列的`鍵值（key）`要用可以辨識資料的值。

像是使用者的資料可以用使用者編號當鍵值，若之後要直接取特定使用者的資料，直接就可以指定使用者編號去取得使用者的資料：

```php
<?php

$users[$user_id] = $user_info;
```

像是我們要取所有使用者的留言，留言資料中有使用者編號，不同的留言可能來自同一個使用者，但我們都要取用他的使用者名稱，這樣我們就可以很方便的取得該使用者的資料。

```php
<?php

$messages = [
  [
    'id'      => 1,
    'user_id' => 1,
    'message' => 'Hi'
  ],
  [
    'id'      => 2,
    'user_id' => 2,
    'message' => 'hello'
  ],
  [
    'id'      => 3,
    'user_id' => 1,
    'message' => 'How are you?'
  ]
];

// 取得留言使用者姓名
foreach ($messages as &$message) {
  $message['user_name'] = $users[$message['user_id']]['user_name'];
}
```
