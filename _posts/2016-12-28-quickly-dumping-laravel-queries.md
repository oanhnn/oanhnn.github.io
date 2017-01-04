---
title:    Liệt kê nhanh các câu truy vấn trong Laravel
layout:   post
category: tutorial
tags:     [ php, skill, laravel ]
feature:  ~
---

Khi bạn phát triển một ứng dụng với Laravel (hoặc với Eloquent), rất có thể bạn sẽ gặp một vài vấn đề.
Đó có thể là bạn lấy một bản ghi rồi lấy các bản ghi khác dựa theo kết quả lấy được, ...
Hoặc là khi bạn đang sử dụng các truy vấn phức tạp và bạn muốn xem nó đã thực hiện truy vấn như nào đến cơ sở dữ liệu.
Để làm được điều đó thì bạn có thể sử dụng [Laravel Debugbar](https://laravel-news.com/laravel-debugbar/)
hoặc [Laravel database profiler](https://github.com/dmitry-ivanov/laravel-db-profiler) ở môi truờng phát triển của mình.
Tuy nhiên có 1 cách đơn giản mà không cần cài thêm thư viên gì cả.

<!--more-->

Đầu tiên bạn thêm đoạn code sau vào method `AppServiceProvider::boot()` của mình.

```php
<?php

namespace App\Providers;

use DB;
use Event;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
      // Add this block to dump queries
      // ============= Begin ==========
      if (env('APP_ENV') === 'local') {
        DB::connection()->enableQueryLog();
      }
      if (env('APP_ENV') === 'local') {
        Event::listen('kernel.handled', function ($request, $response) {
          if ( $request->has('sql-debug') ) {
            $queries = DB::getQueryLog();
            dd($queries);
          }
        });
      }
      // ============== End ===========
    }

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        //
    }
}
```

Bây giờ, trong lúc bạn code, bạn chỉ cần thêm `?sql-debug=1` là có thể
xem toàn bộ các câu truy vấn được thực hiện tại màn hình hiện tại.

![Liệt kê toàn bộ câu truy vấn được thực hiện tại màn hình hiện tại](/assets/img/dump-queries.png)*Liệt kê
toàn bộ câu truy vấn được thực hiện tại màn hình hiện tại*

Chúc các bạn thỏa mái hơn khi code!
