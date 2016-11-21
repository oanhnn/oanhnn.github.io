---
title:    Hiểu biết căn bản về Git (p1)
layout:   post
category: tutorial-tips
tags:     [skill, git, dev-ops]
---

Trong quá trình phát triển phần mềm, chắc hẳn chúng ta không thể nào tránh được việc quản lý các phiên bản của mã nguồn.
Sử dụng các hệ thống quản lý phiên bản sẽ giúp bạn quản lý, khôi phục các phiên bản mã nguồn cũ, làm việc nhóm một cách hiệu quả.
Git là một trong những hệ thống quản lý phiên bản phân tán. Nó đang đuợc nhiều khách hàng của chúng ta lựa chọn sử dụng.
Hôm nay mình sẽ giới thiệu các khái niệm cơ bản về Git để khi bước vào dự án có sử dụng Git, các bạn khỏi bỡ ngỡ.

<!--more-->

![Xung đột mã nguồn trong làm việc nhóm](http://backlogtool.com/git-guide/vn/img/post/intro/capture_intro1_1_2.png)

## Repository quản lý lịch sử

Repository là nơi sẽ ghi lại trạng thái của thư mục và file. Tại đây, nó được lưu lại như những lịch sử thay đổi của nội dung.
Bằng việc đặt thư mục muốn quản lý lịch sử thay đổi dưới sự quản lý của repository,
bạn có thể ghi chép lại lịch sử thay đổi của thư mục và file trong thư mục đó.

![Repository quản lý lịch sử thư mục và file](http://backlogtool.com/git-guide/vn/img/post/intro/capture_intro1_2_1.png)

## Remote repository và local repository

Đầu tiên, repository của Git được phân thành 2 loại là remote repository và local repository.

- Remote repository: Là repository để chia sẻ giữa nhiều người và bố trí trên server chuyên dụng.
- Local repository: Là repository bố trí trên máy của bản thân mình, dành cho một người dùng sử dụng.

Do repository phân thành 2 loại là local và remote nên với những công việc bình thường
thì có thể sử dụng local repository và thực hiện trên toàn bộ máy sẵn có. Khi muốn công khai
nội dung công việc mà bản thân đã làm trên local repository, thì sẽ upload lên remote repository
rồi công khai. Thêm nữa, thông qua remote repository cũng có thể lấy về nội dung công việc của người khác.

![Làm việc với remote repository](http://backlogtool.com/git-guide/vn/img/post/intro/capture_intro1_2_2.png)

## Tạo repository

Có 2 cách tạo local repository:

- Tạo repository hoàn toàn mới
- Sao chép từ remote repository (clone)

![Tạo repository](http://backlogtool.com/git-guide/vn/img/post/intro/capture_intro1_2_3.png)

## Commit sẽ ghi lại thay đổi

Để ghi lại việc thêm/ thay đổi file hay thư mục vào repository thì sẽ thực hiện thao tác gọi là Commit.

Khi thực hiện commit, trong repository sẽ tạo ra commit (hoặc revision) đã ghi lại
sự khác biệt từ trạng thái đã commit lần trước đến trạng thái hiện tại.

Commit này đang được chứa tại repository trong trạng thái đã nối tiếp với nhau
theo thứ tự thời gian như trong hình dưới đây. Bằng việc lần theo commit này
từ trạng thái mới nhất thì có thể hiểu được lịch sử thay đổi trong quá khứ hoặc nội dung thay đổi đó.

![Lưu trữ các commit](http://backlogtool.com/git-guide/vn/img/post/intro/capture_intro1_3_1.png)

Các commit này, được đặt tên bởi 40 ký tự alphabet không trùng nhau đã được tính toán
từ thông tin commit. Bằng việc chỉ định tên này, có thể chỉ định commit từ trong repository.

> **Tip:** Những thay đổi mang ý nghĩa khác nhau chẳng hạn như thêm chức năng hay
> sửa lỗi thì hãy cố gắng chia ra rồi commit. Để sau này khi xem lịch sử và tìm kiếm
> một nội dung thay đổi định sẵn sẽ dễ dàng hơn.

Khi thực hiện commit có yêu cầu nhập giải thích commit (commit message).
Vì commit message là bắt buộc nên nếu để trống mà thực hiện thì commit sẽ thất bại.

> **Tip:** Vì phần giải thích đó sẽ trở thành thông tin quan trọng khi bản thân
> xem lại lịch sử sau đó hay trường hợp tìm kiếm nội dung thay đổi mà người khác commit,
> nên hãy chú ý để viết giải thích sao cho dễ dàng hiểu được nội dung thay đổi.
> Viết giải thích theo hình thức tiêu chuẩn trên Git như sau:   
> **Dòng thứ 1**: Tóm tắt nội dung thay đổi trong commit   
> **Dòng thứ 2**: Dòng trống   
> **Dòng thứ 3 trở đi**: Lý do đã thay đổi   

## Ghi lại thay đổi vào repository: Working Tree và Index

Trên Git, những thư mục được đặt trong sự quản lý của Git mà mọi người đang thực hiện công việc trong thực tế được gọi là working tree.

Và trên Git, giữa repository và working tree tồn tại một nơi gọi là index. Index là nơi để chuẩn bị cho việc commit lên repository.

![Ghi lại thay đổi vào repository](http://backlogtool.com/git-guide/vn/img/post/intro/capture_intro1_4_1.png)

Trên Git, khi đã thực hiện commit thì trạng thái sẽ không được ghi trực tiếp trong repository từ working tree,
mà sẽ ghi trạng thái đã được thiết lập của index được xây dựng ở giữa đó.
Vì thế, để ghi lại trạng thái của file bằng commit thì trước hết cần đăng ký file trong index.

Với việc chèn index vào giữa như thế này, bạn có thể thực hiện commit không bao gồm
những file không cần thiết trong working tree, hay có thể đăng ký chỉ một phần thay đổi của file trong index rồi commit.

*-- [còn tiếp][next-post] --*

[next-post]: /2016-08-17/basic-knowledge-about-git-p2.md.html
