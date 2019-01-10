## 安装部署问题

**Q：使用MySQL数据库，有时重启teleport服务后工作不正常。** _（注：v3版本已经修正了此问题）_

A：Teleport内建支持SQLite，因此 `/etc/init.d/teleport` 启动脚本没有加入对mysqld服务的依赖，导致有时teleport服务先于mysqld服务启动，此时teleport服务会因为无法连接到数据库而工作不正常。解决办法是修改`/etc/init.d/teleport`脚本，将mysqld加入到依赖的服务列表中：在脚本开始处，找到 `# Required-Start:` 一行，在这一行末尾，追加 ` mysqld`，然后重启teleport服务即可。



## 远程连接问题

**Q：使用RDP进行远程连接时，有时连接界面一闪而过，在日志页面显示错误“协议不支持”。**

A：Teleport目前支持两类RDP协议，一是最原生的RDP协议，二是基于SSL链路层的RDP协议。高版本Windows系统的RDP协议在身份认证方面引入了NLA（网络级身份验证），此方式目前teleport尚未支持。要想使用RDP远程连接这些Windows主机，需要在远程主机上关闭NLA。

在远程主机上打开“**计算机-属性-远程设置**”，将默认的“仅允许运行使用网络级别身份验证的远程桌面的计算机连接”勾选项**去掉**。如果有选择协议的选项，请选择“允许运行任意版本远程桌面的计算机连接” 。

**Q：RDP不支持复制粘贴？**

A：目前RDP客户端选用的是FreeRDP，因为FreeRDP本身的问题，无法支持复制粘贴，有时候看上去复制粘贴了，但是复制的文件内容是空的。此问题暂时无解。

部分用户反馈，如果客户端运行平台是Windows10，那么可以从FreeRDP官网下载最新版本，替换掉teleport助手内置的tprdp-client.exe，可以解决一部分问题。


**Q：那么为什么不支持使用系统自带的原生RDP客户端 mstsc ？**

A：Teleport的RDP实现尚不完整，使用mstsc做RDP客户端可以进行远程连接，但记录的录像回放文件无法播放，因此目前暂时禁用了mstsc的支持。


**Q：有时候进行远程连接时，WEB页面提示“核心服务未启动”。** _（注：v3版本已经修正了此问题）_

A：确实是核心服务未启动，应该是核心服务崩溃了，常见于执行一些无法进行的RDP远程连接（例如远程主机地址无法连接之类的）之后，因为核心服务的bug导致核心服务崩溃。此问题正在想办法解决。目前只能重启核心服务，或者在teleport服务器上开启定时执行核心服务启动脚本（核心服务启动脚本会自己检查是否已经启动了，如果当前没有启动，则会启动，否则忽略）。目前teleport QQ群里有同学已经写了这样的脚本，临时解决一下问题。

**Q：V3版本，MacOS平台进行SSH远程连接时，SSH命令行返回错误“Invalid key length”。** _（注：v3.01.6版本已经修正了此问题）_

A：Teleport服务端的SSH转发服务内建的RSA私钥是1024位的，高版本的MacOS的SSH客户端要求2048位以上的RSA密钥对。临时解决办法是使用工具`ssh-keygen`生成2048位的密钥对，替换Teleport服务内建的，然后重启核心服务即可。

```shell
$> cd /usr/local/teleport/data/etc
$> ssh-keygen -f tp_ssh_server.key
```

注意，提示设置密码时，直接回车，不能为密钥设置密码，否则核心服务将无法加载此密钥文件。

**Q：使用ssh登录华为交换机，总是提示密码错误。**

A：华为交换机默认开启了keyboard-interactive模式，teleport以此认证模式进行认证时，会因为“模拟交互过程”不匹配而导致认证失败（而非密码错误），因此，可以关闭此认证模式。

正常ssh远程到华为交换机，执行以下命令关闭交互式认证模式：

```bash
undo ssh server authentication-type keyboard-interactive enable
```

关闭后即可通过teleport进行远程管理了。