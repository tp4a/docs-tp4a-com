## 服务端配置文件说明

**本文中 `%INSTDIR%` 指 teleport 服务端安装目录，默认位于 `/usr/local/teleport/`。**

teleport 配置文件位于 `%INSTDIR%/data/etc` 目录下。服务端包含两个配置文件： `core.ini` 和 `web.ini`，其中，core.ini用于核心服务，web.ini用于WEB服务。

**注意：修改完配置文件后，需要重启 teleport 服务方可生效，重启方式详见[这里](install.md#server-control)。**

### 核心服务配置文件 <a id="core-ini"></a>core.ini

```ini
; codec: utf-8

[common]
; 'log-file' 定义核心服务日志文件。如果没有设置，默认为 %INSTDIR%/data/log/tpcore.log
;log-file=/var/log/teleport/tpcore.log

; log-level 指定日志等级，可以为 0 到 4 之间的数值，如果没有设置，默认为 2.
; LOG_LEVEL_DEBUG     0   log every-thing.
; LOG_LEVEL_VERBOSE   1   log every-thing but without debug message.
; LOG_LEVEL_INFO      2   log infomation/warning/error message.
; LOG_LEVEL_WARN      3   log warning and error message.
; LOG_LEVEL_ERROR     4   log error message only.
;log-level=2

; 'debug-mode' 指定是否运行为调试模式，可以是 0 或 1，默认为0，表示不运行为调试模式。
; 调试模式下，log-level 强制为 0，且发生错误时会记录函数调用栈。
debug-mode=0

; 'replay-path' 定义录像文件的存放目录，如果没有设置，默认为 %INSTDIR%/data/replay
;replay-path=/var/lib/teleport/replay

; 'web-server-rpc' 定义WEB服务的rpc接口地址，如果没有设置，默认为 http://127.0.0.1:7190/rpc
; 注意：如果你修改了web.ini中的端口，必须同步修改本设置项。
web-server-rpc=http://127.0.0.1:7190/rpc

[rpc]
; 核心服务的rpc接口监听的IP地址和端口号。
; 注意：如果你修改了本设置，请同步修改web.ini中的 'core-server-rpc' 设置项。
bind-ip=127.0.0.1
bind-port=52080

[protocol-ssh]
enabled=true
lib=tpssh
bind-ip=0.0.0.0
bind-port=52189

[protocol-rdp]
enabled=true
lib=tprdp
bind-ip=0.0.0.0
bind-port=52089

[protocol-telnet]
enabled=true
lib=tptelnet
bind-ip=0.0.0.0
bind-port=52389
```

#### 录像文件存放路径 <a id="replay-path"></a>replay-path

因为在teleport服务工作过程中，占用磁盘空间较大的是录像回放文件，容易导致 /usr 空间不足，因此可以根据需要将录像文件存放到别的挂载的磁盘上。可以通过修改 `replay-path` 来进行设定。

#### 禁用某个协议

如果您并不打算远程连接teleport支持的所有协议，可以将其禁用。例如，在您的工作环境中不需要使用Windows远程桌面（即，RDP协议），那么可以将 `[protocol-rdp]` 小节中的 `eanbled` 设为 `false` 。


### WEB服务配置文件 <a id="web-ini"></a>web.ini

```ini
; codec: utf-8

[common]
; ip=0.0.0.0

; WEB服务监听端口，默认为 7190.
; 注意：如果你修改了本设置项，请同步修改core.ini中的 web-server-rpc 配置项。
; port=7190
port=7190

; 'log-file' 定义核心服务日志文件。如果没有设置，默认为 %INSTDIR%/data/log/tpweb.log
;log-file=/var/log/teleport/tpweb.log

; log-level 指定日志等级，可以为 0 到 4 之间的数值，如果没有设置，默认为 2.
; LOG_LEVEL_DEBUG     0   log every-thing.
; LOG_LEVEL_VERBOSE   1   log every-thing but without debug message.
; LOG_LEVEL_INFO      2   log infomation/warning/error message.
; LOG_LEVEL_WARN      3   log warning and error message.
; LOG_LEVEL_ERROR     4   log error message only.
;log-level=2

; 'debug-mode' 指定是否运行为调试模式，可以是 0 或 1，默认为0，表示不运行为调试模式。
; 调试模式下，log-level 强制为 0，且发生错误时会记录函数调用栈。
debug-mode=0

; 'core-server-rpc' 定义核心服务的rpc接口地址，如果没有设置，默认为 http://127.0.0.1:52080/rpc
; 注意：如果你修改了core.ini中的端口，必须同步修改本设置项。
core-server-rpc=http://127.0.0.1:52080/rpc

[database]
; 数据库类型，可以是 sqlite 或 mysql, 默认为 sqlite.
; type=sqlite

; 当数据库设置为使用 sqlite 时，指定sqlite数据库文件的路径，如果未指定，默认为 %INSTDIR%/data/db/ts_db.db
; sqlite-file=/var/lib/teleport/data/ts_db.db

; 当数据库设置为使用 mysql 时，指定mysql数据库的访问地址和用户账号信息。
; 注意：如需使用mysql数据库，需要先创建对应的数据库和用户，并为用户授权访问指定的数据库。
; mysql-host=127.0.0.1
; mysql-port=3306
; mysql-db=teleport
; mysql-prefix=tp_
; mysql-user=teleport
; mysql-password=password
```

#### 使用 <a id="use-mysql"></a>MySql 或 MariaDB

Teleport支持MySQL数据库（当然也支持MariaDB）。这里以MySQL为例进行说明。要使用MySQL数据库，需要先为teleport创建库并授权。以MySQL数据库的管理员身份在命令行工具中执行：

**注意：数据库的字符集必须是 utf-8 ，否则会出现乱码。**

```sql
> create database teleport default character set utf8 collate utf8_general_ci;
> grant all privileges on teleport to 'teleport'@'127.0.0.1' identified by 'password';
> flush privileges;
```

请注意表名、用户名与密码等要与 web.ini 中的设置一致。修改 `web.ini`，去掉 `; type=sqlite` 前面的注释符号，并改为 `type=mysql`。