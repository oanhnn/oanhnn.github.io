---
title:    Cách tạo ảnh GIF từ một tập hợp các ảnh
layout:   post
category: tutorial-tips
tags:     [linux]
feature:  /assets/img/ImageMagick-logo.png
---
Bạn đang có một loạt ảnh tĩnh (png, jpeg image) và bạn muốn tạo 1 cái ảnh động (gif image) từ chúng?   
Tôi xin giới thiêu với các bạn đang dùng Linux một cách đơn giản đó là dùng ImageMagick.

<!--more-->

1. Đầu tiên là bạn phải cài gói [ImageMagick](http://www.imagemagick.org/script/index.php)

2. Đổi tên các file ảnh thành dạng `<số thứ tự>.<định dạng>` .
   Ví dụ mình có 25 cái ảnh `png` và mình sẽ đổi tên thành: `001.png`, `002.png`, ..., `025.png` .

3. Resize ảnh về chung kích thước

   ```
   $ convert *.png -resize 800x -set filename:area "%t" /path/to/your-save-dir/%[filename:area].png
   ```

   > **Chú ý:** %t là thay thế cho tên của file gốc

4. Tạo ảnh động

   ```
   cd /path/to/your-save-dir && convert -delay 100 -loop 0 *.png funny.gif
   ```

   > **Chú ý:** delay có đơn vị là mili giây, loop bằng 0 có nghĩa là lặp vô hạn

   Vậy là bạn đã có một ảnh động với tên là `funny.gif`
