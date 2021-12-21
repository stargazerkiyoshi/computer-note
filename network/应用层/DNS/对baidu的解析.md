原文：https://www.cnblogs.com/zw1sh/p/10181450.html

# ping百度域名时的收获

# ping百度

你会发现`ping www.baidu.com`的时候，会转为`ping www.a.shifen.com`。但是`ping baidu.com`的时候却是普通的ip地址，而且ip地址还会变化。那么出现`www.a.shifen.com`的原因是什么呢？为什么`baidu.com`和`www.baidu.com`的解析地址不同呢？

这里用一句话说就是[CNAME](https://baike.baidu.com/item/CNAME/9845877?fr=aladdin)的原因（cname：别名），但能由此扩展到以下几个方面

### 从DNS查找原因

首先我们需要明白`www.baidu.com`和`baidu.com`是两个完全独立没有任何关系的url地址。所以解析出来的ip地址当然也可以不同。   
我们可以先查看一下`www.baidu.com`解析的ip地址

```
nslookup www.baidu.com
nslookup baidu.com
```

-   1
-   2

结果为

![[nslookupbaidu.com.png]]

可以看到`www.baidu.com`有一个别名，就是`www.a.shifen.com`，也就是说在百度的`baidu.com`域服务器上，有一条c记录。但是`www.a.shifen.com`是不能被直接访问的，浏览器访问或者`curl -v`都会被拒绝。

而`baidu.com`这个域名是可以直接访问的，这个域名有3个ip地址，这3个ip是可以直接浏览器访问的，而访问之后就会被直接转为`www.baidu.com`的域名上。为什么呢？我们后面再说。

### DNS访问过程

当我们查询`www.baidu.com`的地址时，会依次去本地host，本地dns，根域DNS服务器，.com服务器，baidu.com服务器查询。而在baidu.com域服务器里查找到www对应一条c记录，将`www.baidu.com`映射到`www.a.shifen.com`。所以会再次去.com域进行对shifen.com进行查询，以同样的方式，最终查到了`119.75.216.20`这个地址。

> 疑问：因为`shifen.com`和`baidu.com`两台域服务器其实是同一台服务器，而`baidu.com`对`www.baidu.com`进行别名转换的时候，其实是会直接返回`shifen.com`域服务器的ip的，有人说会直接访问`shifen.com`的域服务器，而不需要重新走`.com`顶级域服务器。我比较支持这观点，但是没试验过，实验的话也会比较麻烦，需要清理掉本地dns的影响。但这个并不重要。

当我们查询`baidu.com`的时候，也是同样的过程，只不过到了`baidu.com`域服务器的时候，没有别名转换了，直接返回对应的ip地址。

### HTTP/HTTPS访问

1.  我们会发现浏览器无法直接访问`www.a.shifen.com`，但是我们却可以访问`www.baidu.com`，可他们俩解析出来的地址应该是一样的啊。这是因为http请求的时候，会把请求的url写入请求头，服务器会拒绝带`www.a.shifen.com`域名的请求。
    
2.  我们直接访问`119.75.216.20`，发现是可以获得页面的，这说明百度的服务器就在`119.75.216.20`这个服务器上，也说明服务器并不拒绝ip直接访问。但是这样访问的话，只能是http的访问，不能使用登录相关的功能了。你使用`https://119.75.216.20`也没用，因为证书不认识，证书的域名是baidu.com
    
3.  但是我们平时访问的时候，都习惯输入`baidu.com`，而前面我们看到`baidu.com`的解析ip结果可和`www`的不一样，那为什么浏览器里访问的域名带不带`www`的都一样呢。通过
    
    ```
    curl -v baidu.com
    ```
    
    -   1
    
    我们可以发现结果如下图，返回的是一个很小的页面，他会使浏览器刷新转到`www.baidu.com`，也就是上面说的`119.75.216.20`这个服务器。动作跟上面也就一样了。所以其实你访问`baidu.com`解析出来的ip也是可以的，也会直接跳到`https://www.baidu.com`   
    ![[curlbaidu.png]]
    
4.  那为什么访问`http://www.baidu.com`的时候会变成`https://www.baidu.com`呢。这种应该是apache服务器将域名rewrite，转换成https链接进行访问，但是你发现使用ip进行访问是不行的，因为rewriterule是对请求的url进行正则匹配的，所以ip地址是无法进行rewrite的。
    

### 猜测思考

为什么为`baidu.com`分配3个ip地址呢？应该是DNS负载均衡。可以产生`ping baidu.com`解析的地址都是变化的。因为这个web地址只是返回单个index跳转页面，所以使用DNS负载均衡是满足要求，没什么问题的。而压力最大的`www.baidu.com`这个地址只有一个ip地址，想必是内网做的负载均衡。

但是百度服务器肯定是遍布全国啊，所以不同地区解析`www.baidu.com`的地址其实是不同的。如下图所示

![[baiduDNS负载均衡.png]]

左边的是北京地区，而右边的是大连地区的返回结果。也可能是网络服务商的不同返回结果不同。总之这样就会做到负载均衡。而两地对`baidu.com`域名的解析的结果是相同的，都是那3个ip地址。可能全国都一样吧。   
至于`a.shifen.com`，这个别名对用户来说没有意义，至于为什么保留这个别名，也不清楚为什么。