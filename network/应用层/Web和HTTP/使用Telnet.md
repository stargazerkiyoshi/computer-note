- macos 安装 telnet ，先安装brew，再安装telnet
	```
	brew install telnet
	```
- 查看真实的HTTP响应报文
1. GET
	- 输入
	```
	telnet cis.poly.edu 80

	GET /-ross/ HTTP/1.1
	Host: cis.poly.edu
	```
	- 响应
		```
		HTTP/1.1 301 Moved Permanently
		Date: Wed, 15 Dec 2021 02:33:32 GMT
		Server: Apache/2.4.6
		Location: https://cse.engineering.nyu.edu/-ross/
		Content-Length: 246
		Content-Type: text/html; charset=iso-8859-1

		<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
		<html><head>
		<title>301 Moved Permanently</title>
		</head><body>
		<h1>Moved Permanently</h1>
		<p>The document has moved <a href="https://cse.engineering.nyu.edu/-ross/">here</a>.</p>
		</body></html>
		```
2. HEAD
    - 输入
	```
	telnet cis.poly.edu 80

	HEAD /-ross/ HTTP/1.1
	Host: cis.poly.edu
	```
    - 响应
		```
		HTTP/1.1 400 Bad Request
		Date: Wed, 15 Dec 2021 02:38:54 GMT
		Server: Apache/2.4.6
		Connection: close
		Content-Type: text/html; charset=iso-8859-1
		```
3. 更换路径
    - 输入
	```
	telnet cis.poly.edu 80

	GET /-banana/ HTTP/1.1
	Host: cis.poly.edu
	```
    - 响应
		```
		HTTP/1.1 400 Bad Request
		Date: Wed, 15 Dec 2021 02:40:54 GMT
		Server: Apache/2.4.6
		Content-Length: 226
		Connection: close
		Content-Type: text/html; charset=iso-8859-1

		<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
		<html><head>
		<title>400 Bad Request</title>
		</head><body>
		<h1>Bad Request</h1>
		<p>Your browser sent a request that this server could not understand.<br />
		</p>
		</body></html>
		```