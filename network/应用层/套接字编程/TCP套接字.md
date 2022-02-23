- 面向连接的协议，所以客户和服务器之间发送数据前要先握手和创建TCP连接
- 客户端套接字（IP地址和端口号）和服务器套接字（IP地址和端口号）相连接
- 发送数据时，只需将分组传递给TCP连接。（与[[UDP套接字]]）不同
- 客户和服务器通过一根**管道**连接在一起，都可以发送接收数据
### 过程
- 因为客户要发起与服务器的握手动作，服务器要迎接初始接触，所以服务器要事先准备好
- 准备好包括：
	1. 服务器程序作为进程已经运行
	2. 该进程中的一个特殊套接字时刻等着接受初始接触（**欢迎套接字**）
- 客户程序通过创建一个TCP套接字，与服务器发起TCP连接。创建时，要指定服务器欢迎套接字的地址（IP地址和端口号）
- 生成TCP套接字后，客户发起三次握手（客户和服务器都知道），并创建一个TCP连接
- 三次握手时，服务器将生成一个新的套接字，称为**连接套接字（connection Socket）**，用于对专门的客户进行连接
![[TCP套接字过程.png]]
##### 欢迎套接字
- 对该服务器进程的所有客户进行监听，用来进行三次握手的过程
##### 连接套接字
- 三次握手后，服务器进程创建的对专门的客户的套接字

### 程序流程
![[tcp程序流程.png]]

### 代码
```python
# tcp-client.py
from socket import *

# 服务器的地址（IP地址或主机名），如果是主机名则再通过DNS获得IP地址
serverName = 'localhost'
serverPort = 12000

# 参数1：地址族，AF_INET指示底层网络使用IPv4
# 参数2：套接字类型，SOCK_STREAM指示TCP套接字
# 创建的时候不需要指定客户套接字的端口号，由操作系统指示，创建客户进程
clientSocket = socket(AF_INET, SOCK_STREAM)
# 与目的服务器连接：执行三次握手，并创建一条TCP连接
clientSocket.connect((serverName, serverPort))

res = ''
while res != 'EXIT':
  sentence = input('Input lowercase sentence:')

  # 不需要显式创建一个分组并附上目的地址，只需将字符串放在TCP连接中，等待接收服务器的返回
  clientSocket.send(sentence.encode('utf-8'))
  # 服务器返回的字节累积在modifiedMessage，直到收到回车符，结束该行
  # 缓存长度2048
  modifiedMessage = clientSocket.recv(2048)
  res = modifiedMessage.decode('utf-8')
  print(res)
# 关闭套接字，关闭TCP连接，将会向服务器发送一条TCP报文
clientSocket.close()
```

```python
# tcp-server.py
from socket import *

serverPort = 12000
serverSocket = socket(AF_INET, SOCK_STREAM)
# 欢迎套接字绑定一个端口端口号,
serverSocket.bind(('', serverPort))
# 欢迎套接字等待敲门握手
# 参数为最大连接数, >= 1
serverSocket.listen(1)
print('服务器准备接收')
while True:
  # 敲门握手时，创建了一个新的socket:connectionSocket，给该客户使用
  # 完成握手，创建了一TCP连接
  connectionSocket, addr = serverSocket.accept()
  # 在该连接上发送数据
  sentence = connectionSocket.recv(2048)
  print(f"resevie sentence '{ sentence }' from { addr }")
  modifiedMessage = sentence.upper()
  connectionSocket.send(modifiedMessage)
  # 关闭TCP连接，但不会关闭欢迎套接字
  connectionSocket.close()
```