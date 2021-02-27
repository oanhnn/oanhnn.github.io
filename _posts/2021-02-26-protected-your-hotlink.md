---
title:    Bảo vệ hotlink của bạn
layout:   post
category: blogs
tags:     [skill]
---

## Bảo vệ Hotlink là gì?

Hotlink là một quá trình sử dụng hình ảnh trang web của bạn, tài sản tĩnh (CSS, Javascript, Phông chữ, v.v.) đến các trang web khác không liên quan đến bạn. Do đó, hiệu suất trang web của bạn sẽ giảm đáng kể. Mỗi khi trang web người dùng khác tải nó sẽ lấy tài nguyên từ trang web của bạn.

Không cần phải lo lắng vì chúng ta có thể ngăn chặn nó bằng phương pháp sau


## Triển khai trong Máy chủ Web Nginx

```nginx
valid_referers none blocked yoursite.com *.yoursite.com;
if ($invalid_referer) {
    return   403;
}
```

VD: Đoạn mã triển khai đầy đủ trong Máy chủ Web Nginx

```nginx
location ~* \.(gif|otf|jpg|jpeg|png|css|js)$ {
    valid_referers none blocked yoursite.com *.yoursite.com;
    if ($invalid_referer) {
        return   403;
    }
    add_header Cache-Control public;
    add_header Pragma public;
    add_header Vary Accept-Encoding;
    expires max;
}
```

## Tham khảo:

- https://stackcoder.in/posts/hotlink-protection-in-nginx (bài gốc)
- https://nginx.org/en/docs/http/ngx_http_referer_module.html#valid_referers
