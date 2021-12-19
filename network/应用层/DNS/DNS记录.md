- 所有DNS服务器存储了[[#资源记录]]

### 资源记录
- Resource Record，RR
- 提供了主机名到IP地址的映射
- 每个DNS回答报文包含一条或多条资源记录

##### 包含字段
- 包含下列字段的4元组：`(Name, Value, Type, TTL)`
##### 记录的类型
- TTL该记录的生存时间，决定了该条记录应当从缓存删除的时间
1. Type = A。记录了标准的主机名到IP地址的映射

| Type | Name | Value |
| ------ | ------ |
| A | 主机名 | 该主机对应的IP地址 |
例如：`(relay1.bar.foo.com, 145.37.93.126, A)`
2. Type = NS。用于沿着查询链来路由DNS查询

| Type | Name | Value |
| ------ | ------ |
| NS | 一个域 | 权威DNS服务器主机名(知道如何获得该域中主机IP地址) |
例如：`(foo.com, dns.foo.com, NS)`
3. Type = CNAME。记录了一个[[DNS#主机别名]]对应的[[DNS#规范主机名]]

| Type | Name | Value |
| ------ | ------ |
| CNAME | 主机别名 | 该主机别名的规范主机名 |
例如：`(foo.com, relay1.bar.foo.com, CNAME)`
3. Type = MX。记录了一个[[DNS#邮件服务器别名]]对应的[[DNS#规范主机名]]

| Type | Name | Value |
| ------ | ------ |
| MX | 邮件服务器别名 | 该邮件服务器别名的规范主机名 |
例如：`(foo.com, mail.bar.foo.com, MX)`
- 可以与其他服务器使用相同的别名
- 当获取邮件服务器的规范主机名时，DNS客户应当请求MX记录；其他服务器的规范主机名，则请求CNAME记录

##### [[DNS工作过程#DNS服务器层次结构]]中的记录类型
1. 权威DNS服务器（用于某特定主机名）
	- 有一条**类型A记录**，包含该主机名
2. 其他层次的服务器
	- 有一条**类型NS记录**，对应于包含主机名的域
	- 有一条**类型A记录**，提供NS记录中Value字段中DNS服务器的IP地址
	- 可能有其他缓存的**类型A记录**
	- 例子：假设edu TLD服务器不是权威DNS服务器，则：
		- 包含一条包括主机`cs.umass.edu`的域记录，如：`(umass.edu, dns.umass.edu, NS)`
		- 包括一条将`dns.umass.edu`映射为IP地址的A记录，如`(dns.umass.edu, 128.119.40.111, A)`


