**Q：使用MySQL数据库，有时重启teleport服务后工作不正常。** _（注：v3版本已经修正了此问题）_

A：Teleport内建支持SQLite，因此 `/etc/init.d/teleport` 启动脚本没有加入对mysqld服务的依赖，导致有时teleport服务先于mysqld服务启动，此时teleport服务会因为无法连接到数据库而工作不正常。解决办法是修改`/etc/init.d/teleport`脚本，将mysqld加入到依赖的服务列表中：在脚本开始处，找到 `# Required-Start:` 一行，在这一行末尾，追加 ` mysqld`，然后重启teleport服务即可。
