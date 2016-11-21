---
title:    Tích hợp liên tục (CI) là cái gì?
layout:   post
category: tutorial-tips
tags:     [skill, dev-ops]
feature:  /assets/img/ci.jpg
---

Với nhiều bạn khái niệm Continuous Integration (Tích hợp liên tục - CI) là một cái gì đó xa lạ.
Hôm nay mình sẽ giúp các bạn hiểu thế nào là CI và vận dụng nó vào trong công việc phát triển phần mềm.

<!--more-->

## CI là gì?

> Tích hợp liên tục (CI) là phương pháp phát triển phần mềm đòi hỏi các thành viên trong nhóm
> tích hợp công việc thường xuyên. Mỗi ngày, các thành viên đều phải theo dõi và phát triển
> công việc của họ ít nhất một lần. Việc này sẽ được một nhóm khác kiểm tra tự động,
> nhóm này sẽ tiến hành kiểm thử truy hồi để phát hiện lỗi nhanh nhất có thể. Cả nhóm thấy rằng
> phương pháp tiếp cận này giúp giảm bớt vấn đề về tích hợp hơn và cho phép phát triển phần mềm gắn kết nhanh hơn
> --- **[Thư viện phát triển phần mềm của IBM][ibm]**

![Continuous Integration](/assets/img/ci.jpg)*Vòng đời phát triển phần mềm theo phương pháp CI*

## Tại sao nên dùng CI ?

Sử dụng phương pháp CI giúp cho hệ thống luôn đảm bảo là build được và chạy đúng 
(do phải pass qua toàn bộ test case). Mặt khác các công đoạn test sẽ được hệ thống CI server 
thực hiện tự dộng giúp cho ta có thể dễ dàng biết được tình trạng của một branch, 
một commit nào đó và không cần lấy source về test thử. Do đó tốc độ phát triển được tăng lên.
Đây cũng là lý do mà nhiều team phát triển theo mô hình Agile lựa chọn phương pháp này.

Hiện tại các công ty lớn thường triển khai các hệ thống CI cho riêng mình hoặc sử dụng dịch vụ của hãng thứ 3.
Một số framework được sử dụng phổ biến như:

- Team Foundation Server - Microsoft
- TeamCity - Jet Brains
- Hudson
- Jenkin
- Travis CI
- GitLab CI

Ngoài ra còn nhiều dịch vụ khác, bạn có thể tham khảo tại [đây][ci-services]

## Workflow của CI

Workflow của phương pháp CI được chia thành 6 bước như sau:

**Bước 1** Các người phát triển sẽ đưa phần source mà họ đã thay đổi lên Source Control Server. 
Khi đó Source Control Server sẽ thông báo với CI Server là có bản source mới.

**Bước 2** CI Server lấy source mới về

**Bước 3, 4 & 5** CI Server sẽ build, test và cập nhật lại trạng thái build của bản source đó.

**Bước 6** CI Server sẽ thông báo đến người quản lý và các người phát triển về tình trạng của bản code mới.

![Continuous Integration Workflow](/assets/img/ci-workflow.jpg)*Workflow của phương pháp CI*

## Làm việc với Travis CI

## Làm việc với GitLab CI

## Resources
<i class="fa fa-file-powerpoint-o"></i> [Slide][slide-03]   
<i class="fa fa-file-code-o"></i> [Source code][source]

## Tài liệu tham khảo:
- [Bài viết của Martin Folwer][martin]
- [Thư viện phát triển phần mềm của IBM][ibm]
- [Slide giới thiệu về Travis CI][slide-01]
- [Slide hướng dẫn tích hợp Travis CI với PHP][slide-02]

[martin]:   http://www.martinfowler.com/articles/continuousIntegration.html
[ibm]:      http://www.ibm.com/developerworks/vn/library/rational/201301/continuous-integration-agile-development/
[slide-01]: https://speakerdeck.com/rkh/travis-ci
[slide-02]: http://www.slideshare.net/mostofreddy/travisci-continuos-integration-in-the-cloud-for-php
[slide-03]: /slides/2016-07-08-continuous-integration.html
[source]:   https://github.com/oanhnn/slim-skeleton
[ci-services]: https://github.com/ligurio/Continuous-Integration-services/blob/master/continuous-integration-services-list.md
