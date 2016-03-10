#Bạn mất bao nhiêu thời gian cho việc kiểm thử (test) ?#
Mình là một developer, mình muốn tạo ra cũng thứ tốt đẹp cho thế giới này bằng những dòng code (nói quá lên chút :D).   
Và mình cũng như các bạn, không muốn sản phẩm của mình tạo ra đầy những bug, phải không? 
Nhưng tạo sao chúng ta lại ngại việc test đến như vậy? Một phần nguyên nhân là việc test tốn nhiều time, 
setup phức tạp và luôn phải lặp đi lặp lại công việc nhàn chán này. Mình không cam chịu điều này và 
mình đã đi tìm cách thực hiện việc này 1 cách tự động. Và thật tuyệt vời, mình đã tìm thấy PHPUnit.

#PHPUnit là gì?#
_Định nghĩa của Wikipedia:_
> PHPUnit is a unit testing framework for the PHP programming language. 
> It is an instance of the xUnit architecture for unit testing frameworks.
> PHPUnit was created by Sebastian Bergmann and its development is hosted on GitHub.

#Bắt đầu với PHPUnit#
Có nhiều cách để bắt đầu với PHPUnit. Ở đây mình giới thiệu 1 cách sử dụng [`composer`](https://getcomposer.org).



#Làm thế nào để test không sót case?#
- Lập test case
- Viết test script



#Tự động test trước khi git commit#
Có nhiều cách để thêm trigger cho phép chúng ta tự động chạy script test, ở đây mình xin giới thiệu 1 cách là sử dụng git hook `pre-commit`.
Sau khi đã có đoạn script test, bạn tìm đến thư mục `.git/hooks/` và tạo file `pre-commit` với nội dung như sau:

```
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
```
user@host:/path/to/project $ chmod a+x .git/hooks/pre-commit
user@host:/path/to/project $ git commit -am "Added changes"

Starting unit tests...
All tests for 'Training PHPUnit 01' passed.
 
[master xxxxxxx] Added changes
 1 files changed, 1 insertions(+), 1 deletions(-)
user@host:/path/to/project $
```

Thật tuyệt vời phải không, sao chúng ta không làm việc này thành 1 quy trình sản xuất phần mềm?
Thật ra từ lâu người ta đã nghĩ tới việc này và tạo ra mô hình TDD.

#TDD là cái gì?#
_Định nghĩa của Wikipedia:_
> Test-driven development (TDD) is a software development process that relies on the repetition of a very short development cycle: 
> first the developer writes an (initially failing) automated test case that defines a desired improvement or new function, 
> then produces the minimum amount of code to pass that test, and finally refactors the new code to acceptable standards. 

![TDD Global Lifecycle](https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/TDD_Global_Lifecycle.png/1024px-TDD_Global_Lifecycle.png)*TDD Global Lifecycle*

Các bước trong quá trình TDD:

1. Tạo script test
2. Chạy test và chỉnh sửa script test theo đúng spec
3. Viết code chương trình
4. Chạy test
5. Chỉnh sửa lại code để sửa lỗi
6. Lặp lại các bước khi có thêm yêu cầu

#Tổng kết#
Như vậy chúng ta đã biết cách tạo script test đơn giản với PHPUnit, biết cách thêm trigger tự động test trước mỗi lần commit với git, 
và biết TDD là gì, áp dụng nó và công việc gồm những bước nào. Ở bài sau, cũng ta sẽ cùng trao đổi sâu hơn về cách viết unit test với PHPUnit.

#Bài Tập#
Mình đã tạo 1 repository trên github [https://github.com/rikkeisoft/training-phpunit-01.git](https://github.com/rikkeisoft/training-phpunit-01.git).
Trong đó, mình đã viết sẵn các class unit test với PHPUnit test, nhiệm vụ của các bạn là:

1. Fork repository này về
2. Thực hiện viết nội dung các class để thực hiện yêu cầu
3. Test lại code với PHPUnit
4. Cuối cùng là sẽ tạo Pull request tới repository gốc

#Tài liệu tham khảo#

