---
title:    Cách viết unit test với PHPUnit (p1)
layout:   post
category: tutorial-tips
tags:     [skill, php, unit-test, dev-ops]
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

> Unit Tests are primarily written as a good practice to help developers identify and
> fix bugs, to refactor code and to serve as documentation for a unit of software under
> test. To achieve these benefits, unit tests ideally should cover all the possible paths
> in a program. One unit test usually covers one specific path in one function or method.
> However a test method is not necessary an encapsulated, independent entity. Often
> there are implicit dependencies between test methods, hidden in the implementation scenario of a test.
> --- **Adrian Kuhn et. al.**

Mình tạm dịch đoạn trên như sau: _Mục đích chính của Unit Tests là để giúp deverlopers
xác định và sửa lỗi, để tái cấu trúc lại code và làm tài liệu cho kiểm thử một đơn vị phần mềm.
Để đạt được điều đó, các Unit Tests nen covers tất cả các possible paths trong chương trình.
Mỗi unit test thường xuyên cover một path cụ thể của một function hoặc method. Tuy nhiên,
Một method test không cần thiết phải là một sự đóng gói, thực thể độc lập.
Chúng thường phụ thuộc vào nhau, và ẩn trong kịch bản triển khai một thử nghiệm._

Như vậy thì làm thế nào để khai báo test method này phụ thuộc vào các test method khác?
PHPUnit cung cấp cho ta một cách là sử dụng một annotation[^1] là `@depends`.

```php
<?php
class StackTest extends PHPUnit_Framework_TestCase
{
    public function testEmpty()
    {
        $stack = array();
        $this->assertEmpty($stack);

        return $stack;
    }

    /**
     * @depends testEmpty
     */
    public function testPush(array $stack)
    {
        array_push($stack, 'foo');
        $this->assertEquals('foo', $stack[count($stack)-1]);
        $this->assertNotEmpty($stack);

        return $stack;
    }

    /**
     * @depends testPush
     */
    public function testPop(array $stack)
    {
        $this->assertEquals('foo', array_pop($stack));
        $this->assertEmpty($stack);
    }
}
```

Khi sử dụng dependencies thì các test dependency methods phải return về kết quả.
Chúng sẽ là tham số cho method test của chúng ta.

## Data providers

Bây giờ quay lại với method `testAdd` ở trên, chúng ta thấy là có một vấn đề là
nó chỉ test có một trường hợp nên không chắc các trường hợp khác, method `add` đã chạy đúng.
Do đó chúng ta cần test thêm nhiều trường hợp khác.

```php
<?php
namespace Demo\Calculator\Tests;

class CalculatorTest extends \PHPUnit_Framework_TestCase
{
    public function testAdd()
    {
        $this->assertSame(10, Calculator::add(5, 5));
        $this->assertSame(10, Calculator::add(-5, 15));
        $this->assertSame(10, Calculator::add(0, 10));
    }
}
```

Khoan, từ từ đã. Có gì không ổn ở đây. Đó là đây chỉ là test đơn giản và chúng ta
đang sử dụng cách lặp lại phần code test. Nếu như với những test phức tạp thì sao?
Chúng ta không thể làm như vậy được. Vậy phải làm thế nào?   
PHPUnit cũng đã tính đến điều này, và cho phép bạn sử dụng annotation `@dataProvider`
để thực hiện việc test với nhiều trường hợp dữ liệu. Và bây giờ trong method test
của bạn chỉ cần định nghĩa cách thức test mà thôi.

```php
<?php
namespace Demo\Calculator\Tests;

class CalculatorTest extends \PHPUnit_Framework_TestCase
{
    /**
     * @dataProvider dataForTestAdd
     */
    public function testAdd($a, $b, $expected)
    {
        $this->assertSame($expected, Calculator::add($a, $b));
    }

    public function dataForTestAdd
    {
        return [
            //[$a, $b, $expected],
            [5, 5, 10],
            [-5, 15, 10],
            [0, 10, 10],
        ];
    }
}
```

> Syntax: `@dataProvider methodName`, chỉ định một public method của class test
> sẽ cung cấp các trường hợp dữ liệu cho test. Method này sẽ return một array,
> với mỗi phần tử là một trường hợp dữ liệu, là một mãng các tham số cho method test
> theo đúng thứ tự.

Thật là đơn giản và clear phải không?

## Testing Exception

Trong một số trường hợp, bạn cần phải test throw ra các exception. Bạn có thể sử dụng
method `expectException()`, `expectExceptionCode()`, `expectExceptionMessage()`
và `expectExceptionMessageRegExp()` đã được thêm sẵn trong PHPUnit để test trong
các trường hợp này.

```php
<?php
namespace Demo\Calculator\Tests;

class CalculatorTest extends PHPUnit_Framework_TestCase
{
    public function testDivByZero()
    {
        $this->expectException(\InvalidArgumentException::class); // exception class name
        $this->expectExceptionMessage('Division by zero');        // exception mesage
        Calculator::div(10, 0);
    }
}
```

Một cách khác, bạn có thể dùng các annotations để setup các test throw exception.

```php
<?php
namespace Demo\Calculator\Tests;

class CalculatorTest extends PHPUnit_Framework_TestCase
{
    /**
     * Test case dividing by zero
     *
     * @expectedException \InvalidArgumentException
     * @expectedExceptionMessage Division by zero
     */
    public function testDivByZero()
    {
        Calculator::div(10, 0);
    }
}
```

## Testing PHP errors, warnings and notices

Bời mặc định PHPUnit chuyển các PHP errors, warnings và notices xuất hiện trong quá trình
thực thi test thành các exception, nên bạn cũng có thể sử dụng cách trên để test method
sẽ sinh ra PHP errors.

```php
<?php
namespace Demo\Calculator\Tests;

class CalculatorTest extends PHPUnit_Framework_TestCase
{
    /**
     * Test case modulus by zero
     *
     * @expectedException \PHPUnit_Framework_Error
     * @expectedExceptionMessage Division by zero
     */
    public function testModByZero()
    {
        Calculator::mod(10, 0); // 10 % 0
    }
}
```

Tương tự với `PHPUnit_Framework_Error_Notice` và `PHPUnit_Framework_Error_Warning`.

> Chú ý: Setting `error_reporting` của PHP có thể giới hạn các kiểu error được
> đưa ra cũng như convert thành exception

## Test PHP output

Thỉnh thoảng bạn cũng phải test PHP's Output Buffering xem nó có như mòng đợi không?
Trong trường hợp này, hai method `expectOutputString()` và `expectOutputRegex()`
sẽ giúp bạn thục hiện điều đó.

```php
<?php
class OutputTest extends PHPUnit_Framework_TestCase
{
    public function testExpectFooActualFoo()
    {
        $this->expectOutputString('foo');
        print 'foo';
    }

    public function testExpectBarActualBaz()
    {
        $this->expectOutputRegex('/^baz(\d*)$/');
        print 'baz' . rand(1, 1000);
    }
}
```

 --- [_Còn tiếp_][next-post] ---

[^1]: Annotation là một dạng đặc biệt của ngữ pháp siêu dữ liệu (syntactic metadata),
      nó có thể được thêm vào trong mã nguồn của các ngôn ngữ lập trình.
      Trong PHP, annotation có dạng `@annotation` và nằm trong một comment block
      được bắt đầu bởi `/**` và kết thúc bởi `*/`. Bạn có thể xem tất cả các annotations
      mà PHPUnit hỗ trợ ở [đây][annotations].


[prev-post]:         /2016-03-08/auto-test-with-phpunit.html
[next-post]:         /2016-03-25/how-to-make-unit-test-with-phpunit-p2.html
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
