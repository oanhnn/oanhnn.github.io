---
title:    Sử dụng wget để tải một thư mục trên http
layout:   post
category: tutorial-tips
tags:     [skill, dev-ops]
feature:  /assets/img/i-love-php.jpg
---

Đôi khi bạn muốn tải về tất cả các tài liệu mà thầy giáo bạn cho dưới dạng một thư mục. 
Bạn đang lay hoay tìm phần mềm để tải về? Tại sao bạn ko thử dùng ngay wget?

<!--more-->

![Duyệt thư mục qua http](/assets/img/directory-http.png)*Duyệt thư mục qua http*

Để tải một thư mục, bạn có thể sử dụng wget:

```
wget -r --no-parent http://example.com/path_to_dir/
```

Trong lệnh trên:

- Tham số `-r` là để download tất cả các files trong thư mục và các thư mục con.
- Tham số `--no-parent` là để nó sẽ theo đường dẫn từ link, không tải các file từ 
  thư mục cha của thư mục bạn muốn tải.

Nếu bạn muốn bỏ các file index.html được tạo ra ở mỗi thư mục thì các bạn dùng lệnh:

```
wget -r --no-parent --reject "index.html*" http://example.com/path_to_dir/
```

Nếu các bạn không muốn giữ cấu trúc thư mục từ trên site, các bạn có thể dùng lệnh sau:

```
wget -r -nH -nd -np -R "index.html*" http://example.com/path_to_dir/
```

Chúc các bạn thành công!