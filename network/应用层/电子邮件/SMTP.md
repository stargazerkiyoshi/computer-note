### 简介
- 将一台主机的邮件推到另一台主机
- 是[[email]]中的主要**[[internet协议栈#应用层]]协议**
- 使用[[两种特定的internet运输服务#TCP服务|TCP可靠数据传输服务]]，从发送方[[email#邮件服务器]]发送邮件到接收方邮件服务器
- 所有报文体部分，只能采用7位ASCII码编码（历史原因），多媒体数据也是如此

### 运行地点
- 发送方邮件服务器的SMTP客户端（客户SMTP）
- 接收方邮件服务器的SMTP服务器端（服务器SMTP）
- 每台[[email#邮件服务器]]既运行SMTP客户端也运行SMTP服务器端

### 运行过程
##### 场景
- Alice给Bob发送一封简单的ASCII报文
##### 过程
1. Alice，使用[[email#用户代理|邮件代理]]程序，提供Bob的邮件地址（Bob@some.com）,撰写报文，然后指示发送
2. **Alice的[[email#用户代理|用户代理]]**，把报文发送到她的[[email#邮件服务器|邮件服务器]]中，报文被存放在那里的报文队列中
3. **Alice的[[email#邮件服务器|邮件服务器]]的SMTP客户端**，在队列中发现该报文，创建一个到**Bob的邮件服务器的SMTP服务器端**的TCP连接
4. 经过初始SMTP握手后，**Alice的[[email#邮件服务器|邮件服务器]]的SMTP客户端**通过该TCP连接发送报文
5. **Bob的邮件服务器的SMTP服务器端**接收该报文，服务器将该报文放入Bob邮箱中
6. Bob方便的时候，使用用户代理阅读该报文
![[发送email报文.png]]

### 过程总结
- 服务器双方直接通过TCP连接。SMTP一般不使用中间邮件服务器来发送邮件
- 接收方服务器没有开机，则报文保留在发送方的邮件服务器上，并等待尝试

### 报文从发送邮件服务器传送到接收服务器的过程
1. 客户SMTP（在发送邮件服务器中）在25号端口建立一个到服务器SMTP（在接收邮件服务器中）的TCP连接
2. 检查服务器有没有开机。如果没有开机，则稍后尝试连接
3. 建立连接成功后，执行SMTP应用层的握手动作（自我介绍）
	- 客户SMTP指示发送方邮件地址和接收方的邮件地址
4. 依赖TCP提供的服务，可靠的将邮件投递到接收服务器
5. 如有另外报文要发送到该服务器，则在该TCP连接重复这种处理
6. 指示TCP关闭连接

### 报文格式
- 客户SMTP和服务器SMTP之间交换的报文
- 握手部分协议
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
