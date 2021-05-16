Linux 开发环境主要用于开发 Teleport 的服务端。

## 一、基本信息

- 系统：CentOS 7 x64
- 编译器：gcc-8

## 二、安装/配置开发环境

**🔔 可以在虚拟机中配置一套开发teleport服务端的开发环境。非商业用途，可以从 VmWare 官网下载 VmWare Player 15 进行安装使用。
因为要进行大量编译操作，建议虚拟机配置为：CPU: 4核，内存: 8GB，硬盘: 120GB**

- 从 CentOS 官网下载 CentOS 7 的最小安装镜像，CentOS-7-x86_64-Minimal-2009.iso，然后正常安装。

- 配置为阿里云的CentOS源：
```bash
# 切换为root用户身份
su
# 安装必要的网络工具
yum install -y wget
# 备份源配置
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
# 换为阿里云的源
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
# 略作调整，将CentOS-Base.repo中的所有http改为https
vi /etc/yum.repos.d/CentOS-Base.repo
# 更新缓存
yum clean all
yum makecache
# 更新已安装的软件
yum update
```

- 如果在 VmWare 虚拟机中安装，建议安装 VmWare-Tools
```bash
# 切换为root用户身份
su
# 安装 VmWare-Tools
yum install -y open-vm-tools
```

- 最小化安装的系统中是没有开发工具的，如果直接  yum install gcc，则安装的是gcc 4.x。我们希望安装 gcc-8。
```bash
# 切换为root用户身份
su
# 安装 gcc-8 相关软件、调试器等
yum install -y centos-release-scl-rh devtoolset-8-build devtoolset-8-gdb devtoolset-8-gcc devtoolset-8-gcc-c++
# 应用 gcc-8，使之成为默认编译器（但是关闭终端或者重启系统后，就失效了）
source /opt/rh/devtoolset-8/enable
# 查看 gcc 版本
gcc --version
```

- 设置开机就默认使用 gcc-8
```bash
# 切换为root用户身份
su
vi /etc/profile
```

在最后一行加入以下内容
```bash
source /opt/rh/devtoolset-8/enable
```

- 安装其他必要的工具和开发库
```bash
yum install -y git zip unzip
yum install -y libffi-devel zlib-devel
```

- 安装 CMake。yum 安装的 cmake 为2.x版，但我们想要使用 3.x 版，需要源码编译安装，这比较麻烦。
偷个懒，使用CLion内带的高版本 CMake。从 JetBrains 官网下载最新版本的 Linux 版 CLion，解压缩，将里面的cmake拿来用。
```bash
# JetBrains 官网地址: https://www.jetbrains.com/ 下载最新版本的 CLion，然后：
tar zxvf CLion-2021.1.1.tar.gz
# 切换为root用户身份
su
# 将 CLion 内置的 CMake 复制到系统目录下
cp clion-2021.1.1/bin/cmake/linux/bin/* /usr/local/bin/.
# 查看 CMake 版本（目前是3.19）
cmake --version
```

- 配置工作目录（以下操作均为普通用户身份进行，不建议使用 root 身份）。下面假定将代码 clone 到当前用户的 `work/tp4a/teleport` 目录下，并在下文中以 `${TP}` 来指代这个源码根目录。

```bash
cd ~
mkdir -p work/tp4a
cd work/tp4a
git clone git@github.com:tp4a/teleport.git teleport
```


## 三、编译Python

teleport 服务端安装包内建 Python 运行环境和必要的库，目前采用源码编译的方式进行编译，编译工作已经使用脚本进行封装，只需要执行编译命令即可自动完成源代码下载、解压、编译等工作。

在编译前，还需要做一点点准备工作：
```
# 进入teleport源代码根目录
cd ~/work/tp4a/teleport
# 复制一个配置文件，用于配置 linux 环境下的工具链
cp conf.json.in conf.linux.json
# 修改 conf.linux.json，主要是要配置其中的 CMake 路径
# 将 toolchain 部分修改为：
    "toolchain": {
        "pyexec": "",
        "qt_path": "",
        "cmake": "/usr/local/bin/cmake"
    },
```

然后就可以开始编译了，编译时间非常的长，建议开始编译后出去走动一下，活动活动筋骨。
```bash
./make.sh
```

**🔔需要注意的是：因为常见的网络抽风的情况，有时候下载源代码包可能会失败，导致编译失败。遇到问题不要慌，检查一下文件是否下载正常。**

下载的文件位于 `${TP}/external/_download_` 目录下。正在下载的文件以 `._tmp_` 为后缀。如果文件下载失败，极有可能文件大小为 0  字节，需要将 0 字节的文件删除，并重新执行 `./make.sh` 脚本。

一切正常的情况下，经过漫长的编译过程，最终会进入到 teleport 的构建环境中，界面如下所示：
```plain
[]==========================================================[]
 | Teleport Projects Builder v2.0                           |
 | auth: apex.liu@qq.com                                    |
[]==========================================================[]

=====================[ MENU ]===============================

  prepare for server [build once]:
  [ 1] Make Python-Runtime for python37-x64

  server side:
  [ 2] build external libraries for server
  [ 3] build server applications [release]
  [ 4] make server installer for linux

  clear:
  [ 5] clear external libraries for server

  misc:
  [ 6] update version setting

  ----------------------------------------------------------
  [ Q] exit

select action: 
```

以后要编译 teleport 服务端，也只需要再次执行 `./make.sh` 即可进入这个界面。

【未完，待续】
