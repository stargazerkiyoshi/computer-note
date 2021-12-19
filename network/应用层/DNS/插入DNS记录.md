- [[DNS工作过程]]关注于如何取资源记录
- 如何向DNS服务器中的数据库插入[[DNS记录]]呢

### 场景
- **你**创立了一家公司Network Utopia（网络乌托邦）
- **你**在**[[#注册登记机构]]**注册域名 `networkutopia.com`
- **你**在注册时，需要提供基本和辅助[[DNS工作过程#权威DNS服务器|权威DNS服务器]]的名字和IP地址。（ `dns1.networkutopia.com` `212.212.212.1` 和 `dns2.networkutopia.com` `212.212.212.2`）
- 对每一个提供的权威DNS服务器，**注册登记机构**确保将有一个类型NS和类型A的记录输入TLD com服务器，特别是基本权威DNS服务器
	插入记录：
	- `(networkutopia.com, dns1.networkutopia.com, NS)`
	- `(dns1.networkutopia.com, 212.212.212.1, A)`
- **你**输入两条记录到刚才提供的权威DNS服务器中：
	- 用于Web服务器`www.networkutopia.com`的类型A资源记录
	- 用于邮件服务器`mail.networkutopia.com`的类型MX资源记录
- 此时，人们可以访问**你**的Web站点，发送电子邮件
##### 注册登记机构
- registrar
- 商业实体
- 验证该域名的唯一性，将域名输入DNS数据库
- 收取少量费用
- 由internet名字和地址分配机构（Internet Corporation for Assigned Names and Numbers，ICANN）向注册登记机构授权
- www.internic.net可以查询
