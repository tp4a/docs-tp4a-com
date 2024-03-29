## 一、资产管理

点击页面左侧菜单，展开“资产”项目，然后点击“主机及账号”，会打开主机管理页面，远程主机和远程登录账号的管理主要在这个界面中进行。

![web-host-acc](img/web-host-acc.png)

### 1.1 	添加主机

点击页面右上方的“添加主机”按钮，然后在弹出的对话框中根据你要添加的远程主机的信息选择和填写相关内容：

 - **远程主机系统**： 可选择Windows或者Linux/Unix；
 - **远程主机地址**： 填写远程主机的IP地址，目前teleport尚未支持域名方式连接远程主机；
 - **连接模式**： 可选择直接连接或者端口映射模式；
 - 名称/资产编号/备注： （选填）为方便区分可填写相关信息。

![web-add-host-1](img/web-add-host-1.png)

其中“连接模式”一项，如果此远程主机可以由teleport服务器直接连接，则可以选择“直接连接”模式，例如远程主机与teleport服务器处于同一个局域网中，或者远程主机是开放了远程连接端口的云端服务器等。

如果远程主机与teleport服务器之间需要通过其他网络设备进行端口映射方可连接的，则需要选择“端口映射”模式。在这种模式下，teleport服务器实际上连接的是配置的 **路由主机** 的地址和映射端口，而非远程主机地址。

![web-add-host-2](img/web-add-host-2.png)

端口映射模式常用于以下场景：

 - teleport服务器处于公司内网，被维护的远程主机在云端且没有公网IP，需要通过一台有公网IP的服务器进行跳转。集群服务器常使用这种方式。
 - 使用teleport服务器管理运行docker的主机以及进入docker内部进行管理。

主机信息填写完毕后，点击“确定”按钮即可完成主机的添加。

### 1.2 添加账号

添加完主机后，还需要为此主机设置远程登录的账号，点击主机的账号数量，或者右侧操作菜单中的“管理远程账号”，会显示远程账号管理对话框。

![web-asset-manage-acc](img/web-asset-manage-acc.png)

然后点击“添加账号”按钮，弹出添加远程账号的对话框。需要注意的是，如果主机的连接模式使用端口映射模式，那么这里的“端口”项是无法更改的。

![web-asset-add-acc](img/web-asset-add-acc.png)

远程账号信息填写完毕后，可以使用对话框上的“测试连接”按钮来进行测试。

#### 1.2.1 RDP协议

添加账号时，如果远程账号是Windows域用户，那么账号名称需要使用 `user@domain` 这样的形式，而不能使用 `domain\user` 的形式。后者虽然可以正常远程登录使用，但会导致无法进行录像回放。因为teleport在回放RDP录像时，会下载录像文件到本地，文件名中会包含远程账号名称，而远程账号中的字符 `\` 是操作系统的路径风格符，从而导致无法创建本地文件使得下载失败。

!!! Note "提示"
    在 teleport 3.5 以上版本，已经没有上述限制了。

#### 1.2.2 SSH协议

如果是SSH协议，那么认证方式可以选择“SSH私钥认证”，此时密码输入框将被替换为SSH私钥输入框。目前teleport **仅支持RSA私钥，且私钥不能是密码加密后的**。私钥的格式如下所示：

```text
-----BEGIN RSA PRIVATE KEY-----
MIIEoAIBAAKCAQBki7kohX+oa0AELV8Oflrz545PQS47H5mh/sQ6KRHkt7bErVC3
watQdeXFHrnH0uKbmglFS6MVni86/tltqxxt+dcgTBsNyFiuUtSQBlIIwBiqtpFl
......
JQKBgBWsgyj+So3nB8nVsRFawarouM+xfwN+12CeLrlHVxvOdJ5LBF+uhPUO75qG
jU8XyGm54zSLsZfjyJ5IQu4WmiuEhzQagceML9x2INHHXE3w
-----END RSA PRIVATE KEY-----
```

如果你使用的私钥不是以上格式，请用相关工具进行转换方可在teleport中使用。

**注意，私钥对应的公钥应该预先部署在你的远程主机上了，具体操作可搜索“SSH免密码登录”。**

#### 1.2.3 TELNET协议

在添加telnet协议的远程账号时，如果使用用户名/密码认证方式，会要求填写“用户名预期提示”和“密码预期提示”两项内容。这是因为telnet本身是**弱协议**，也就是说它并没有明确定义用户如何登陆，何时输入用户名或者密码，不同的telnet服务有着各自不同的实现方式。比如说，有些telnet服务会显示 `Login:` 来提示用户输入用户名，而另一些则用 `username:` 进行提示，还有的用 `login:` 进行提示。而teleport在检测到对应的提示时，才能够正确填写相应的用户名和密码来完成自动登录。

所以，在添加telnet协议的远程账号之前，你需要手动连接一次你的telnet服务器，查看它的提示语，然后将其填写到预期提示的输入框中。

![web-asset-add-acc-telnet](img/web-asset-add-acc-telnet.png)

!!! Note "小技巧"
    默认的预期提示 `ogin:` 和 `assword:`，其实是 `login:` 和 `password:` 去掉了第一个字母，是因为常见的telnet服务多数使用这两个提示语，但是一些首字母是大写，一些首字母是小写。跳过第一个字母则可以增加适配性。

### 1.3 批量添加主机和账号

在“资产-主机及账号”页面的右上角，点击“导入主机和账号”按钮，会弹出导入资产对话框。

![](img/web-asset-import-1.png)

点击图标上传你的资产信息文件。如果还没有准备资产信息文件，可以从对话框中的下载链接中下载一份进行修改。

资产信息文件是一个CSV格式（逗号分隔）的文本文件，你可以用 Office 2016 及以上版本的 Excel 打开编辑，也可以用普通的文本编辑工具进行编辑，比如 EditPlus、UltraEdit，或者 NotePad++等。

!!! Note "注意"
    建议使用文本编辑工具进行编辑，某些版本的Excel会在保存时去掉部分逗号，导致最终文件格式不合法而无法导入。

#### 1.3.1 资产文件格式

下面对资产信息文件进行详细说明。首先来看一个示例：

用文本编辑工具打开的效果：

```
#IP,操作系统类型（linux/win）,名称,路由IP,路由端口,资产编号,账号,协议类型（ssh/rdp/telnet）,协议端口,认证类型（no/pw/key）,密码或私钥,账号提示（仅telnet）,密码提示（仅telnet）,分组,描述
1.2.3.4,linux,数据库,,,DB-0453,,,,,,,,数据库|阿里云-天津机房,
,,,,,,root,ssh,22,pw,abcd1234,,,管理员,
,,,,,,test,ssh,22,pw,abcd1234,,,测试|部署,
1.2.3.5,windows,,192.168.0.5,2054,,,,,,,,,,阿里云，天津机房\naS2n129m
,,,,,,administrator,rdp,,pw,abcd1234,,,管理员,
1.2.3.6,linux,,,,,,,,,,,,,
,,,,,,root,ssh,22,key,abcd1234,,,管理员,
```

用Excel打开的效果：

![](img/web-asset-import-2.png)

在资产信息文件中，`#`开头的行是注释，注释行和空行在导入时将被忽略。**Teleport要求资产信息文件中，主机与账号各自独立成行，不能写到同一行上。正确的做法是，一行主机信息，后面紧跟着一到多行的账号信息。**

下面详细解释各个字段的含义（除特别说明外，**加粗字段**是必填项）：

| 字段             | 主机 | 账号 | 说明                                                         |
| ---------------- | :--: | :--: | ------------------------------------------------------------ |
| **IP**           |  √   |      | 远程主机的IP地址                                             |
| **操作系统类型** |  √   |      | 远程主机的操作系统，可以是 `windows` 或者 `linux`，其中windows可以简写为 `win` |
| 名称             |  √   |      | 远程主机的名称，用名称比用IP更便于区分                       |
| 路由IP           |  √   |      | 如果远程主机需要使用“端口映射”模式进行连接，则需要填写路由IP |
| 路由端口         |  √   |      | “端口映射”模式时使用的端口                                   |
| 资产编号         |  √   |      | 自行设置的编号，可用于资产管理                               |
| **账号**         |      |  √   | 登录远程主机的账号                                           |
| **协议类型**     |      |  √   | 登录远程主机的协议，可以是 `rdp`，`ssh`，或者 `telnet`       |
| **协议端口**     |      |  √   | 对应远程主机协议的端口，例如，默认情况下rdp为`3389`，ssh为`22`，telnet为`23`。**注意，如果使用端口映射模式，则不能填写协议端口，因为端口映射模式下，远程连接必须通过路由端口进行。** |
| **认证类型**     |      |  √   | 远程账号登陆时的验证方式，可以是密码验证（`pw`），私钥验证（`key`）或者无验证（`no`）。其中，rdp协议必须使用pw，ssh协议可以使用pw或者key，而telnet协议则可以使用pw或者no。 |
| **密码或私钥**   |      |  √   | 根据认证类型字段，填写账号的密码或者私钥，如果认证类型为no，则本字段不用填写。 |
| 账号提示         |      |  √   | **仅用于telnet协议**，预期的提示输入账号的提示语             |
| 密码提示         |      |  √   | **仅用于telnet协议**，预期的提示输入密码的提示语             |
| 分组             |  √   |  √   | 对主机或账号进行分组，方便管理。一个主机或者账号可以属于多个组，多个组之间可以用竖线符号`|`分隔开。 |
| 描述             |  √   |  √   | 为主机或账号编写描述信息，相当于备注                         |

#### 1.3.2 批量导入常见问题

- 在编辑资产信息文件期间，页面会话已经超时了，需要重新登录后再执行导入操作。
- 主机和账号写到同一行上了。
- 逗号分隔符数量不正确。例如密码中包含英文逗号，但是在资产文件中逗号被当做分隔符使用，导致格式错误。或者用低版本的Excel编辑后保存，有可能导致某些行的逗号丢失。
- 文本编辑工具或者Excel打开后是乱码。请更换编辑工具或高版本的Excel。

## 二、分组管理

### 2.1 主机分组管理

为便于管理（例如运维授权或者审计授权时），可以将主机进行分组。

分组操作非常简单，在“资产-主机分组管理”页面，点击右上角的“创建主机分组”按钮并填写分组名称，即可建立一个新的分组。

分组建立之后，点击某个分组的名称，即可进入对应分组的主机成员列表页面，管理此分组中的主机。在此页面中可以添加或者删除主机。

向分组中添加主机，或者删除分组，均不影响现存的主机数据，但如果此分组已经在运维策略或者审计策略中被授权了，那么删除分组会影响到分组中所有主机的授权。

### 2.2 账号分组管理

账号分组管理功能与主机分组的操作一致，此处不再赘述。

## 三、分组最佳实践

在teleport系统中，主机、账号、用户均可以进行分组管理，而且，每一项也可以属于多个分组。例如，一台运行数据库的Linux主机，可以属于 “Linux” 分组，同时可以属于 “数据库” 分组，还可以属于 “阿里云ECS” 分组。

灵活使用分组，可以高效快捷地管理主机、账号和用户，尤其是在管理大量资产时，利用分组可以大大减少管理难度。下面通过几个示例进行说明，当然，需要根据你的实际使用场景进行灵活处理。

### 3.1 资产管理

某公司有大量虚拟主机，部分运行在云端，部分运行在自建机房，为了方便管理，可以从不同分类的维度创建一些主机分组来进行区分，例如：

- 按地域或机房划分：总部机房/自建-华东区/自建-西南区/阿里云/腾讯云/AWS/...
- 按设备类型划分：物理主机/虚拟机/交换机/路由器/...
- 按用途划分：防火墙/网关/蜜罐系统/数据库服务器/应用发布服务器/...

这样可以在主机管理界面通过分组过滤器快速分拣出需要的主机列表。

### 3.2 运维授权管理

虽然teleport支持将主机授权给某个用户，从而使用户可以使用该主机上的任意一个账号进行远程连接、运维，但从日常使用场景来看，给运维人员分配的权限往往需要映射到具体的某台主机的某个账号上。

在实际工作中，会因为运维人员的经验、岗位等不同而为其分配不同的远程账号。例如，有经验的运维人员可以为其分配远程主机的root账号，而数据库运维人员会为其分配一些运行数据库服务的主机的dba账号。因此，可以从不同维度创建一些账号分组来进行方便授权操作，例如：

- 按业务类型划分：www/database/自动构建/系统管理员/...
- 按地域划分：总部机房管理员账号/华东办事处管理员账号组/...

同样的，也可以对用户进行分组，比如：

- 按岗位划分：管理员组/QA用户组/运维组/...
- 按职责划分：应用发布组/灰度测试组/基础部署组/...

因为teleport可按组进行授权，因此设置好授权策略并按分组进行授权之后，后续有主机、账号或者运维人员的变更时，只需要将其加入对应的一个或者多个组（或者从相应组的成员列表中移除），即可获得或失去对应的授权。











