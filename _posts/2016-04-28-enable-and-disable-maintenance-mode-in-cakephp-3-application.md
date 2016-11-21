---
title:    Bật tắt chế độ bảo trì trong CakePHP3
layout:   post
category: tutorial-tips
tags:     [cakephp, php]
feature:  /assets/img/cakephp-maintenance.png
---

Mình đã làm việc với cả hai frameworks là Laravel 5.x và CakePHP 3.x (trong bài này
mình không so sánh hai frameworks đó). Mình thấy trong Laravel 5.x có một tính năng
rất hay là có thể bật và tắt chế độ bảo trì bằng CLI, đó là `artisan down` và `artisan up`.
Trong CakePHP 3.x mình không thấy có tính năng này và mình bắt đầu tìm hiểu để
tích hợp thêm tính năng này cho CakePHP.

<!--more-->

## Requirements

Đầu tiên mình đặt ra yêu cầu:

1. Cho phép bặt và tắt chế độ bảo trì bằng CLI, thông qua `bin/cake`
2. Khi bặt chế độ bảo trì, mọi request đến sẽ nhận được một trang cảnh báo là hệ thống đang bảo trì, vui lòng quay lại sau
3. Có thể tùy chỉnh trang cảnh bảo.

## First idea

Đầu tiên mình nghĩ là nên tạo một action là maintenance rồi forward tất cả các requests
về action này khi bật một cái cờ. Cách này có vẻ đơn giản khi mình viết trong `AppController` như sau:

```php
<?php
namespace App\Controller;

use Cake\Controller\Controller;
use Cake\Event\Event;

class AppController extends Controller
{
    // ....
    public function beforeFilter(Event $event)
    {
        if ($this->enabledMaintenanceMode()) {
            $this->setAction('maintenance');
        }
    }

    public function maintenance()
    {
        // TODO: render alert page
    }

    /**
     * @return boolean
     */
    protected function enabledMaintenanceMode()
    {
        // TODO: check maintenance mode is enabled
    }
    // ...
}
```

Tuy đơn giản nhưng mình thấy nó còn có nhiều hạn chế, đặc biệt nó vẫn phải chạy
sau nhiều xử lý khác hoặc có thể bị AuthComponent chặn, bắt login, ...

## Find better solution

Mình tiếp tục nghiên cứu thêm thì thấy cái dispatcher nó có hệ thống event
chạy trước khi dispatch. Hay quá, nếu bắt event này và chạy thì nó sẽ chưa cần phải dispatch,
nhẹ hơn cho server. Mình đã viết một class DispatcherFilter để kiểm tra và cho hiển thị trang cảnh báo.

```php
<?php

namespace App\Routing\Filter;

use Cake\Core\Configure;
use Cake\Event\Event;
use Cake\Routing\DispatcherFilter;

class MaintenanceModeFilter extends DispatcherFilter
{
    /**
     * @var int This filter should run first
     */
    protected $_priority = -1;

    public function implementedEvents()
    {
        return [
            'Dispatcher.beforeDispatch' => [
                'callable' => 'handle',
                'priority' => $this->_config['priority']
            ],
        ];
    }

    public function handle(Event $event)
    {
        if ($this->enabledMaintenanceMode()) {
            // stop event
            $event->stopPropagation();
            $request = $event->data['request'];
            $response = $event->data['response'];

            // TODO: render maintenance alert page
            $response->body('Maintenance mode ON');

            return $response;
        }
    }

    /**
     * @return boolean
     */
    protected function enabledMaintenanceMode()
    {
        // TODO: check maintenance mode is enabled
    }
}
```

Như các bạn thấy, mình set property `$_priority = -1` là để đảm bảo nó được thực thi
trước các filter khác. Mình cũng viết class shell để có hai commands bật và tắt
chế độ bảo trì. Để tạo cờ chó việc bặt tắt chế độ bảo trì, mình đã sử dụng một cách
là khi bật chế độ bảo trì, mình tạo ra một file là `maintenance.php` ở trong thư mục
`TMP` của ứng dụng, khi tắt chế độ bảo trì thì sẽ xóa nó đi. Nội dung của file này
là các config giúp cho mình render trang cảnh bảo.

## Sharing

Mình cũng đã dóng gói thành một plugin để cho mọi người có thể dễ dàng sử dụng hơn.
Mọi người có thể cài đặt nó và sử dụng tại [đây][plugin-url]

[plugin-url]:  https://github.com/lemonphp/cakeplugin-maintenance-mode.git
