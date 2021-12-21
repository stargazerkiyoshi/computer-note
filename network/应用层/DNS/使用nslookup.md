### 查询类型A记录
- `nslookup domain [dns-server]`
- dns-server 可选的，不输入用默认DNS服务器（本地DNS服务器？？？）
##### 例1
- 命令行输入`nslookup com`
	```
	➜  ~ nslookup com
	Server:		192.168.100.1
	Address:	192.168.100.1#53

	Non-authoritative answer:
	*** Can't find com: No answer
	```
- `Server:		192.168.100.1
	Address:	192.168.100.1#53`
	应该是[[DNS工作过程#本地DNS服务器|本地DNS服务器]]，通过53端口发送查询指令
- `Non-authoritative answer:`
	表示：非权威的回答
- 后面是自解释的

##### 例2
- 命令行输入`nslookup baidu.com`
	```
	➜  ~ nslookup baidu.com
	Server:		192.168.100.1
	Address:	192.168.100.1#53

	Non-authoritative answer:
	Name:	baidu.com
	Address: 220.181.38.251
	Name:	baidu.com
	Address: 220.181.38.148
	```
- `Name:	baidu.com
	Address: 220.181.38.251
	Name:	baidu.com
	Address: 220.181.38.148`
	返回两条类型A记录，非权威服务器，可能是某个TLD服务器
- 查询IP地址`220.181.38.251`、`220.181.38.148`归属地，都是在北京IDC机房
	![[baidu.com归属地查询.png]]

##### 例3
- 命令行输入`nslookup www.baidu.com`
	```
	➜  ~ nslookup www.baidu.com
	Server:		192.168.100.1
	Address:	192.168.100.1#53

	Non-authoritative answer:
	www.baidu.com	canonical name = www.a.shifen.com.
	Name:	www.a.shifen.com
	Address: 180.101.49.12
	Name:	www.a.shifen.com
	Address: 180.101.49.11
	```
- `www.baidu.com	canonical name = www.a.shifen.com.`
	`www.baidu.com`应该是个[[DNS#主机别名|主机别名]]，其[[DNS#规范主机名|规范主机名]]是`www.a.shifen.com`
- 后面是`www.a.shifen.com`的两条类型A记录
- 查询IP地址`180.101.49.12`、`180.101.49.11`归属地，都是在南京
![[www.a.shifen.com的归属地.png]]

### 查询其他类型记录
- `nslookup -q=type domain [dns-server]`
- `-q`可以替换为`-ty`
- type 可以替换为下列类型：
A 地址记录
AAAA 地址记录
AFSDB Andrew文件系统数据库服务器记录
ATMA ATM地址记录
CNAME 别名记录
HINFO 硬件配置记录，包括CPU、操作系统信息
ISDN 域名对应的ISDN号码
MB 存放指定邮箱的服务器
MG 邮件组记录
MINFO 邮件组和邮箱的信息记录
MR 改名的邮箱记录
MX 邮件服务器记录
NS 名字服务器记录
PTR 反向记录
RP 负责人记录
RT 路由穿透记录
SRV TCP服务器信息记录
TXT 域名对应的文本信息
X25 域名对应的X.25地址记录

##### 例1
- 命令行输入`nslookup -q=ns baidu.com`
	```
	➜  ~ nslookup -q=ns baidu.com
	Server:		192.168.100.1
	Address:	192.168.100.1#53

	Non-authoritative answer:
	baidu.com	nameserver = ns7.baidu.com.
	baidu.com	nameserver = ns3.baidu.com.
	baidu.com	nameserver = ns2.baidu.com.
	baidu.com	nameserver = ns4.baidu.com.
	baidu.com	nameserver = dns.baidu.com.

	Authoritative answers can be found from:
	```
- 返回4条`baidu.com`的类型NS记录，用于接下来的查找

### 对`www.baidu.com`的解析
- 见[[对baidu的解析]]