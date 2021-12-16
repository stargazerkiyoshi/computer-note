- conditional GET
- 当[[Web缓存器]]中缓存的对象副本后，初始服务器该对象又被修改了，此时Web缓存器的对象是旧的，客户请求的一直是缓存器中的旧对象，即无法更新新对象
- 条件Get方法解决该问题
- [[HTTP]]协议的一种机制

### 组成
1. 请求报文中使用GET方法
2. 请求报文中包含首部行：If-Modified-Since

### 过程
1. 一个[[Web缓存器|代理缓存器]]代表浏览器向一个Web服务器发送请求
	```
		GET /fruit/kiwi.gif HTTP/1.1
		Host: www.exotiquecuisine.com
	```
2. Web服务器返回一个包含该对象的HTTP响应
	```
		HTTP/1.1 200 OK
		Date: Sat, 8 Oct 2011 15:39:29
		Server: Apache/1.3.0 (Unix)
		Last-Modified: Wed, 7 Sep 2011 09:23:24
		Content-Type: image/gif
		
		(data data data data data ...)
	```
3. 缓存器