---
title:    How to make unit test with PHPUnit (p2)
layout:   post
category: tutorial-tips
tags:     [skill, php, unit test]
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

## Unit test for private, protected method

Để đạt tới code coverage tối đa (100%) thì bạn cần viết cả test cho các private
và protected methods. Vậy thì làm thế nào để viết unit test cho chúng trong khi
chúng ta không thể call chúng từ ngoài class của chúng?

Mình sẽ giới thiệu một cách test các private và protected methods mà mình vẫn hay dùng,
đó là sử dụng extension [reflection][php-reflection].

```php

public function callProtectedMethod($obj, $methodName, $args = [])
{
    
}

```

## 100% code coverage and Why it is not enough?

## Stubbing & Mocking

## Fixtures

## Database testing

## Skip test

## Extending PHPUnit



[prev-post]:              /2016-03-24/how-to-make-unit-test-with-phpunit-p1.html
[code-coverage-analysis]: https://phpunit.de/manual/current/en/code-coverage-analysis.html
[php-reflection]:         http://php.net/manual/en/book.reflection.php
