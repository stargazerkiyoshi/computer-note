### 截获请求和重定向
- 用户主机用浏览器请求一个特定的视频（URL表示）
- CDN截获该请求，进行：
	1. 确定合适的CDN服务器集群
	2. 将请求重定向到该集群的某个服务器中
- 利用[[DNS]]截获和重定向
##### 场景过程
- 内容提供商：NetCinema
- 第三方CDN：KingCDN
- NetCinema使用KingCDN向其客户分发视频
- NetCinema有一个Web页面，其中为每个视频指派了一个URL，如：`http://video.netcinema.com/6Y7B23V`
- 用户访问该Web页面
- 用户点击链接`http://video.netcinema.com/6Y7B23V`
- 该用户主机发送了一个对`video.netcinema.com`的DNS请求
- 用户[[DNS工作过程#本地DNS服务器|本地DNS服务器]]（LDNS）将服务器请求中继到用于NetCinema的[[DNS工作过程#权威DNS服务器|权威DNS服务器]]
- 该权威服务器发现主机名中的`video`，不返回IP地址，而返回KingCDN域的主机名。如：`a1105.kingcdn.com`。该步骤是为了将DNS请求移交给KingCDN
- DNS请求进入KingCDN专用DNS
- 用户的LDNS对`a1105.kingcdn.com`再次发送DNS请求
- KingCDN专用DNS通过[[#集群选择策略]]，最终向用户LDNS返回KingCDN内容服务器的IP地址，即指定了CDN服务器
- LDNS向用户主机转发KingCDN内容服务器CDN节点的IP地址
- 客户接收到该IP地址后，创建一条TCP连接，发出对该视频的HTTP GET请求
- 如果使用[[internet视频#DASH|DASH]]，服务器首先向客户发送具有URL列表的告示文件
- 客户动态选择来自不同版本的块

### 集群选择策略
- cluster selection strategy
- 即：动态将客户定向到CDN中某个服务器集群或数据中心的机制
- 是CDN的核心
- 客户经DNS查找到CDN域主机名，CDN也知道了该客户的LDNS服务器的IP地址，并基于该地址选择一个适当的集群
- 有多种策略

##### 地理上最为接近
- geographically closet
- 一种简单的策略
- 离客户地理上最近
- 使用地理数据库，为每个LDNS IP地址映射到一个地理位置
- 通过该地理位置选择最近的CDN集群
- 缺点：
	1. 忽略了时延和可用带宽随internet路径时间变化，总是提供相同的集群
	2. 少数用户最近的CDN集群可能在网络上不是最近的（网络路径长度和跳数
	3. 某些用户使用位于远处的LDNS
##### 实时测量
- real-time measurement
- 基于当前流量为客户决定最好的集群
- 对集群和客户之间的时延和丢包性能进行周期性的实时测量（通过集群周期性的向所有LDNS发送探测分组）
- 缺点：许多LDNS被配置为不会响应探测

