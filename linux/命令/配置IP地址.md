# centos7 配置主机名和Ip地址

2020-05-18阅读 1650

链接:

-   [centos修改主机名的正确方法](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnblogs.com%2Fzhaojiedi1992%2Fp%2Fzhaojiedi_linux_043_hostname.html)
-   [centos7配置IP地址](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnblogs.com%2Fyhongji%2Fp%2F9336247.html)

## 配置主机名

\[root@centos7 ~\]$ hostnamectl set\-hostname centos77.magedu.com             # 使用这个命令会立即生效且重启也生效
\[root@centos7 ~\]$ hostname                                                 # 查看下
centos77.magedu.com
\[root@centos7 ~\]$ vim /etc/hosts                                           # 编辑下hosts文件， 给127.0.0.1添加hostname
\[root@centos7 ~\]$ cat /etc/hosts                                           # 检查
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 centos77.magedu.com
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

## 配置Ip地址

有关于centos7获取IP地址的方法主要有两种，1：动态获取ip；2：设置静态IP地址

在配置网络之前我们先要知道centos的网卡名称是什么，centos7不再使用ifconfig命令，可通过命令 IP addr查看，如图，网卡名为ens32，是没有IP地址的

![[img1.png]]

**1、动态获取ip**（前提是你的路由器已经开启了DHCP）

修改网卡配置文件 vi /etc/sysconfig/network-scripts/ifcfg-ens32 (最后一个为网卡名称)

动态获取IP地址需要修改两处地方即可

（1）bootproto=dhcp

（2）onboot=yes

![](https://ask.qcloudimg.com/http-save/yehe-3635889/uvxe10inwm.png?imageView2/2/w/1620)

image

修改后重启一下网络服务即可 systemctl restart network

\[root@mini ~\]\# systemctl restart network
\[root@mini ~\]\# 

这样动态配置IP地址就设置好了，这个时候再查看一下ip addr 就可以看到已经获取了IP地址，且可以上网（ping 百度）

![](https://ask.qcloudimg.com/http-save/yehe-3635889/yfgqpwuaud.png?imageView2/2/w/1620)

image

**2、配置静态IP地址**

设置静态IP地址与动态iIP差不多，也是要修改网卡配置文件 vi /etc/sysconfig/network-scripts/ifcfg-ens32 (最后一个为网卡名称)

（1）bootproto=static

（2）onboot=yes

（3）在最后加上几行，IP地址、子网掩码、网关、dns服务器

IPADDR\=192.168.1.160
NETMASK\=255.255.255.0
GATEWAY\=192.168.1.1
DNS1\=119.29.29.29
DNS2\=8.8.8.8

（4）重启网络服务

\[root@mini ~\]\# systemctl restart network
\[root@mini ~\]\# 

DNS服务器可以只配一个，我用的是两个免费的dns服务器，查看IP地址，测试联网

![](https://ask.qcloudimg.com/http-save/yehe-3635889/wqtmj0a6m.png?imageView2/2/w/1620)

image