---
title:    Liệt kê nhanh các câu truy vấn SQL trong Flarum
layout:   post
category: tutorial
tags:     [ php, skill, laravel ]
feature:  /assets/img/flarum-preview.jpg
---

Vừa qua mình có code một dự án với thằng [Flarum][flarum]. Phải công nhận là thằng này
có nhiều cái hay để mình học hỏi nhưng cũng phải nói là nó vần còn nhiều cái tù
(vì cũng còn đang là beta-6 mà).

Trong quá trình phát triển, mình cần phải debug với câu truy vấn SQL của nó.
Lúc đầu mình cũng lúng túng, chưa biết làm như nào.
<!--more-->
Sau khi tìm hiểu thì mình thấy nó cũng dùng **Eloquent** - bộ thư viện database của Laravel.

```
// class Flarum\Foundation\Application, method registerCoreContainerAliases
        'app'                  => ['Flarum\Foundation\Application', 'Illuminate\Contracts\Container\Container', 'Illuminate\Contracts\Foundation\Application'],
        'blade.compiler'       => 'Illuminate\View\Compilers\BladeCompiler',
        'cache'                => ['Illuminate\Cache\CacheManager', 'Illuminate\Contracts\Cache\Factory'],
        'cache.store'          => ['Illuminate\Cache\Repository', 'Illuminate\Contracts\Cache\Repository'],
        'config'               => ['Illuminate\Config\Repository', 'Illuminate\Contracts\Config\Repository'],
        'db'                   => 'Illuminate\Database\DatabaseManager',
        'events'               => ['Illuminate\Events\Dispatcher', 'Illuminate\Contracts\Events\Dispatcher'],
```

Sử dụng kiến thức [liệt kê nhanh các câu truy vấn SQL trong Laravel][prev-post] mà
bài trước mình đã chia sẻ, mình đã giải quyết được vấn đề này như sau:

### Bước 1: Tạo một extension

Đầu tiên bạn cho phép composer sử dụng local package ở trong thư mục `extensions`.
Mình sử dụng repository type path bằng cách thêm đoạn code sau vào file `composer.json` của project.
Bạn tham khảo ở [đây](https://getcomposer.org/doc/05-repositories.md#hosting-your-own)

```
{
  // ...
  "repositories": [
    {
      "type": "path",
      "url": "extensions/*/"
    }
  ],
  // ...
}
```
Sau đó mình tạo extension

```
$ mkdir -p extensions/flarum-ext-log-sql-queries
$ touch extensions/flarum-ext-log-sql-queries/{bootstrap.php,composer.json}
```
Nội dung của 2 file `bootstrap.php` và `composer.json` như sau:

```
<?php
// bootstrap.php
return function () {
  if (app('app')->inDebugMode()) {
    app('db')->connection()->listen(function ($query, $bindings) {
      app('log')->debug(sprintf("%s\n%s\n", $query, var_export($bindings, true)));
    });
  }
}
```
Nội dung file `composer.json`:

```
{
  "name": "oanhnn/flarum-ext-log-sql-queries",
  "description": "Quickly log sql queries",
  "type": "flarum-extension",
  "require": {
    "flarum/core": "^0.1.0-beta.6"
  },
  "extra": {
    "flarum-extension": {
      "title": "Quickly log sql queries",
      "icon": {
        "name": "file-text-o",
        "backgroundColor": "#D13E32",
        "color": "#fdb1b6"
      }
    }
  }
}
```

### Bước 2: Cài đặt extension và xem các câu SQL

Cài đặt extension.

```
$ composer require "oanhnn/flarum-ext-log-sql-queries":"*@dev" --dev
```

Sau đó vào trang admin và bật extension đó lên.

```
$ tail -f /path/to/project/storage/logs/flarum.log
[2017-01-19 04:20:44] production.DEBUG: select `groups`.*, `users_groups`.`user_id` as `pivot_user_id`, `users_groups`.`group_id` as `pivot_group_id` from `groups` inner join `users_groups` on `groups`.`id` = `users_groups`.`group_id` where `users_groups`.`user_id` = ?
array (
  0 => 2,
)

[2017-01-19 04:20:44] production.DEBUG: select * from `pages` where `is_hidden` = ? order by `time` desc limit 21
array (
  0 => 0,
)
```

Chúc các bạn thỏa mái hơn khi code!

[flarum]:     http://flarum.org/
[prev-post]:  /2016-12-28/quickly-dumping-laravel-queries.html
