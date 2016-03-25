---
title:    How to make unit test with PHPUnit (p1)
layout:   post
category: tutorial-tips
tags:     [skill, php, unit test]
feature:  /assets/img/PHPUnit-logo.png
---

Ở [bài trước][prev-post], mình đã giới thiệu với các bạn về thư viên PHPUnit và
một các đơn giản để setup nó chạy tự động trước mỗi lần commit. Tuy nhiên
nhiều bạn vẫn thắc mắc là làm thế nào để tạo unit test vói PHPUnit? Ở loạt bài này,
mình sẽ trình bày kỹ hơn về cách sử dụng PHPUnit và các kỹ năng viết unit test
nâng cao.

<!--more-->

## Requirements

- PHPUnit 5.2 (current stable) yêu cầu PHP 5.6 trở lên (Nếu bạn cần test với phiên bản PHP thấp hơn
  thì dùng PHPUnit có phiên bản thấp hơn. Ví dụ: PHPUnit 4.8 yêu cầu PHP 5.3.3 trở lên).
- PHPUnit yêu cầu các extensions: [dom][ext-dom], [json][ext-json], [pcre][ext-pcre], [reflection][ext-reflection] và [spl][ext-spl].
  Các extensions này thường được mặc định bật, nhưng bạn cũng nên kiểm tra lại bời lệnh `php -m`.
- Tính năng code coverage report còn yêu cầu bạn phải cài [Xdebug][] và hai extentions [tokenizer][ext-tokenizer] và [xmlwriter][ext-xmlwriter]

## Installing PHPUnit

Để cài đặt PHPUnit thì bạn có thể làm theo các cách sau:

### Download PHP Archive (PHAR) file

> Để có thể chạy được file PHAR, bạn cần bật các extensions [phar][ext-phar] và [openssl][ext-openssl].
> Nếu extension [Suhosin][ext-suhosin] được bật thì bạn phải set trong file `php.ini` là:
>
> ```ini
> suhosin.executor.include.whitelist = phar
> ```

Bạn vào trang https://phar.phpunit.de/ và download file PHAR của bản PHPUnit bạn cần về máy.
Copy vào thư mục project, thế là bạn đã có thể dùng bởi lệnh `php phpunit.phar`.   
Tuy nhiên để có thể sử dụng được trong nhiều project thì bạn cần làm như sau:

- Với UNIX OS:

  ```
  $ chmod +x phpunit.phar
  $ sudo mv phpunit.phar /usr/local/bin/phpunit
  $ phpunit --version
  ```

- Với Windows OS:

  ```
  C:\Downloads> mkdir C:\bin
  C:\Downloads> copy phpunit.phar C:\bin
  C:\Downloads> cd C:\bin
  C:\bin> echo @php "%~dp0phpunit.phar" %* > phpunit.cmd
  C:\bin> set PATH=%PATH%;C:\bin
  C:\bin> phpunit --version
  ```

  Tham khảo thêm tại [đây][install-phar-win]


### Using `composer` (recommend)

> Bạn cần cài `composer` trước, tham khảo tại [đây][install-composer].

- Local install (đây là cách mình vẫn hay dùng):

  ```
  $ composer require --dev "phpunit/phpunit=5.2.*"
  $ ./vendor/bin/phpunit --version
  ```

- Global install:

  ```
  $ composer global require "phpunit/phpunit=5.2.*"
  $ phpunit --version
  ```

## Run PHPUnit

Bạn có thể chạy `phpunit -v` để xem phiên bản và các options. Xem thêm tại [đây][run-options].

> Nếu bạn muốn tạo một tài liệu mô tả dạng TestDox cho class thì bạn có thể sử dụng tính năng [testdox][testdox].

## Organizing Tests

Ở bài trước, mình cũng đã giới thiệu [conventions][conventions] cho PHPUnit.   
Dự vào đó mình tổ chức project như sau:

```
project
├── composer.json
├── composer.lock
├── phpunit.xml.dist
├── src
│   └── Calculator.php
├── tests
│   ├── bootstrap.php
│   └── CalculatorTest.php
└── vendor
```

Các bạn chú ý đến 3 file: `composer.json`, `phpunit.xml.dist`, `tests/bootstrap.php`.   
Nội dung chi tiết cả từng file, bạn xem tại [demo repository][demo-repos].

## Write first test

Chúng ta cùng bắt đầu viết class `CalculatorTest.php` nào:

> Đừng quên [conventions][conventions] nhé các bạn!

```php
<?php
namespace Demo\Calculator\Tests;

class CalculatorTest extends \PHPUnit_Framework_TestCase
{
    public function testAdd()
    {
        $this->assertSame(10, Calculator::add(5, 5));
    }
}
```

Trong method `testAdd` trên, mình đã dùng method `assertSame` để check kết quả trả về 
của method `Calculator::add(5, 5)` có phải là 10 không?   
Sử dụng `assertSame` để check cả về giá trị và kiểu dữ liệu. Ngoài ra còn có thể 
sử dụng nhiều method `assert*` khác để check, bạn xem chi tiết ở [đây][assertions].

Bây giờ bạn thử chạy `phpunit` xem nào? Wow. Thật đơn giản, bạn đã viết đc rồi đấy.

## Test dependencies



## Data providers

Sử dụng annotation[^1] `@dataProvider`

## Testing Exception, PHP errors, output


[^1]: Annotation là một dạng đặc biệt của ngữ pháp siêu dữ liệu (syntactic metadata),
      nó có thể được thêm vào trong mã nguồn của các ngôn ngữ lập trình.
      Trong PHP, annotation có dạng `@annotation` và nằm trong một comment block
      được bắt đầu bởi `/**` và kết thúc bởi `*/`. Bạn có thể xem tất cả các annotations
      mà PHPUnit hỗ trợ ở [đây][annotations].



[prev-post]:         /2016-03-08/auto-test-with-phpunit.html
[conventions]:       /2016-03-08/auto-test-with-phpunit.html#conventions
[ext-dom]:           http://php.net/manual/en/dom.setup.php
[ext-json]:          http://php.net/manual/en/json.installation.php
[ext-pcre]:          http://php.net/manual/en/pcre.installation.php
[ext-reflection]:    http://php.net/manual/en/reflection.installation.php
[ext-spl]:           http://php.net/manual/en/spl.installation.php
[ext-tokenizer]:     http://php.net/manual/en/tokenizer.installation.php
[ext-xmlwriter]:     http://php.net/manual/en/xmlwriter.installation.php
[ext-phar]:          http://php.net/manual/en/phar.installation.php
[ext-openssl]:       http://php.net/manual/en/openssl.installation.php
[ext-suhosin]:       https://suhosin.org/stories/index.html
[Xdebug]:            https://xdebug.org/docs/install
[install-phar-win]:  https://phpunit.de/manual/5.2/en/installation.html#installation.phar.windows
[install-composer]:  https://getcomposer.org/doc/00-intro.md
[run-options]:       https://phpunit.de/manual/5.2/en/textui.html
[xml-configuration]: https://phpunit.de/manual/5.2/en/organizing-tests.html#organizing-tests.xml-configuration
[testdox]:           https://phpunit.de/manual/5.2/en/other-uses-for-tests.html#other-uses-for-tests.agile-documentation
[assertions]:        https://phpunit.de/manual/5.2/en/appendixes.assertions.html
[annotations]:       https://phpunit.de/manual/5.2/en/appendixes.annotations.html
[demo-repos]:        https://github.com/oanhnn/demo-phpunit-calculator.git