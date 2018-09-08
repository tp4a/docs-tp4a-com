Teleport服务端默认WEB服务端口为7190，可以在TP服务器前面架设一个Nginx：

 - 可以将端口映射为默认端口，更好记；
 - 可以配置为使用域名方式访问你的TP服务；
 - 可以配置成HTTPS方式访问，更安全。

## 普通配置

从TP v3.x开始，部分页面使用了websocket 进行实时通讯，因此，Nginx的配置文件需要做一些额外调整，下面是一个示例（以Nginx服务与TP服务在同一台主机上为例）：

```nginx
# ...其他内容...

  server {
    listen 80;
    server_name www.your-teleport-domain.com;
    location / {
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_pass       http://127.0.0.1:7190;

      # 以下三行是websocket需要的
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
    }
  }

# ...其他内容...
```

如果Nginx与TP服务不在同一台主机上，那么还要记得在Nginx服务所在主机上为TP服务器开启相应的端口转发，默认的需要52089/52189/52389三个端口，具体的端口值，请检查你的TP服务核心配置文件 [core.ini](config.md#core-ini) 。

## 配置为HTTPS

为提升完全性，可以将 nginx 配置为 https 方式访问。要这样做，需要将 nginx  的配置文件做如下修改：

```nginx
# ...其他内容...

  server {
    listen 443;
    server_name www.your-teleport-domain.com;

    ssl on;
    ssl_certificate_key path/to/your/server-private-key.pem
    ssl_certificate path/to/your/server-cert.pem
    
    location / {
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_pass       http://127.0.0.1:7190;

      # 以下三行是websocket需要的
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
    }
  }

# ...其他内容...
```

其中，`ssl_certificate_key` 和 `ssl_certificate` 是你的服务端证书的私钥和服务端证书。如果你不知道如何获取一份服务端证书，那么去了解一下 [Let's Encrypt](https://letsencrypt.org/) 将会是一个不错的开端。



