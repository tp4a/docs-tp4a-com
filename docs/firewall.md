默认安装的情况下，Teleport服务端在四个端口上监听外部接入：

- `7190` Teleport的WEB后台
- `52089` RDP远程桌面协议端口
- `52189` SSH协议端口
- `52389` Telnet协议端口

> 注：这些端口可以在核心配置文件core.ini中更改设置，也可以根据需要禁用无需支持的协议端口。

为保证客户端能够接入，需要在teleport服务器主机防火墙放开以上端口。

以CentOS 7为例，需要以管理员身份执行：

```bash
firewall-cmd --zone=public --add-port=7190/tcp --permanent
firewall-cmd --zone=public --add-port=52089/tcp --permanent
firewall-cmd --zone=public --add-port=52189/tcp --permanent
firewall-cmd --zone=public --add-port=52389/tcp --permanent
firewall-cmd --reload
```




