### 简介
- 域名系统，Domain Name System
- 由RFC 1034和RFC 1035定义
- 主要是一种能进行主机名到IP地址的目录服务
- 详情见[[#提供服务]]
### 主机在网络中标识的方法
##### 主机名
- hostname
- 类似于人名
- 如www.baidu.com、www.google.com
- 便于记忆
- 很少提供主机在internet中的位置信息
##### IP地址
- IP address
- 由4个字节组成
- 有严格的层次结构，越来越具体的关于主机位于internet何处的信息
- 计算机更方便的一种表示方式

### 组成
##### DNS服务器
- 一个分布式数据库，由分层的DNS服务器（DNS server）实现的
- DNS服务器是运行BIND（Berkeley Internet Name Domain）软件的UNIX机器
##### DNS客户端
- DNS应用的客户端
##### DNS协议
- 是应用层协议：在端系统之间进行通信；通过运输层协议传输DNS报文
- 使主机能够查询分布式数据库
- DNS协议运行在UDP之上，使用53号端口

### 提供服务
- 为其他应用程序提供一个核心功能
- 提供：[[#主机名到IP地址的目录服务]]、[[#主机别名]]、[[#邮件服务器别名]]、[[#负载分配]]、等服务
##### 主机名到IP地址的目录服务
- 通常是为其他协议使用，包括[[HTTP]]、[[SMTP]]、[[FTP]]
##### HTTP场景
- 运行在用户主机的浏览器，请求URL `www.someschool.edu/index.html`
- 用户主机为了将HTTP报文发到`www.someschool.edu`的主机上，必须知道其IP地址
- 过程：
	1. 该用户主机上运行着[[DNS客户端]]
	2. 浏览器从URL中抽取[[#主机名]]，将其传给[[DNS客户端]]。（见[[现代浏览器内部揭秘#一次简单的导航过程]]）
	3. [[#DNS客户端]]向[[#DNS服务器]]发送一个包含主机名的请求
	4. DNS客户端最终受到一份回答报文，其中包含主机名**对应的**[[#IP地址]]
	5. 浏览器接收到来自[[DNS客户端]]的IP地址，然后向位于该IP地址80端口的HTTP服务器进程发起一个TCP连接
##### 时延
- 上述过程明显会有时延
- 通常[[#IP地址]]缓存在**附近的**DNS服务器中，助于减少时延、流量

##### 主机别名
- host aliasing
- 主机别名比规范主机名更容易记忆
- 应用程序可以调用DNS获得主机别名对应的[[#规范主机名]]和IP地址
- 一个主机可以有多个别名
###### 规范主机名
- canonical hostname
- 有复杂主机名的主机能拥有多个别名。其中原始的主要的主机名称为**规范主机名**
##### 邮件服务器别名
- mail server aliasing
- 电子邮件应用程序可以调用DNS获得主机别名对应的规范主机名和IP地址
- 通过**MX记录**允许邮件服务器和Web服务器使用**相同的别名化的**主机名

##### 负载分配
- load distribution
- 用于在冗余的服务器之间进行负载分配
- 一个Web站点被分布在多台服务器上，每个都有不同的IP地址。其地址**集合**与同一个[[#规范主机名]]联系
- DNS数据库中存储主机名与IP地址集合的映射
- 当客户对该主机名发出一个DNS请求时，DNS服务器会用整个IP地址进行响应，但每个回答中**循环**这些地址**次序**
- 即循环分配了负载
- 同样可以用于邮件服务器。多个邮件服务器可以具有相同的别名