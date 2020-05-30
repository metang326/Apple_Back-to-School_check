# Apple_Back-to-School_check
Apple暑期返校送Beats耳机活动监控，发现活动开始后通过邮件的方式提醒

最近考虑换电脑，一般来说7月的时候苹果会推出教育优惠买MacBook送Beats耳机的活动，为了第一时间发现活动，挑到颜色好看的耳机，写了一个小脚本定时监控教育商店的主页（https://www.apple.com.cn/cn-k12/shop）
看看有没有更新了关键词“耳机”或者“Beats” 

相比于之前写Nike定制监控的脚本（https://github.com/metang326/nike_by_you_check）
这次尝试了一下python发送邮件提醒，居然非常简单，用的库也是自带的（我使用的是python3.7）

步骤：申请一个新邮箱，在【设置】页面的POP3/SMTP/IMAP标签，给它开启IMAP/SMTP服务，邮箱系统会给一个密码，把它填入下面的password即可。smtp_server是发件邮箱的地址，如果是163或者qq邮箱是不同的，在邮箱系统的页面上会标注的。

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
import requests
import datetime
import time
import smtplib
from email.mime.text import MIMEText
from email.header import Header


def alarm_email():
    from_addr = '发件邮箱@163.com'
    password = '邮箱系统开启IMAP/SMTP服务后提供的密码'
    to_addr = '收件邮箱@qq.com'
    smtp_server = 'smtp.163.com'
    msg = MIMEText('正文', 'plain', 'utf-8')
    msg['From'] = Header(from_addr)
    msg['To'] = Header(to_addr)
    msg['Subject'] = Header('标题')
    server = smtplib.SMTP_SSL()
    server.connect(smtp_server, 465)
    server.login(from_addr, password)
    server.sendmail(from_addr, to_addr, msg.as_string())
    server.quit()
    print('已发送邮件')
```

完整的监控代码在https://github.com/metang326/Apple_Back-to-School_check/blob/master/check.py

计划把这个脚本设置成系统的定时任务，每天定时执行几次。linux增加crontab定时任务的方法博客里有写：

https://metang326.github.io/2020/02/03/[linux]linux%E5%A2%9E%E5%8A%A0crontab%E5%AE%9A%E6%97%B6%E4%BB%BB%E5%8A%A1/
