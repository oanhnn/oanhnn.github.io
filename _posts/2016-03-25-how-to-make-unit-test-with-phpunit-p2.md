---
title:    Cách viết unit test với PHPUnit (p2)
layout:   post
category: tutorial-tips
tags:     [skill, php, unit-test, dev-ops]
feature:  /assets/img/PHPUnit-logo.png
---

Ở [phần trước][prev-post], mình đã giới thiệu cách cài đặt cũng như cách tổ chức và
viết các test cases đơn giản. Trong phần này, mình sẽ giới thiệu các kỹ năng
viết unit test phức tạp hơn.

<!--more-->

## Code coverage analysis

Một câu hỏi mà chúng ta thường xuyên đặt ra là: "phải viết unit test đến bao giờ?"   
Câu trả lời thường thấy là: “Cho tới khi đạt 100% code coverage”.   
Vậy code coverage là gì?

> In computer science, code coverage is a measure used to describe the degree to
> which the source code of a program is tested by a particular test suite.
> A program with high code coverage has been more thoroughly tested and has a lower chance
> of containing software bugs than a program with low code coverage.
> --- **Wikipedia**

Bạn có thể hiểu là nó là độ bao phủ của phần test của mình. Thông số này càng lớn nghĩa
là chương trình được test càng kỹ và sẽ ít bug hơn.   
Làm thế nào để biết được code coverage bây giờ? Phần nào là phần chưa được test đến?
PHPUnit cũng cung cấp cho bạn chức năng phân tích code coverage của phần script test của các bạn.

> Tính năng này yêu cầu bạn phải cài Xdebug và hai extensions là tokenizer và xmlwriter

Bạn chạy phpunit với option tạo code coverage report

```
$ ./vendor/bin/phpunit --coverage-text
```

Và bạn thu được kết quả như sau:   
![Code coverage report](/assets/img/code-coverage-report.png)*PHPUnit report code coverage with text format*

Bạn cũng có thể tạo report ra file với các optíon `coverage-text`, `coverage-html`,
`coverage-php`, `coverage-xml`. Bạn có thể tham khảo thêm tại [đây][code-coverage-analysis]

## Unit test for private, protected methods & property

Để đạt tới code coverage tối đa (100%) thì bạn cần viết cả test cho các private
và protected methods. Vậy thì làm thế nào để viết unit test cho chúng trong khi
chúng ta không thể call chúng từ ngoài class của chúng?

Có thể thay đổi code để cho phép test kết quả của các protected method hay giá trị
của property, nhưng ở đây mình sẽ giới thiệu một cách test các private và protected
methods mà mình vẫn hay dùng, đó là sử dụng extension [reflection][php-reflection].   
Đầu tiên bạn thêm ba methods sau vào class test của bạn.

```php
<?php

class MyTestCase extends \PHPUnit_Framework_TestCase
{
    /**
     * Get a non public property of an object
     *
     * @param object $obj
     * @param string $property
     * @return mixed
     */
    protected function getNonPublicProperty($obj, $property)
    {
        if (!is_object($obj) || !is_string($property)) {
            return null;
        }
        $ref = new \ReflectionProperty(get_class($obj), $property);
        $ref->setAccessible(true);

        return $ref->getValue($obj);
    }

    /**
     * Set value for a non public property of an object
     *
     * @param object $obj
     * @param string $property
     * @param mixed  $value
     */
    protected function setNonPublicProperty($obj, $property, $value)
    {
        if (!is_object($obj) || !is_string($property)) {
            return;
        }
        $ref = new \ReflectionProperty(get_class($obj), $property);
        $ref->setAccessible(true);

        $ref->setValue($obj, $value);
    }

    /**
     * Call protected/private method of a class.
     *
     * @param object $obj    Instantiated object that we will run method on.
     * @param string $method Method name to call
     * @param array  $params Array of parameters to pass into method.
     * @return mixed         Method return.
     * @throws \InvalidArgumentException
     */
    protected function invokeNonPublicMethod($obj, $method, $params = [])
    {
        if (!is_object($obj) || !is_string($method) || method_exists($obj, $method)) {
            throw new \InvalidArgumentException();
        }
        $ref = new \ReflectionMethod($obj, $method);
        $ref->setAccessible(true);

        return $ref->invokeArgs($obj, $params);
    }
}
```

Bây giờ thì bạn có thể dễ dàng test các private/protected methods và property rồi phải không?
Ví dụ để test protected method `cryptPassword` của class User thì chúng ta làm như sau:

```php
<?php
// ...
    public function testCryptPassword($expectedCryptedPassword, $rawPassword)
    {
        $user = new User();
        $result = $this->invokeNonPublicMethod($user, 'cryptPassword', [$rawPassword]);
        $this->assertSame($expectedCryptedPassword, $result);
    }
```

## 100% code coverage and Why it is not enough?

Vậy là chúng ta có thể viết test đến 100% code coverage rồi phải không nào?
Nhưng liệu như vậy đã đủ chưa?   
Theo cá nhân mình là chưa đủ. Bởi vì code coverage chỉ cho thấy tất cả các phần code
đã được test chạy qua, nhưng chưa phải là đã test được hết tất cả các trường hợp.
Ví dụ như truờng hợp sau:

```php
<?php
// ...
    /**
     * Return 'XY' when $x >= 0 and $y >= 0
     * Return 'xY' when $x <  0 and $y >= 0
     * Return 'Xy' when $x >= 0 and $y <  0
     * Return 'xy' when $x <  0 and $y <  0
     *
     * @param int $x
     * @param int $y
     * @return string
     */
    public function abc ($x, $y)
    {
        $z = '';
        $z .= ($x >= 0) ? 'X' : 'x';
        $z .= ($y >= 0) ? 'Y' : 'y';
        if ($x < 0 && $y < 0) {
            $z = 'XY';
        }

        return $z;
    }
```

Như với method trên, ta thấy chỉ với 3 truờng hợp (1, 1), (-1, 1) và (1, -1),
nó đã cho code coverage 100% và test là pass. Nhưng với truờng hợp (-1, -1) nó lại sai! @@   
Vì vậy chúng ta nên tạo matrix test để có thể bao phủ hết tất cả các truờng hợp.

## Stubbing & Mocking

Trong một số trường hợp, chúng ta phải cover cả dữ liệu ngẫu nhiên, dữ liệu `time()`,
hay từ hãng thứ ba, vậy thì làm thế nào mình có thể test được các case theo
mong muốn của mình chứ?   


## Fixtures

## Database testing

## Skip test

## Extending PHPUnit

## Resources

<i class="fa fa-file-powerpoint-o"></i> [Slide][slide]   

## Tài liệu tham khảo

- [PHPUnit site][3]
- [Wikipedia about PHPUnit][4]
- [Unit testing tutorial of Juan Treminio][5]


[prev-post]:              /2016-03-24/how-to-make-unit-test-with-phpunit-p1.html
[code-coverage-analysis]: https://phpunit.de/manual/current/en/code-coverage-analysis.html
[php-reflection]:         http://php.net/manual/en/book.reflection.php
[slide]:                  /slides/2016-03-24-how-to-make-unit-test-with-phpunit.html
[3]: https://phpunit.de
[4]: https://en.wikipedia.org/wiki/PHPUnit
[5]: https://jtreminio.com/2013/03/unit-testing-tutorial-introduction-to-phpunit
