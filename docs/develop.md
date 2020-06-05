## Windows上搭建前端web的开发环境

**前提条件：**

```
代码位置：git@github.com:eomsoft/teleport.git
python环境：32位的python3.7版本
开发环境： pytharm
```

**搭建过程：**

1、拉取代码

```
cd E:\git_workspace\teleport

git clone git@github.com:eomsoft/teleport.git

```

2、下载python3.7的32位版本，下载embed版本即可（因为小）

```
下载地址：https://www.python.org/ftp/python/3.7.7/python-3.7.7-embed-win32.zip
```

3、下载pytharm并安装

```
下载地址：http://www.jetbrains.com/pycharm/download/

简单的安装教程：https://www.jianshu.com/p/bf14e7634d30
```

4、配置开发环境：

```
将python解压到本地目录：D:\Python\python-3.7.7-embed-win32\下

在这个文件夹下，创建一个新文件，名字为：dev_mode    #注意不能有扩展名，与python.exe同级
```

启动pycharm，导入teleport的web项目：
![web-ops-auz-1](img/develop_pytharm-1.png)

配置python环境：<br/>
![web-ops-auz-1](img/develop_pytharm-2.png)

![web-ops-auz-1](img/develop_pytharm-3.png)

重新启动pytharm。<br/>
配置web.ini文件
```
cd E:\git_workspace\teleport\server\share\etc

拷贝 web.ini.in，同文件夹下重命名为：web.ini
```

在pytharm中，启动项目：
![web-ops-auz-1](img/develop_pytharm-4.png)

浏览器访问：http://localhost:7190
<br/>
打完收工。