**Github报错OpenSSL SSL_connect: Connection was reset in connection to github.com:443终极解决方案**

报错信息
``` shell
fatal: unable to access 'https://github.com/wxler/test.git/': OpenSSL SSL_connect: Connection was reset in connection to github.com:443
```

### 方案一
``` shell
git config --global http.sslBackend "openssl" git config --global http.sslCAInfo "C:\Program Files\Git\mingw64\ssl\cert.pem"
```

### 方案二
如果是开了vpn，这时需要设置代理
一定要查看自己的VPN端口号，假如你的端口号是7890，在git bash命令行中输入以下命令即可：
``` shell
git config --global http.proxy 127.0.0.1:7890
git config --global https.proxy 127.0.0.1:7890
```

如果你之前git中已经设置过上述配置，则使用如下命令取消再进行配置即可：

``` shell
git config --global --unset http.proxy 
git config --global --unset https.proxy
```

下面是几个常用的git配置查看命令：
``` shell
git config --global http.proxy #查看git的http代理配置 
git config --global https.proxy #查看git的https代理配置 
git config --global -l #查看git的所有配置
```
