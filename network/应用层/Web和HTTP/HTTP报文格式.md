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
- 
### 响应报文