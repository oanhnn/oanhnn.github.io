---
title:    Hiểu biết căn bản về Git (p2)
layout:   post
category: tutorial-tips
tags:     [skill, git, dev-ops]
---

Trong [phần trước][prev-post] mình đã giới thiệu một số khái niệm căn bản trong git cho các bạn.
Ở bài này, mình sẽ giới thiệu tiếp các khái niệm về chia sẻ repository và merge lịch sử thay đổi.

<!--more-->

![Làm việc với remote repository](http://backlogtool.com/git-guide/vn/img/post/intro/capture_intro1_2_2.png)

## Clone một remote repository

Nếu có ai đó chia sẻ một repository của họ, bạn có thể lấy lịch sử thay đổi của nó
về máy của mình để có thể thao tác trên phần công việc đó. Thao tác sao chép này
người ta gọi là **clone**. Repository được chia sẻ được gọi là remote repository.

Khi thực hiện clone, toàn bộ nội dung và lịch sử thay đổi của remote repository
sẽ được tải về thành local repository.

> Tip: Vì trong local repository đã clone về, lịch sử thay đổi cũng được sao chép
> nên có thể tham chiếu lịch sử hoàn toàn giống với repository gốc và có thể commit.

## Set một remote repository

Để có thể chia sẻ local repository của mình, bạn cần trỏ nó vào một remote repository nào đó.
Việc trỏ này có thể thay đổi, thêm hoặc xóa đi khi bạn muốn.

> Tip: Việc trỏ vào một remote repository đã có sãn các lịch sử thay đổi có thể làm bạn
> gặp xung đột khi upload phần lịch sử thay đổi của local repository lên remote repository
> hoặc tải phần lịch sử thay đổi của remote repository về.

## Push lên remote repository

Để chia sẻ lịch sử thay đổi của local repository mà mình đang có lên remote repository,
cần phải upload lịch sử thay đổi trong local repository.

Thao tác upload lịch sử thay đổi từ local repository lên remote repository được gọi là **push**.
Sau khi thực hiện push, các lịch sử thay đổi của bạn sẽ được upload lên remote repository và
lịch sử thay đổi của remote repository sẽ có trạng thái giống với local repository của bạn.

![Push lên remote repository](http://backlogtool.com/git-guide/vn/img/post/intro/capture_intro3_1_1.png)

## Pull từ remote repository

Khi chia sẻ remote repository và nhiều người cũng thực hiện công việc, thì mọi người
sẽ push lên remote repository. Khi đó, cần lấy nội dung thay đổi mà người khác đã push lên
đem vào local repository của mình.

Để cập nhật local repository từ remote repository thì thực hiện thao tác gọi là **pull**.

Khi thực hiện pull, sẽ tải lịch sử thay đổi mới nhất từ remote repository về,
rồi đưa nội dung đó vào local repository của mình.

![Pull từ remote repository](http://backlogtool.com/git-guide/vn/img/post/intro/capture_intro3_3_1.png)

## Merge lịch sử thay đổi

Trường hợp trong khoảng thời gian từ sau khi pull lần cuối cho đến khi push lần tiếp theo,
mà có người khác lỡ push rồi cập nhật remote repository, thì push của chính mình sẽ bị từ chối.

![Push bị từ chối](http://backlogtool.com/git-guide/vn/img/post/intro/capture_intro5_1_1.png)

Trong trường hợp này, cho đến khi thực hiện merge rồi tiếp nhận thay đổi của phần lịch sử khác,
thì việc bản thân push lên sẽ bị từ chối. Lý do là vì khi không merge mà cứ ghi đè lên lịch sử,
thì những thay đổi mà người khác đã push (commit C trong sơ đồ) sẽ bị mất.

![Push sau khi đã merge](http://backlogtool.com/git-guide/vn/img/post/intro/capture_intro5_1_2.png)

Bằng cách merge, Git sẽ tự động tích hợp chổ thay đổi! Nhưng, cũng có trường hợp không thể tự tích hợp được.

## Giải quyết xung đột

Như đã giải thích ở trang trước, thì khi thực hiện merge, Git sẽ tích hợp tự động
những chỗ thay đổi. Tuy nhiên, cũng có trường hợp không thể tích hợp tự động được.

Đó là trường hợp đã thay đổi ở nơi giống nhau trong file trên remote repository và local repository.
Trường hợp này, vì nó không thể tự phán đoán được lấy thay đổi từ bên nào nên sẽ phát sinh lỗi.

Những nơi phát sinh xung đột, Git sẽ chỉnh sửa nội dung file giống như sơ đồ bên dưới,
nên cần phải chỉnh sửa bằng tay.

![Nội dung bị xung đột](http://backlogtool.com/git-guide/vn/img/post/intro/capture_intro5_1_3.png)

> Tip: Phần phía trên mà đã được chia ra bằng == là local repository,
> phía dưới được hiển thị là nội dung chỉnh sửa của remote repository.

Giống như sơ đồ tiếp theo, sau khi chỉnh sửa tất cả chỗ xung đột, nếu như tiến hành commit
thì commit đã tích hợp thay đổi sẽ được tạo ra.

![Nội dung sau khi đã giải quyết hết xung đột](http://backlogtool.com/git-guide/vn/img/post/intro/capture_intro5_1_4.png)

Qua hai bài, mình đã giới thiệu với các bạn những khái niệm căn bản về git để các bạn
có thể dễ dàng hơn khi tiếp cận với nó.   
Hiện nay có rất nhiều tool có giao diện cũng như dòng lệnh hỗ trợ bạn làm việc với git.
Các bạn có thể tham khảo thêm tài liệu hướng dẫn sử dụng chúng.

**Chúc các bạn thành công!**

[prev-post]: /2016-05-11/basic-knowledge-about-git.html
