- 只有两种类型：查询和回答
- 两种类型格式相同
![[network/img/dns报文格式.png]]
### 报文说明
##### 首部区域
- 前12个字节
1. 标识符（16bit）
	- 标识该查询，复制到回答报文，用于匹配请求和回答
2. 标志字段
	- 含有一些标志位
		1. "查询/回答"标志位（1bit）：表示是查询（0）还是回答报文（1）
		2. "权威的"标志位（1bit）：在回答报文中，表示该服务器是所请求名字的权威服务器
		3. "希望递归"标志位（1bit）:在查询报文中，客户或DNS服务器在被查询的DNS服务器中没有记录时，希望执行递归查询
		4. "递归可用"标志位（1bit）：在回答报文中，DNS服务器支持递归查询
3. 后面四个数量字段表示首部后的4类数据区域出现的数量，一一对应

##### 问题区域
- 包含着查询信息，包括：
	1. 名字字段：正在被查询的主机名字
	2. 类型字段：该名字正被问询的问题类型。主机名和IP地址关联（类型A）还是某个邮件服务器（类型MX）

##### 回答区域
- 包含对最初请求的名字的[[DNS记录#资源记录|资源记录]]
- 可以包含多条
- 一个主机名能够由多个IP地址（冗余web服务器）

##### 权威区域
- 包含其他权威服务器的记录

##### 附加区域
- 包含其他有帮助的记录
- 如：MX请求的回答报文，该区域包含一条记录邮件服务器的规范主机名和该规范主机名的IP地址（类型A）

### nslookup程序
- nslookup program
- 可以直接向某些DNS服务器发送DNS查询报文
- 见[[使用nslookup]]