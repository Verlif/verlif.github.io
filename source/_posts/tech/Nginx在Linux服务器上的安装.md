---
title: Nginx在Linux服务器上的安装
tags:
  - 教程
  - Nginx
published: true
abbrlink: 55359
date: 2022-02-25 10:15:29
category:
	- 技术
---
首先最常用的就是在线安装了，比如`yum install nginx`，在线安装一般都不会有什么大问题，这里就不赘述了，就只谈谈离线安装的问题。

1. 下载Nginx

    下载可以是在本机下载，然后上传到服务器，也可以直接在服务器下载。
    官网下载地址是:
    <http://nginx.org/en/download.html>
    下载tar.gz版本就可以了。

2. 解压文件

    确定好解压文件夹后，通过`tar -zxvf [filename]`来解压文件。
    解压后会根据nginx版本生成文件夹，例如`nginx-1.20.0`，然后cd进去。

3. 检查依赖与安装依赖

    在解压后的nginx文件夹下， 通过`./configure`命令来检测依赖与执行基础配置。如果有需要而未安装的依赖，会有命令提示。
    依赖包括但不限于：

    > gcc  
    pcre  
    pcre-devel  
    zlib  
    zlib-devel

    依赖的安装请自行尝试，这里不做赘述。

4. 编译与安装

    `./configure`成功之后，会生成`makeFile`文件，用于描述编译信息。这时在nginx目录下，通过`make`命令执行编译操作，过程需要几分钟。
    在编译完成后，执行`make install`命令安装。
    安装完成后，可以通过`whereis nginx`命令查看安装路径，一般都是`/usr/local/nginx`。

5. 启动

    在安装路径下，有一个`sbin`目录，其中的nginx就是可执行文件。通过`./nginx`来做临时启动。
    剩下的就没什么可说了。

6. 配置

    nginx的基础配置文件是安装目录下的`config/nginx.conf`，在这里面可以修改配置。修改后在`sbin`目录下中执行`nginx -t`来检测配置文件的正确性。

    一般情况下，我们都会使用额外的配置文件，所以需要在`config/nginx.conf`中添加`include`参数，例如`include /usr/local/nginx-conf/*.conf;`，这样，nginx就会在运行时包括`/usr/local/nginx-conf`下的所有配置文件了。

    另外就是添加新的代理，这里我们以新的配置文件`test.conf`做演示。

    ```nginx
    server {
        # 监听的端口
        listen  81;
        # 代理的前端文件路径
        root    /usr/local/html/test;
        
        # url监听地址
        location / {
            # 首页文件名
            index index.html;
        }
    }
    ```

    随后`nginx -t`检查配置是否正确，检测通过后可以通过`nginx -s reload`热重载。
    此时就可以通过访问`IP:81`来查看前端页面了。
