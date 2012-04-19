# 安装 Play 2.0

## 前置条件

运行Play Framework，你需要 [[JDK 6 或更高版本 | http://www.oracle.com/technetwork/java/javase/downloads/index.html]]. 

> 如果你使用的是Mac系统，Java已经内置了. 如果你使用的是Linux，请确保Java版本为Sun JDK 或 OpenJDK (不能是一些发行版上默认的那种gcj). 如果你使用windows，请直接下载并安装最新的JDK.

请确保 `java` 和 `javac` 在你的PATH路径下 (你可以通过命令行命令 `java -version` 和 `javac -version` 确认). 

## 下载二进制发行版本

下载 [[Play 2.0 二进制发行版 | http://download.playframework.org/releases/]] (最新的 2.0 版本) ，解压到任一拥有读写权限的目录. (`play`命令会在当前目录下写一些文件，所以请确保不要解压在 `/opt`, `/usr/local`或其他你没有写权限的路径.)

## 把play命令脚本添加到Path路径

为了使用方便，你可以把play目录添加到系统的PATH路径. 在类Unix系统下，你可以这么做:

```bash
export PATH=$PATH:/path/to/play20
```

在window下，你需要设置系统的环境变量.

> 如果你使用Unix类系统，请确保`play`脚本文件有执行权限 (或者直接执行 `chmod a+x play`).

## 检查play命令有效

从命令行执行 `play help` 命令即可

```bash
$ play help
```

如果所以事情都搞定，你可能想要看一下帮助内容:

[[images/play.png]]

> **Next:** [[创建一个新的应用 | NewApplication]]