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

```
$ phpunit -v
PHPUnit 5.2.12 by Sebastian Bergmann and contributors.

Usage: phpunit [options] UnitTest [UnitTest.php]
       phpunit [options] <directory>

Code Coverage Options:

  --coverage-clover <file>  Generate code coverage report in Clover XML format.
  --coverage-crap4j <file>  Generate code coverage report in Crap4J XML format.
  --coverage-html <dir>     Generate code coverage report in HTML format.
  --coverage-php <file>     Export PHP_CodeCoverage object to file.
  --coverage-text=<file>    Generate code coverage report in text format.
                            Default: Standard output.
  --coverage-xml <dir>      Generate code coverage report in PHPUnit XML format.
  --whitelist <dir>         Whitelist <dir> for code coverage analysis.
  --disable-coverage-ignore Disable annotations for ignoring code coverage.

Logging Options:

  --log-junit <file>        Log test execution in JUnit XML format to file.
  --log-tap <file>          Log test execution in TAP format to file.
  --log-teamcity <file>     Log test execution in TeamCity format to file.
  --log-json <file>         Log test execution in JSON format.
  --testdox-html <file>     Write agile documentation in HTML format to file.
  --testdox-text <file>     Write agile documentation in Text format to file.
  --reverse-list            Print defects in reverse order

Test Selection Options:

  --filter <pattern>        Filter which tests to run.
  --testsuite <pattern>     Filter which testsuite to run.
  --group ...               Only runs tests from the specified group(s).
  --exclude-group ...       Exclude tests from the specified group(s).
  --list-groups             List available test groups.
  --test-suffix ...         Only search for test in files with specified
                            suffix(es). Default: Test.php,.phpt

Test Execution Options:

  --report-useless-tests    Be strict about tests that do not test anything.
  --strict-coverage         Be strict about @covers annotation usage.
  --strict-global-state     Be strict about changes to global state
  --disallow-test-output    Be strict about output during tests.
  --disallow-resource-usage Be strict about resource usage during small tests.
  --enforce-time-limit      Enforce time limit based on test size.
  --disallow-todo-tests     Disallow @todo-annotated tests.

  --process-isolation       Run each test in a separate PHP process.
  --no-globals-backup       Do not backup and restore $GLOBALS for each test.
  --static-backup           Backup and restore static attributes for each test.

  --colors=<flag>           Use colors in output ("never", "auto" or "always").
  --columns <n>             Number of columns to use for progress output.
  --columns max             Use maximum number of columns for progress output.
  --stderr                  Write to STDERR instead of STDOUT.
  --stop-on-error           Stop execution upon first error.
  --stop-on-failure         Stop execution upon first error or failure.
  --stop-on-warning         Stop execution upon first warning.
  --stop-on-risky           Stop execution upon first risky test.
  --stop-on-skipped         Stop execution upon first skipped test.
  --stop-on-incomplete      Stop execution upon first incomplete test.
  --fail-on-warning         Treat tests with warnings as failures.
  --fail-on-risky           Treat risky tests as failures.
  -v|--verbose              Output more verbose information.
  --debug                   Display debugging information during test execution.

  --loader <loader>         TestSuiteLoader implementation to use.
  --repeat <times>          Runs the test(s) repeatedly.
  --tap                     Report test execution progress in TAP format.
  --teamcity                Report test execution progress in TeamCity format.
  --testdox                 Report test execution progress in TestDox format.
  --printer <printer>       TestListener implementation to use.

Configuration Options:

  --bootstrap <file>        A "bootstrap" PHP file that is run before the tests.
  -c|--configuration <file> Read configuration from XML file.
  --no-configuration        Ignore default configuration file (phpunit.xml).
  --no-coverage             Ignore code coverage configuration.
  --include-path <path(s)>  Prepend PHP's include_path with given path(s).
  -d key[=value]            Sets a php.ini value.

Miscellaneous Options:

  -h|--help                 Prints this usage information.
  --version                 Prints the version and exits.
  --atleast-version <min>   Checks that version is greater than min and exits.

  --check-version           Check whether PHPUnit is the latest version.
  --self-update             Update PHPUnit to the latest version within the same
                            release series.

  --self-upgrade            Upgrade PHPUnit to the latest version.
```

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
│   ├── Currency.php
│   ├── Formatter
│   │   ├── FormatterInterface.php
│   │   └── IntlFormatter.php
│   └── Money.php
├── tests
│   ├── bootstrap.php
│   ├── CurrencyTest.php
│   ├── Formatter
│   │   └── IntlFormatterTest.php
│   └── MoneyTest.php
└── vendor
```

Các bạn chú ý đến 3 file: `composer.json`, `phpunit.xml.dist`, `tests/bootstrap.php`.


## Write first test

> Đừng quên [conventions][conventions] nhé các bạn!


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