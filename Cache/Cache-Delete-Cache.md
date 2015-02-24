# 刪除快取

我們設定了快取希望使用者存取資料的時候能夠加快回應時間，但資料可能會因為使用者的`刪除`或`修改`而產生了異動，若我們需要讓使用者能夠讀取到最新的資料，我們通常會手動的將快取刪除，讓使用者下次讀取的話能夠去資料庫讀取到最新的資料，在重新設定一次新的快取資料。

## 以部落格文章快取為例

### 更新部落格文章資料

```php
<?php

// 快取 key 依照不同的文章編號，可以是 blog_post:1 或 blog_post:2
$cache_key = 'blog_post:' . $post_id;

$results = DB::update('UPDATE `posts` SET `content`= ? WHERE `post_id` = ?', [$new_content, $post_id]);

if ($result) {
  // 選擇1: 直接設定文章快取，讓下一位使用者再次存取相同文章時可直接取用快取
  Cache::put($cache_key, $new_content, $cache_minutes);

  // 選擇2: 刪除快取，等下一位使用者讀取文章時在去設定快取
  Cache::forget($cache_key);
}

return $result;
```

### 刪除部落格文章資料

```php
<?php

// 快取 key 依照不同的文章編號，可以是 blog_post:1 或 blog_post:2
$cache_key = 'blog_post:' . $post_id;

$results = DB::delete('DELETE FROM `posts` WHERE `post_id` = ?', [$post_id]);

if ($result) {
  // 刪除快取，讓下一位使用者不要再讀取已被刪除的文章
  Cache::forget($cache_key);
}

return $result;
```
