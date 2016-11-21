---
title: Tự động test với PHPUnit
layout:   post
category: tutorial-tips
tags:     [skill, php, unit-test, dev-ops]
feature:  /assets/img/PHPUnit-logo.png
---

Mình là một developer, mình muốn tạo ra nhũng thứ tốt đẹp cho thế giới này
bằng những dòng code (nói quá lên chút :D). Và mình cũng như các bạn,
không muốn sản phẩm của mình tạo ra đầy những bug, phải không? Nhưng tạo sao
chúng ta lại ngại việc test đến như vậy? Một phần nguyên nhân là việc test tốn nhiều time,
setup phức tạp và luôn phải lặp đi lặp lại công việc nhàn chán này. Mình đã
đi tìm cách thực hiện việc này một cách tự động. Và thật tuyệt vời, mình đã tìm thấy __PHPUnit__.

<!--more-->

## PHPUnit là gì?

> PHPUnit is a unit testing framework for the PHP programming language.
> It is an instance of the xUnit architecture for unit testing frameworks.
> PHPUnit was created by Sebastian Bergmann and its development is hosted on GitHub.
> --- **Wikipedia**

Mình hiểu nôm na thì nó là một thư viện, một công cụ giúp ta tạo ra các script unit test.
Còn **_unit_** là đơn vị, mà đơn vị to hay nhỏ thì tuỳ vào trường hợp,
như là **_met_**, **_minimet_** và **_kilomet_** vậy.

## Bắt đầu với PHPUnit

Có nhiều cách để bắt đầu với PHPUnit. Ở đây mình giới thiệu 1 cách sử dụng [`composer`][1] để cài đặt PHPUnit.

```shell
$ cd /path/to/project
$ composer require phpunit/phpunit ~4.8
$ ./vendor/bin/phpunit -v
```

Bạn tạo file `phpunit.xml` để thiết lập các tham số cho PHPUnit khi chạy.

```xml
<?xml version="1.0" encoding="utf-8"?>
<phpunit colors="false" verbose="true" stopOnError="false"
    stopOnFailure="false" bootstrap="tests/bootstrap.php">
    <!-- Change php settings -->
    <php>
        <ini name="memory_limit" value="-1"/>
        <ini name="apc.enable_cli" value="1"/>
    </php>

    <!-- Add any test suites you want to run here -->
    <testsuites>
        <testsuite name="Test Source">
            <directory>tests</directory>
        </testsuite>
    </testsuites>

    <!-- Ignore vendor tests in code coverage reports -->
    <filter>
        <blacklist>
            <directory>vendor</directory>
            <directory>bin</directory>
        </blacklist>
        <whitelist>
            <directory suffix=".php">src</directory>
        </whitelist>
    </filter>
</phpunit>
```

Trong file `phpunit.xml` trên, mình có khai báo là sẽ dùng file `tests/bootstrap.php`
để khởi tạo các yếu tố cần thiết cho việc giả lập môi trường test và load các class test.

```php
<?php
// tests/bootstrap.php

// Enable Composer autoloader
$autoloader = require dirname(__DIR__) . '/vendor/autoload.php';
// Register test classes
$autoloader->addPsr4('YourOrg\\YourProject\\Tests\\', __DIR__);
```

Tiếp theo, chúng ta bắt đầu viết các class test và đừng quên đúng conventions của nó.

## Conventions

1. Tên các class test sẽ kết thúc bằng `Test` và thường tên giống với tên class cần test
2. Tên file giống với tên class test
3. Class test phải kế thừa từ class `PHPUnit_Framework_TestCase`
4. Method test phải bắt đầu bằng `test` và các method này phải là `public`.
   Ví dụ method `testVerifyAccount()` để test cho method `verifyAccount()`

## Làm thế nào để test không sót case?

- Lập bảng matrix test case

  ![Bảng matrix cho test method matchingAccount()](/assets/img/table-matrix-for-test.jpg)*Bảng matrix cho test method `matchingAccount($username, $password): boolean`*

- Viết test script theo bảng matrix, có thể dùng tính năng [**dataProvider**][6] để thực hiện test với nhiều case

```php
<?php
//...
/**
 * Test `matchingAccount()` method
 *
 * @param string  $username
 * @param string  $password
 * @param boolean $expected
 * @dataProvider dataTestMatchingAccount
 */
public function testMatchingAccount($username, $password, $expected)
{
    $result = $this->authenticator->matchingAccount($username, $password);
    $this->assertSame($expected, $result);
}

/**
 * Data for test `matchingAccount()` method
 *
 * @return array
 */
public function dataTestMatchingAccount()
{
    return [
        ['',       '',         false],
        ['',       'abc12345', false],
        ['oanhnn', '',         false],
        ['oanhnn', 'abc12345', true],
        ['oanhnn', '12345678', false],
        ['oanh',   '',         false],
        ['oanh',   'abc12345', false],
        ['oanh',   '12345678', false],
    ];
}
```

Bây giờ chạy test thôi:

```shell
$ ./vendor/bin/phpunit
```

## Tự động test trước khi git commit

Có nhiều cách để thêm trigger cho phép chúng ta tự động chạy script test,
ở đây mình xin giới thiệu 1 cách là sử dụng git hook `pre-commit`. Sau khi
đã có đoạn script test, bạn tìm đến thư mục `/path/to/project/.git/hooks/` và tạo file `pre-commit`
với nội dung như sau:

```php
#!/usr/bin/php
<?php
// Hook configuration
$project = 'Training PHPUnit 01';

// Tell the commiter what the hook is doing
echo PHP_EOL;
echo "Starting unit tests...\n";

// Execute project unit tests
exec('phpunit', $output, $returnCode);

// if the build failed, output a summary and fail
if ($returnCode !== 0) {
    // find the line with the summary; this might not be the last
    while (($minimalTestSummary = array_pop($output)) !== null) {
        if (strpos($minimalTestSummary, 'Tests:') !== false) {
            break;
        }
    }

    // output the status and abort the commit
    echo "Test suite for '{$project}' failed:\n";
    echo $minimalTestSummary;
    echo PHP_EOL;
    exit(1);
}

echo "All tests for '{$project}' passed.\n";
echo PHP_EOL;
exit(0);
```

Sau đó thêm quyền thực thi cho file `pre-commit` và commit thử bạn nhé!

```shell
$ cd /path/to/project
$ vi .git/hooks/pre-commit
$ chmod a+x .git/hooks/pre-commit
$ git commit -am "Added changes"

Starting unit tests...
All tests for 'Training PHPUnit 01' passed.

[master xxxxxxx] Added changes
 1 files changed, 1 insertions(+), 1 deletions(-)
```

Thật tuyệt vời phải không, sao chúng ta không làm việc này thành 1 quy trình sản xuất phần mềm?
Thật ra từ lâu người ta đã nghĩ tới việc này và tạo ra mô hình TDD.

## TDD là cái gì?

_Định nghĩa của Wikipedia:_

> Test-driven development (TDD) is a software development process that relies on the repetition of a very short development cycle:
> first the developer writes an (initially failing) automated test case that defines a desired improvement or new function,
> then produces the minimum amount of code to pass that test, and finally refactors the new code to acceptable standards.

Có thể hiểu nôm na nó là một quá trình phát triển phần mềm mà trong đó người ta sẽ viết
script auto test để mô tả yêu cầu của tính năng mới rồi mới đi tạo lượng code tối thiểu để pass qua phần test đó.
Cuối cùng họ sẽ chỉnh sửa lại phần code để phù hợp với các yêu cầu, các chuẩn hoá khác.   
Các bước trong quá trình TDD:

1. Tạo script test
2. Chạy test và chỉnh sửa script test theo đúng spec
3. Viết code chương trình
4. Chạy test
5. Chỉnh sửa lại code để sửa lỗi
6. Lặp lại các bước khi có thêm yêu cầu

![TDD Global Lifecycle](https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/TDD_Global_Lifecycle.png/1024px-TDD_Global_Lifecycle.png)*TDD Global Lifecycle*

## Tổng kết

Như vậy chúng ta đã biết cách tạo script test đơn giản với PHPUnit, biết cách thêm trigger tự động test trước mỗi lần commit với git,
và biết TDD là gì, áp dụng nó và công việc gồm những bước nào. Ở bài sau, cũng ta sẽ cùng trao đổi sâu hơn về cách viết unit test với PHPUnit.

## Resources

<i class="fa fa-file-powerpoint-o"></i> [Slide][7]   

## Tài liệu tham khảo

- [PHPUnit site][3]
- [Wikipedia about PHPUnit][4]
- [Unit testing tutorial of Juan Treminio][5]

[1]: https://getcomposer.org
[2]: https://github.com/rikkeisoft/training-phpunit-01.git
[3]: https://phpunit.de
[4]: https://en.wikipedia.org/wiki/PHPUnit
[5]: https://jtreminio.com/2013/03/unit-testing-tutorial-introduction-to-phpunit
[6]: https://phpunit.de/manual/current/en/writing-tests-for-phpunit.html#writing-tests-for-phpunit.data-providers
[7]: /slides/2016-03-08-auto-test-with-phpunit.html
