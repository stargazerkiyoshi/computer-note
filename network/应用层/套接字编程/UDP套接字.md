- 发送进程使用UDP时，要**先将目的地址**附在分组之上
- 该分组通过发送进程套接字发送到internet上，internet根据该分组上的目的地址，发送到接收进程
- 接收方主机上可能运行多个网络应用，每个应用有一个或多个套接字，怎么知道到底是哪个套接字
- 所以要接收的进程需要创建一个套接字，并指定一个称为**端口号（port number）**的标识符
### 目的地址
- 包括：
	1. 接收方的IP地址
	2. 接收方套接字的端口号

### 程序流程
![[udp程序过程.png]]
### 代码
```python
# udp-client.py
from socket import *

# 服务器的地址（IP地址或主机名），如果是主机名则再通过DNS获得IP地址
serverName = 'localhost'
serverPort = 12001

# 参数1：地址族，AF_INET指示底层网络使用IPv4
# 参数2：套接字类型，SOCK_DGRAM指示UDP套接字
# 创建的时候不需要指定客户套接字的端口号，由操作系统指示，创建客户进程
clientSocket = socket(AF_INET, SOCK_DGRAM)
res = ''
while res != 'EXIT':
  message = input('Input lowercase sentences:')

  # 为报文附上目的地址（并自动附上源地址）向进程的套接字clientSocket发送结果分组
  clientSocket.sendto(message.encode('utf-8'), (serverName, serverPort))
  # 等待服务器返回分组，当返回的分组到达客户端时，分组数据放在modifiedMessage，服务器的地址信息放在serverAddress
  # 缓存长度2048
  modifiedMessage, serverAddress = clientSocket.recvfrom(2048)
  res = modifiedMessage.decode('utf-8')
  print(res)
clientSocket.close()
```

```python
# udp-server
from socket import *

serverPort = 12001
serverSocket = socket(AF_INET, SOCK_DGRAM)
# socket 绑定一个端口端口号
serverSocket.bind(('', serverPort))
print('服务器准备接收')
while True:
  message, clientAddress = serverSocket.recvfrom(2048)
  print(f"resevie message '{ message }' from { clientAddress }")
  modifiedMessage = message.upper()
  serverSocket.sendto(modifiedMessage, clientAddress)
```
