1. 查看DNS缓存
```
dscacheutil -statistics
```

2. 清除
```
dscacheutil -flushcache
```

3. 查看DNS
```
cat /etc/resolv.conf
```