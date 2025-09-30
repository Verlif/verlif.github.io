---
title: Ubuntu服务器开Minecraft1.19-Forge版本记录
tags:
    - 教程
    - Minecraft
published: true
abbrlink: 8118
date: 2024-04-08 09:22:06
category:
	- 技术
---
之前都是开的基于1.18版本Forge服务器，和1.16那些相比，服务端文件从**jar**包换成了运行**脚本**，更轻量化了，但是也的确更麻烦了。

昨天开了一个1.19.2的Forge服务器，本来以为也就那样，结果遇到了各种问题，这里做一下记录。

## 获取Forge服务端

我是从Forge官网下载的**jar**包，在Windows上运行生成好服务端文件后，再上传到服务器的。

这个是下载地址：[ForgeFor1.19.2](https://files.minecraftforge.net/net/minecraftforge/forge/index_1.19.2.html)

![Forge](/images/1712539813912.png)

然后双击运行下载的**jar**包，选择`Install server`，然后选择一个空文件夹点击**确定**。

![Forge运行窗口](/images/1712540002784.png)

这里在**install**的时候，网络可能不稳定，下载失败了可以把目录清空重新运行下载。

安装成功后，目标目录下应该有`run.bat`和`run.sh`这两个启动文件，`run.bat`是给Windows用的，你可以直接在Windows上运行测试。

## 从服务器运行

首先，Minecraft1.19.2需要的运行环境是**Java17**，所以需要用一下命令安装17版本：

```shell
apt install openjdk-17-jdk
```
    
如果已经安装的话用，可以用这个命令检查Java版本：

```shell
java -version
```

多个版本可以使用这个命令进行版本切换：

```shell
update-alternatives --config java
```

然后我们安装一个**Screen**，用来做运行窗口管理，方便后台运行和管理服务端：

```shell
apt install screen
```

也就是说到这里，我们已经有Java17的运行环境和**Screen**的管理工具了，此时将之前生成的Forge服务端文件夹上传到服务器，然后就可以开始踩坑了。

## 进入Screen

如果直接在终端运行run.sh的话，这个终端就会被运行时指令输出占用，导致你没法进行其他操作，所以我们就需要使用**Screen**来生成虚拟窗口，也允许其他终端访问。

通过这个命令开启新的窗口：

```shell
screen -R mc
```

这里的`mc`就是一个名字，可以换成自己的。如果已经有了或是忘记了，可以用这个命令查看窗口列表：

```shell
screen -ls
```

从窗口回到终端时，通过**按住**`CTRL`，然后**依次**按下`A`和`D`即可。回到窗口可以通过这个命令：

```shell
screen -r mc
```

这里的`mc`就是之前开启新窗口的名字。

## 运行Forge服务端

运行的话直接在服务端目录下通过`./run.sh`运行脚本即可，这里可能会遇到两个问题：

1. Permission denied

    直接对Forge文件夹进行`chomd -R 777 forge`，这个forge就是你的服务端目录。

2. 没有许可条款

    在首次运行Minecraft服务端时，都会生成一个`eula.txt`并自动中断运行，此时你只需要编辑服务端根目录下的`eula.txt`文件，将里面的`false`改为`true`就表示已同意条款，再次运行即可。

## 关闭更新

去`config`目录，然后依次检查运行生成的所有配置，关闭里面的`update`选项。如果不知道这是什么，可以跳过这个步骤。

## 运行窗口问题

如果你在运行的时候，使用了类似于新版**MobaXterm**这种支持**X server**服务的终端工具的话，它会开启Forge服务端所需要的`X11 window server`。这表示你会在本地看到一个Java写的服务端运行窗口，包括了运行日志、基本的服务占用信息和命令输入行。

看起来好像方便本地管理服务端，其实并不是，因为你不能关闭这个窗口，否则服务端会直接终止。这和你本地运行服务端没什么区别，除了没有资源占用之外。

如果没有这样的窗口，那你可能会遇到另一个问题，就是服务端卡住，一般就是服务端没有启动完毕，但是日志停止输出。

服务端卡住的原因很多，有一种可能是各种mod正在做更新查询，但是网络环境不行导致的网络等待，这种情况下停止服务器然后去配置文件里关闭更新选项就可以了。还有一种情况就是这个该死的`X11 window server`，然后也有可能服务端没有卡住，直接报错`Can't connect to X11 window server`。

遇到这个问题的话，直接关闭服务端，修改`user_jvm_args.txt`文件，在其中添加这个参数：

```txt
-Djava.awt.headless=true
```

这个其实就是**JVM**参数，和`-Xmx4G`同级，中间用空格隔开就好了。

## 启动

到这里基本上就没什么问题了，除非你的模组冲突。

完结，撒花。