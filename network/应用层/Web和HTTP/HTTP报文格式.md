- 分为请求报文和响应报文

### 请求报文
- 报文例子
```
	GET /somedir/page.html HTTP/1.1
	Host: www.someschool.edu
	Connection: close
	User-agent: Mozilla/5.0
	Accept-language : fr
```
- ASCII文本书写
- 每一行由一个回车和换行符结束，包括最后一行
- 可能有更多行
- 第一行：**请求行（request line）**，之后的行叫：**首部行（header line）**
##### 请求行
- 请求行有三个字段：方法字段、URL字段、HTTP版本字段
- 方法字段有：GET、POST、HEAD、PUT、DELET
- 本例中：浏览器请求一个对象，使用GET方法，请求的对象通过URL标识（/somedir/page.html），使用HTTP/1.1版本

##### 首部行
- Host：`Host: www.someschool.edu`指明对象所在主机。可能认为是不必要的，因为TCP已经连接了，但这是Web代理高速缓存所要求的。
- Connection：`Connection: close`，不使用持续连接，要求服务器发送完请求对象后就断开
- User-agent：用户代理，即向服务器发送请求的浏览器类型。这里是FireFox浏览器。服务器会根据该字段返回相同对象的不同版本（还是同一URL地址）
- Accept-language：希望获得对应语言的对象。如果服务器没有该版本，则返回默认的版本。内容协商部首之一。

##### 实体体
- entity body
- 上述例子中并没有实体
- 一般GET请求中没有实体体。POST请求中一般会有实体体
##### 通用格式
![[http请求报文格式.png]]
##### 各个请求方法
- GET
	1. 大部分使用的请求方法
	2. 没有[[#实体体]]
	3. 表单参数可以通过"?"添加到URL后面，以请求返回特定内容
- POST
	1. 一般有实体体
	2. 通常请求表单时使用，实体体中通常带有表单的输入值
	3. 同时也会请求一个Web页面，页面内容依赖于表单输入值
- HEAD
	1. 类似于GET
	2. 服务器收到HEAD请求时会响应，但不会返回请求对象
	3. 应用于调试跟踪
- PUT
	1. 允许用户或应用程序上传对象到指定的Web服务器上指定的路径（目录）
	2. 常与Web发行工具联合使用
- DELETE
	1. 允许用户或应用程序删除Web服务器上的对象
### 响应报文
- 例子：
	```
	HTTP/1.1 200 OK
	Connection: close
	Date: Tue, 09 Aug 2011 15:44:04 GT
	Server: Apache/2.2.3 (Centos)
	Last-Modified: Tue,
	09 Aug 2011 15:11:03 GMT
	Content-Length:6821
	Content-Type: text/html
	
	
	(data data data data data . . .)
	```
- 分为三部分
	1. 初始**状态行（status line）**
	2. 首部行（header line）
	3. 实体行（entity body）
##### 状态行
- 3个字段：协议版本、状态码、相应状态信息
- 该例子显示：使用HTTP/1.1，一切正常
##### 首部行
- Connection：`Connection: close`，服务器告诉客户，发送完报文后TCP连接将会关闭
- Date：服务器产生并发送该响应报文的日期和时间，即服务器从文件系统中检索出该对象并将其插入响应报文并发送响应报文的时间
- Server：指示是什么服务器产生的（Apache），类似于[[#请求报文]]中的User-agent
- Last-Modified：指示对象创建或最后修改日期和时间。该字段对对象缓存（在本地或网络缓存服务器上）非常重要
- Content-length：指示被发送对象的字节数
- Content-Type：指示实体体中，对象的类型，本例：HTML文本
##### 实体体
- 报文的主要部分
- 包含请求的对象本身
##### 通用格式
![[http响应报文.png]]
##### 常见状态码
- 200 OK：请求成功，信息返回在响应报文中
- 301 Moved Permanently：请求的对象已经被永久转移，新的URL在[[#响应报文]]的**Location**首部行中。客户浏览器将自动获取新的URL（见[[现代浏览器内部揭秘#3 读取响应]]）
- 400 Bad Request：通用差错代码，表示请求不能被服务器所理解
- 404 Not Found：被请求的文档不在服务器上
- 505 HTTP Version Not Supported：服务器不支持请求报文使用的HTTP协议版本

### 使用Telnet
- 查看真实的HTTP响应报文
具体例子待输入

### 更多首部行
- HTTP规范中定义了许多首部行
- 可以被浏览器、Web服务器、Web缓存服务器插入

### 影响首部行的因素
##### 浏览器产生的首部行
- 浏览器的类型、
- 协议版本（HTTP/1.0不会产生HTTP/1.1版本的首部行）
- 浏览器用户配置
- 浏览器当前是否有一个缓存对象，缓存对象版本是否超期
##### Web服务器产生的首部行
- 产品差异
- 版本差异
- 配置差异