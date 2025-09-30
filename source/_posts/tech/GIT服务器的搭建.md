---
title: GIT服务器的搭建
tags:
  - 教程
  - Git
published: true
abbrlink: 46417
date: 2022-02-25 10:16:52
category:
	- 技术
---
本篇只有git服务器的基础搭建教程，没有git的其他教程。

## 准备

● 能安装git的服务器一枚，性能不限，存储空间需要视仓库总容量而定，最少200MB。推荐Linux服务器。  
● 需要用git的客户端一枚（不然服务器给谁用）

## 服务器环境

● 需要与客户机互通  
● 需要开放`9418`端口（git的默认访问端口）

## 搭建服务器

1. 安装git

    没什么好说的，就是安装git。当然，想自己写一个git也不是不行。

    ```shell
    apt-get install git (ubuntu)
    yum install git (centos)
    ```

2. 选择git仓库总地址

    例如我们选择/user/project/git作为我们仓库的存储文件夹。

    ```shell
    mkdir /usr/project/git;
    cd /usr/project
    ```

3. 创建一个git仓库

    这里使用了git的init命令。

    ```shell
    git init --bare mygit.git
    ```

    `--bare`后面我们写上我们的一个仓库名称，这里的仓库名称叫做`mygit.git`。你也可以用如下的仓库名，不过不推荐。

    ```shell
    git init --bare mygit.awsl
    ```

4. 创建访问用户

    有了仓库，我们就需要对仓库进行权限管理，就是设置一下访问用户。
    访问git仓库的方式有很多，我们这里不考虑ssh的方式，直接用用户名与密码，简单粗暴。

    ```shell
    useradd xiaoming;
    passwd xiaoming; (请自行设定密码)
    ```

    这里我们添加了一个叫xiaoming的用户。

5. 将访问用户授权到仓库

    我们把之前创建的`mygit.git`仓库授权给`xiaoming`

    ```shell
    chown -R xiaoming mygit.git
    ```

    这个其实就是将文件夹权限给了系统用户，而这个也是git的权限判定策略。
    到这里其实服务器就差不多了。

## 配置客户机

1. 安装git

    请下载对应客户机系统的git，然后装上，谢谢。

    客户机配置就完成了，是不是很简单。

## 拉取项目

1. 创建项目目录

    省略一万个字。
    需要注意的是，拉取的项目会生成在这个文件夹下的子文件夹中，子文件夹名称就是项目名称。

2. 在这个项目目录下进行git指令操作

    window用户可以在项目文件夹下使用右键调出快捷菜单，然后点击git bash进入git命令行。
    linux用户直接用输入git指令就行。

3. 通过clone命令从git服务器拉取已有项目

    之前我们在git服务器上创建了一个项目`mygit.git`，我们现在就把这个项目拉取下来。

    ```shell
    git clone xiaoming@192.168.10.1:/usr/project/git/mygit.git
    ```

    从命令中可以看出来，@符号前面的是用户，后面跟的是服务器IP地址。:符号后面的就是git项目路径了。
    在命令运行后，会要求你输入xiaoming的密码，这个密码就是之前在服务器中设定的密码，输入后，项目就拉取下来了。

## 推送项目

1. 假装搞项目

    我们为了体验git的作用，就在这个文件夹里随意新建几个文本文件，当然，你也可以把一些什么视频啊、游戏啊放进来，不过不推荐。

2. 将需要管理的文件推送到暂存区

    git的管理属于被动式的，就是你要给它说它需要管理的东西，不然它就不会去管理。就像是我们之前创建的几个文本文件，虽然是属于git管理的目录下，但是它并不会把这些文件纳入管辖范畴，最多就是在你某些操作的时候提醒你还有文件可以管理。
    这里使用`add`命令来添加文件：

    ```shell
    git add 这个文件.txt;
    git add 那个文件.txt;
    ```

    然后这些文件就被添加到了暂存区。
    为什么是暂存区呢？反正目前你只需要知道在暂存区的文件就会受到git的管理就行了，关于git的详细资料请移步

    ● [W3CSchool的git教程](https://www.w3cschool.cn/git/)  
    ● [菜鸟的git教程](https://www.runoob.com/git/git-tutorial.html)

3. 推送到本地仓库

    git的管理首先是要推送到本地仓库的，然后才是远程仓库。也就是从暂存区将文件给到本地仓库。
    这里用`commit`命令：

    ```shell
    git commit -m '这里写上本次提交的意义或是内容'
    ```

4. 推送到远程仓库

    远程仓库可以是任何的git仓库，前提是你在仓库有推送权限。
    这里用`push`命令推送：

    ```shell
    git push origin master;
    ```

   `origin`表示的是远程仓库名称，默认的就是`origin`，`master`表示推送到`master`分支。

对于新建的项目提交，需要使用其他的方法，本篇不做讲解。

## 完成

以上，完成。
