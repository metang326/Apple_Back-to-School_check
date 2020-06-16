# Apple_Back-to-School_check
Apple暑期返校送耳机活动监控，发现活动开始后通过邮件的方式提醒。

相比于之前写Nike定制监控的脚本 https://github.com/metang326/nike_by_you_check 这次尝试了一下python发送邮件提醒，居然非常简单，用的库也是自带的（我使用的是python3.7）

2020/06/16更新

美国官网已更新了活动，万万没想到，今年送的是AirPods，清库存操作。

## 使用流程
1. git clone代码到本地
2. 把代码中的邮箱与密码换成自己的
3. 把执行脚本的语句添加到Linux的定时任务

## 代码流程
- 抓取教育商店的主页 https://www.apple.com.cn/cn-k12/shop 
- 看看有没有更新了关键词“耳机”或者“Beats”或者“AirPods”
  - 如果发现这个活动开始了，发邮件通知自己；
  - 如果没有开始，则每天八点的时候邮件自己一次，通知“活动还没有开始”，确保监控是正常运行的

## code
https://github.com/metang326/Apple_Back-to-School_check/blob/master/check.py

## 如何给自己发送邮件
步骤：申请一个新邮箱，在【设置】页面的POP3/SMTP/IMAP标签，给它开启IMAP/SMTP服务，邮箱系统会给一个密码，把它填入下面的password即可，其他的收件邮箱、发件邮箱等等换成自己的，正文和标题也可以自己随便定义。smtp_server是发件邮箱的地址，如果是163或者qq邮箱是不同的，在邮箱系统的页面上会标注的。
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

## 如何设置定时任务
把这个脚本设置成系统的定时任务，每天定时执行几次。linux增加crontab定时任务的方法博客里有写：

https://metang326.github.io/2020/02/03/[linux]linux%E5%A2%9E%E5%8A%A0crontab%E5%AE%9A%E6%97%B6%E4%BB%BB%E5%8A%A1/

我是设置了每小时check一次

> sudo vim /etc/crontab

增加下面这行：

> 12 */1  * * *   root    python3 /home/ivic/tmy_repos/Apple_Back-to-School_check/check.py

然后重启服务

> sudo /etc/init.d/cron restart

> [ ok ] Restarting cron (via systemctl): cron.service.


这样就是每小时的12分这个时刻，执行一下检测脚本，如果发现官网里出现了关键词就发送邮件进行通知；否则的话就直接结束。

## 测试
可以先通过在代码手动增加一下关键词，进行测试，然后发现准时收到了提醒邮件，Done！

> text += "Beats耳机"
