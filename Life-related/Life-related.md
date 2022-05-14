# Life-related

### windows下获取WiFi密码

### 开代理访问外网加载ssl证书不对

```
一、hosts 是否有问题？
二、域名被运营商劫持？
ping 一下 www.google.com ，看返回的结果呗，得到的 ip 再查 whois，看是不是 google 的，如果不是，那应该是被运营商或墙污染了。

试试 dnscrypt
三、域名被流氓软件劫持？
```

### 如何查看域名是哪个DNS解析的

```
WINDOWS：使用nslookup命令，然后系统就会显示：默认服务器：x.x.x.x，即该 IP 地址就是默认的 DNS 服务器；
UNIX/Linux ：使用 more 命令查看一下 /etc/resolv.conf 文本文件
```

### wsl



