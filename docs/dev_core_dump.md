服务需要长时间稳定运行，但有时会发生核心服务崩溃的问题。为了能够快速定位崩溃的原因，我们需要生成core dump文件以供分析。

对于TELEPORT的核心服务来说，要在崩溃时生成Core Dump文件，请SSH远程登录到teleport服务器上，然后执行：

```shell
$> su
$> /etc/init.d/teleport stop core
$> ulimit -c unlimited
$> /usr/local/teleport/bin/tp_core -d start
```

**注意，请勿关闭此远程连接！**

命令解释：

 - `su` 以管理员身份登录，因为运行服务需要root权限，此时需要输入root密码。；
 - `/etc/init.d/teleport stop core` 停止teleport核心服务；
 - `ulimit -c unlimited` 临时打开系统的core dump功能，使得程序崩溃时记录核心转储数据；
 - `/usr/local/teleport/bin/tp_core -d start` 运行core服务程序，但不是以守护进程的方式运行，因此程序不会在后台运行，可以在控制台上看到大量的日志输出，**需要一直保留这个远程登录连接，不要退出，也不要关闭这个终端**。

然后像平常一样使用TELEPORT的各项功能，直到发生崩溃。此时应该能够看到控制台上最后输出的是`(core dumped)`，然后在
/usr/local/teleport/bin 目录下应该能够看到 `core.xxxx` 文件（xxxx是一个数字），这个文件就是用来进行崩溃分析的core dump文件。

请将这个文件压缩后发送给我们进行分析。
