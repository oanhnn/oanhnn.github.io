---
title:    Một vài kinh nghiệm với crontab
layout:   post
category: tutorial-tips
tags:     [skill, dev-ops]
feature:  /assets/img/crontab.png
---

Cron và crontab chắc là không còn xa lạ gì với anh em developers nhà mình rồi đúng ko?
Tuy nhiên để lập lịch cho một công việc hay sửa đổi nó cũng không hề đơn giản, đôi khi
cũng tốn không ít thời gian của anh em. Bản thân mình cũng vậy, nhiều lúc cũng toát
cả mồ hôi. Hôm nay mình chia sẻ với anh em một vài kinh nghiệm khi làm việc với nó.

<!--more-->

Đầu tiên cho phép mình nhắc lại kiến thức cơ bản một chút.

> The software utility Cron is a time-based job scheduler in Unix-like computer operating systems.
> Crontab is tools to manage Cron.

Syntax của một scheduled job đây:

![Syntax của scheduled job](/assets/img/crontab-01.gif)*Syntax của scheduled job*

Đôi khi còn có thể chỉ định user sẽ chạy:

![Syntax của scheduled job khi chỉ định user](/assets/img/crontab-02.png)*Syntax của scheduled job khi chỉ định user*

Cụ thể hơn thì bạn google nhé!   
Bây giờ mình sẽ giới thiệu ít kinh nghiệm với crontab

### Biến môi trường

Đầu tiện bạn phải nhớ là biến môi trường của cron khi chạy một job sẽ khác với việc
bạn chạy job đó bằng SHELL của bạn. Đây là đáp án của đa phần các câu hỏi:

> Sao mình chạy trong SHELL được mà cho vào crontab thì nó lại chết?

Vì vậy việc làm cho môi trường thực thi của cron giống với của bạn là điều cần thiết.
Và bạn cũng cần phải control các biến môi trường cho tường job của mình.

Đa phần mọi người sẽ biết như sau:

```
ENV_VAR=foo
OTHER_VAR=bar

10 * * * * NODE_ENV=production /path/to/node /path/to/my/script.js
# or shorter
05 * * * * /path/to/laucher.sh
```

Nhưng những cách đó là chưa tốt. Bạn nên tạo file `cron.bash` với nội dung như sau:

```
#!/bin/bash
set -e

source /etc/environment
source /etc/profile
# $HOME is available, but not many other env vars are by default
source $HOME/profile

# setup any env variables you want here
export NODE_ENV=production
export ENV_VAR=foo
export OTHER_VAR=bar

# restore SHELL env var for cron
SHELL=/bin/bash
# execute the cron command in an actual shell
exec /bin/bash --norc "$@"
```

Và cuối cùng là set lại cái SHELL của cron thành file này:

```
SHELL=/path/to/cron.bash
MAILTO=alarms@example.com

10 * * * * node /path/to/my/script.js
05 * * * * /path/to/exec_script.sh
```

Thật là gọn gàng và lại giống với SHELL của chúng ta vẫn dùng. Mặt khác, khi bạn
có thêm biến môi trường vào `$HOME/profile` , bạn cũng không phải vào sửa trong crontab nữa.
Thật là tiện phải không?

### Tổ chức scheduled jobs

Bạn nên tổ chức scheduled jobs của mình một cách khoa học theo các bước sau:

- Viết thêm số 0 và bên trái các số có 1 chữ số trong setting thời gian của scheduled jobs
- Viết các scheduled jobs theo nhóm chức năng hay theo chu kỳ thực hiện
- Sử dụng comment để disable scheduled jobs thay cho việc xoá dòng đó. Như vậy bạn sẽ
  dễ dàng sử dụng lại khi cần. Nhớ thêm ít comment vào để còn nhớ tại sao mình disable.

```
#bad
0 * * * * script1.sh param1
5 5 * * * script2.sh
15 5 * * 0 script3.sh param1 param2
5 10 * * * script4.sh

15 * * * * script5.sh param1
5,35 15 * * * script6.sh
15 15 * * 0 script7.sh

#good

# Every hour
# -----------
00 * * * * script1.sh param1
15 * * * * script5.sh param1

# Every day
# -----------
05    05 * * * script2.sh
05    10 * * * script4.sh
05,35 15 * * * script6.sh

# oanhnn 20161120: paused cause waiting for bla bla bla
#10 04 * * * script8.sh

# Every sunday
# -----------
15 05 * * 00 script3.sh param1 param2
15 15 * * 00 script7.sh

```

### Lưu lại output

Trong đa số trường hợp, chúng ta thường sử dụng `> /dev/null 2>&1` để có thể bỏ qua
output và lỗi của scheduled jobs. Tuy nhiên, trong một số trường hợp, bạn cần lưu lại
output của chúng để tiện cho việc debug hay tìm lỗi, khắc phục sự cố, ... Lúc đó thì
mình khuyên các bạn nên sử dụng lệnh `date` để có thể chia nhỏ file log ra. Nó sẽ rất
tiện cho bạn đấy!

```
00 10 * * * script1.sh > /dev/null 2>&1
00 05 * * * script.sh >> /my/log_`date +\%y\%m\%d`.log 2>&1
```

### Tham khảo:

- http://shtylman.com/post/cron-shell-power/
- http://www.logikdev.com/2010/05/25/using-the-date-command-in-your-crontab/
