---
title: Centos7中搭建Minecraft官服
tags:
  - 教程
  - Minecraft
published: true
abbrlink: 50282
date: 2022-02-25 10:16:14
category:
	- 技术
---
整体流程分为环境搭建、服务端启动、服务端配置、运行维护四个环节。

## 环境搭建

Miecraft官服只需要安装`Java`环境即可，不过一般我们会用其他的辅助管理工具。

1. 安装`screen`，用于服务器后台管理

    ```shell
    yum install screen
    ```

2. 安装`lrzsz`，用于上传和下载

    ```shell
    yum install lrzsz
    ```

3. 安装Java，最基本的服务器环境（注意，`1.18`版本的Minecraft需要`Java17`，其他版本使用`Java8`即可）

    ```shell
    yum -y install java-1.8.0-openjdk*
    ```

4. 上传并启动服务端

    1. 创建服务器目录

        ```shell
        cd /usr/games       (进入/usr/games目录)
        mkdir minecraft     (创建minecraft目录)
        ```

    2. 上传服务端文件

        ```shell
        cd /usr/games/minecraft (进入minecraft目录)
        rz                      (在弹出窗口中选择需要启动的服务端文件或整合包)
        ```

    3. 开启视窗

        ```shell
        screen -S minecraft (创建并进入minecraft视窗)
        ```

## 服务端启动

1. 第一次启动服务端

    ```shell
    java -Xmx1024M -Xms1024M -jar minecraft_1.14_server.jar nogui
    (启动文件名为minecraft_1.14_server.jar的服务端)
    ```

    第一次启动时，会告知需要编辑许可文件。此时服务端会自动关闭，直到许可被同意，操作如下。

2. 编辑许可与vi编辑器的基本使用

   * 在minecraft目录下操作。
   * `vi eula.txt`    (用vi编辑器修改eula.txt文件)
   * 用键盘的方向键将光标移至文本末尾，按下A键，启动编辑模式。
   * 将`eula=false`改为`eula=true`。
   * 随后按下ESC键退出编辑模式。
   * 按住`SHIFT`和L键旁边的`:`，启动命令模式，此时左下角会出现冒号。
   * 输入`wq`（w表示write，q表示quit），按下`ENTER`键，保存退出。

## 服务端配置

1. 服务器参数设置

    ```shell
    vi server.properties    (用vi编辑器修改服务器配置)
    ```

    没有正版的玩家可以将`online=true`改为`online=false`(关闭在线验证)
    其他参数参考 [wiki](https://minecraft.fandom.com/zh/wiki/Server.properties)

2. 重新启动服务端

    当服务端在运行时，可以在服务端内(`screen`视窗内)使用`stop`指令来正常停止服务器。也可以通过`ps -ef | grep server`指令找到服务端对应的运行进程，使用`kill`指令来强制停止(无法正常保存世界数据，不推荐)

    当服务端停止运行时，可以用以下命令启动：

    ```shell
    java -Xmx1024M -Xms1024M -jar minecraft_1.14_server.jar nogui
    (启动文件名为minecraft_1.14_server.jar的服务端)
    ```

    `-Xmx`表示服务器能用的理论最大内存量  
    `-Xms`表示服务器初始时使用的内存量  
    出现`Done For Help`表示启动完成。

3. 退出视窗

    按住`CTRL`，随后先后分别按下`A`和`D`键，及可退出`screen`视窗

4. 退出xshell

    ```shell
    exit    (退出)
    ```

## 服务器维护

1. 进入minecraft视窗

    ```shell
    screen -r minecraft            (进入minecraft视窗)
    ```

1. 关闭服务器

    进入`minecraft`视窗，输入`stop`

注意：
服务器重启后，视窗需要重建。

## 附录

* [Minecraft官服下载](https://www.minecraft.net/en-us/download/server)
* [MinecraftWiki](https://minecraft.fandom.com/zh/wiki/Minecraft_Wiki)
* [MiecraftBBS](https://www.mcbbs.net/)
* [MinecraftMod](https://www.mcmod.cn)
