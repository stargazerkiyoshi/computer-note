make file 时报错找不到linux-header-5.14.0-kali4

1. 查看系统内核
```
uname -r
```
2. 找到可选择的image和headers
```sh
apt-cache search linux-image
```
```sh
apt-cache search linux-headers
```
3. 自动更新或手动更新内核
```
sudo apt update -y && apt upgrade -y && apt dist-upgrade
```
```
sudo apt-get install linux-image-5.15.0-kali2-amd64
```
4. 重启（一定要重启）
```
reboot
```
5. 再次查看系统内核：成功
```
uname -r
```
6. 再次编译要编译的文件
