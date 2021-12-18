### 客户SMTP和服务器SMTP之间交换的报文格式
- 握手协议
##### 场景
- 客户SMTP：C
- 服务器SMTP：S
- 客户主机名：crepes.fr
- 服务器主机名：hamburger.edu
- 当创建了TCP连接后产生如下报文

##### 报文例子
```
S:  220 hamburger.edu
C:  HELO crepes.fr
S:  250 Hello crepes.fr, pleased to meet you
C:  MAIL FROM: ‹alice@crepes.fr>
S:  250 alice@crepes.fr ... Sender ok
C:  RCPT TO: <bob@hamburger.edu>
S:  250 bob@hamburger.edu ... Recipient ok
C:  DATA
S:  354 Enter mail, end with "." on a line by itself
C:  Do you like ketchup?
C:  How about pickles?
C:  .
S:  250 Message accepted for delivery
C:  QUIT
S:  221 hamburger.edu closing connection
```

##### 报文说明
- C: 开头的文本行是客户交给其TCP套接字的行
- S: 开头的文本行是服务器发送给其TCP套接字的行
- 使用了命令：HELO（即Hello）、MAIL FROM、RCPT TO、DATA、QUIT命令。顾名思义
- 客户邮件服务器发送了 ” Do you like ketchup?“、”How about pickles?“的报文
- 客户发送一个只包含"."的行，表示该报文结束（ASCII码中，每个报文以CRLF.CRLF结束，CR和LR分别表示回车和换行）
- 服务器对每个命令进行回答，含有回答码和可选的英文解释
- 当有几个报文同时发往同一个服务器时，由于用的持续连接，所以用一个TCP连接
- 每个报文用一个新的MAIL FROM开始，用一个独立行的”.“指示该邮件结束
- 所有邮件都发完后，用QUIT指示结束

### Telnet实验
```
telnet serverName 25
```












