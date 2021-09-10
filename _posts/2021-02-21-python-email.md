---
layout: post
title: "使用Python发邮件"
date: 2021-02-23
description: "使用Python发邮件"
tag: Python
---

# 一、Yagmail
 yagmail 是一个第三方库，相比于标准库来说，会比较简洁：
 ```python
 import yagmail
ya = yagmail.SMTP(user = 'sender_email', password = 'sender_pwd',
host =' smtp.partner.outlook.cn',port =25, smtp_starttls = True, smtp_ssl = False)
#prepareing ur content
#using the method 'send' ,要发送给多个人 用list
ya.send(to = 'receiver_email', cc = ''cc_email',bcc = 'bcc_email',
subject = 'ur subject', contents = cont,attachments = att)
 ```

# 二、email
虽然三方库很好用，但是有时候会出一些bug；所以标准库更加稳定：
```python
#1.导入相关的包
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email.mime.image import MIMEImage
from email.mime.application import MIMEApplication

#创建一个带附件的邮件消息对象
msg = MIMEMultipart()

#创建文本内容
content = '附件中有本月数据请查收'
text_content = MIMEText(content, 'plain', 'utf-8')
# 将文本内容添加到邮件消息对象中
msg.attach(text_content)

#读取文件并将文件作为附件添加到邮件消息对象中
with open('data.xlsx','rb') as f:
	file = MIMEApplication(f.read())
	file.add_header('Content-Disposition','attachment',filename=('gbk','','data.xlsx'))
	msg.attach(file)

#创建SMTP对象
server= smtplib.SMTP('smtp.163.com')
    # 如果使用世纪互联运营的outlook邮箱，它使用的是SMTP TLS加密
    # smtper = SMTP('smtp.partner.outlook.cn', 587)
    # smtper.starttls()
sender = 'example@163.com'  # 发件人
receivers = ['example@qq.com', 'example@icloud.com']  # 收件人
cc = ['example@qq.com', 'example@icloud.com']  # 抄送人
bcc = ['example@qq.com', 'example@icloud.com']  # 密送人 
msg['From'] = sender
msg['To'] = ';'.join(receivers)
msg['Cc'] = ';'.join(cc)
msg['Bcc'] = ';'.join(bcc)
mas['Subject'] = 'test'
server.login(sender,'password')
server.sendmail(sender,receiver+cc+bcc,msg['Subject'],msg.as_string())
server.quit() #断开smtp连接
print('successful')


```

