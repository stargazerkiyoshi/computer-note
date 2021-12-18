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
3. 缓存器向浏览器转发该对象，同时在本地创建一个该对象的副本。存储了**最后修改日期**。
4. 一个星期后，另一个用户通过该缓存器请求同一个对象，当缓存器查到该对象在本地的一个副本，但有可能该对象已经在初始服务器被修改了一些内容，所以**缓存器**会发送一个条件Get请求，执行检查。
	报文如下：
```
GET /fruit/kiwi.gif HTTP/1.1
Host: www.exotiquecuisine.com
If-modified-since: Wed, 9 Sep 2015 09:23:24
```
If-Modified-Since等于Last-Modify，首部行的值，即告诉服务器，仅当该日期后对对象的修改，才发送该对象
5. 假设没有修改，则Web服务器返回响应报文
```
HTTP/1.1 304 Not Modified
Date: Sat, 10 Oct 2015 15:39:29
Server: Apache/1.3.0 (Unix)

(empty entity body)
```
响应返回的是304 Not Modified，并且不需要返回所包含的对象，即告诉缓存器可以向浏览器返回本地缓存的对象的副本

