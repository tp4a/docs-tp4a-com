## 简介

本项目用于生成 [docs.tp4a.com](http://docs.tp4a.com/) 的内容，是 teleport 项目的在线说明文档。

Teleport是一款简单易用的堡垒机系统，具有小巧、易用的特点，支持 RDP/SSH/SFTP/Telnet 协议的远程连接和审计管理。

## 帮助改进teleport在线说明文档

随着teleport的持续更新，逐渐的会导致文档与实际功能、特性脱节，需要能及时更新。希望你能伸出援手，共同助力teleport的发展。

### 如何做？

Teleport的在线说明文档使用 [mkdocs](https://www.mkdocs.org/) 工具进行构造，所有文档内容均采用 Markdown 格式，因此，你需要先准备以下环境：

- Python，建议使用最新版本，例如 [miniconda](https://conda.io/miniconda.html)；
- 使用 pip 安装 mkdocs；
- git 客户端；

以上环境的安装配置可以google或百度，有很多安装配置的说明文档可以参考。

### 然后呢？

首先你需要从本项目 fork 一个你自己的版本，并且使用git客户端下载到本地。然后在本地修改 docs 目录下的 .md 文件。你需要[了解一点 Markdown 的语法](https://www.jianshu.com/p/191d1e21f7ed)，不过相信我，真的很简单。

编辑时，可以打开一个命令行窗口，然后切换到你下载的项目的根目录下，并执行：

```bash
mkdocs serve
```

这样你就可以在浏览器中访问 `http://127.0.0.1:8000`来实时查看你的编辑成果了。没错，这里说的是**实时成果**，因为 mkdocs 会监控你的修改，一旦你保存了文件，那么浏览器页面会自动刷新，从而能立刻看到编辑后渲染出来的网页文档。

最后，当你确定修改完成，可以提交你的修改内容到此项目中，具体可以看 github 的 pull-request 的相关说明。如果一切都正常，那么你的修改将会合并到本项目中，并且更新到 docs.tp4a.com 的服务器上，你的编辑成果将会被大家看到，为大家带去帮助！