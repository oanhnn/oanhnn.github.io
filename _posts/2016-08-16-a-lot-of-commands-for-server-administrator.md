---
title:    Một vài lệnh Linux cho các nhà quản trị máy chủ
layout:   post
category: tutorial-tips
tags:     [linux]
feature:  /assets/img/terminal.gif
---
Trong bài này, mình chia sẻ với các bạn một số lệnh cơ bản dành cho các nhà quản trị máy chủ.

<!--more-->

## Các lệnh kiểm tra thông số máy chủ

Lệnh kiểm tra CPU:

```
$ cat /proc/cpuinfo
```

Lệnh theo dõi CPU:

```
$ top -c
```

Lệnh kiểm tra hệ điều hành họ CentOS - RHEL

```
$ uname -a
$ cat /etc/redhat-release
```

Lệnh kiểm tra RAM

```
$ free -m
```

Lệnh kiểm tra HDD

```
$ df -h
```

Lệnh kiểm tra tốc độ đọc ghi (I/O) HDD (thay 1024k theo dung lượng bạn muốn)

```
$ dd if=/dev/zero of=1GB.tmp bs=1024 count=1024k conv=fdatasync
```

Lệnh kiểm tra CPU, RAM, test speed và IO

```
wget freevps.us/downloads/bench.sh -O - -o /dev/null|bash
```

Để kiểm tra tốc độ Network, các bạn có thể sử dụng công cụ [Speedtest](speedtest).

## Tìm kiếm toàn bộ file PHP trên Linux

Lệnh này rất hữu ích khi bạn muốn check các file sửa gần đây nhất.

```
$ find . -type f -name '*.php' -printf '%TY-%Tm-%Td %TT %p\n' | sort
```

## Một số lệnh cơ bản kiểm tra server khi bị DDoS

Tấn công từ chối dịch vụ phân tán (DDoS – Distributed Denial Of Service) là kiểu tấn công 
làm cho hệ thống máy tính hay hệ thống mạng quá tải, không thể cung cấp dịch vụ hoặc 
phải dừng hoạt động. Trong các cuộc tấn công DDoS, máy chủ dịch vụ sẽ bị **ngập**
bởi hàng loạt các lệnh truy cập từ lượng kết nối khổng lồ.

Khi số lệnh truy cập quá lớn, máy chủ sẽ quá tải và không còn khả năng xử lý các yêu cầu.
Hậu quả là người dùng không thể truy cập vào các dịch vụ trên các trang web bị tấn công DDoS.

Đếm lượng connection vào Port 80:

```
$ netstat -n | grep :80 |wc -l
```

Kiểm tra số lượng connection đang ở trạng thái SYN_RECV:

```
$ netstat -n | grep :80 | grep SYN_RECV|wc -l
```

Hiển thị tất cả các IP đang kết nối và số lượng kết nối từ mỗi IP:

```
$ netstat -an|grep :80 |awk '{print $5}'|cut -d":" -f1|sort|uniq -c|sort -rn
```

Nếu muốn kiểm tra IP nào mở nhiều SYN thì thêm vào:

```
$ netstat -an|grep :80|grep SYN |awk '{print $5}'|cut -d":" -f1|sort|uniq -c|sort -rn
```

Đối với server có nhiều IP, để kiểm tra IP nào đang bị tấn công:

```
$ netstat -plan | grep :80 | awk '{print $4}'| cut -d: -f1 |sort |uniq -c
```

Hiển thị tất cả các IP đang kết nối và số lượng kết nối từ mỗi IP:

```
$ netstat -an | grep ':80' | awk '{print $5}' | sed s/'::ffff:'// | cut -d":" -f1 | sort | uniq -c
```

Hiển thị số lượng kết nối mỗi loại

```
$ netstat -an | grep :80 | awk '{print $6}' | sort | uniq -c
 61 ESTABLISHED
 13 FIN_WAIT1
 17 FIN_WAIT2
 1 LISTEN
 25 SYN_RECV
 298 TIME_WAIT
```

Hiển thị tất cả các IP đang kết nối và số lượng kết nối từ mỗi IP

```
$ watch "netstat -an | grep ':80' | awk '{print \$5}' | sed s/'::ffff:'// | cut -d\":\" -f1 | sort | uniq -c"
$ watch "netstat -an | grep :80 | awk '{print \$6}' | sort | uniq -c"
```

Khi đã phát hiện IP có dấu hiệu bất thường, bạn có thể sử dụng [CSF](csf) để block IP đó lại.



[csf]:          http://hocvps.com/cau-hinh-nang-cao-cho-csf-de-bao-ve-vps/
[speedtest]:    http://hocvps.com/test-toc-do-vps-ve-viet-nam/
