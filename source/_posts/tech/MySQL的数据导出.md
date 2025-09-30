---
title: MySQL的数据导出
tags:
  - MySQL
published: true
abbrlink: 16579
date: 2022-03-23 10:03:56
category:
	- 技术
---
一般情况下，我们都是使用的`Navicat`等的可视化工具进行数据导出。只有在特殊情况下，无法通过可视化工具进行数据库连接，只能通过命令行来操作，此时就需要用到`mysqldump`工具了。

以下内容摘自 [musqldump原理](https://www.cnblogs.com/markLogZhu/p/11398028.html)

## mysqldump

> `mysqldump`是`MySQL`自带的逻辑备份工具。
> 
> 它的备份原理是通过协议连接到`MySQL`数据库，将需要备份的数据查询出来，将查询出的数据转换成对应的`insert`语句，当我们需要还原这些数据时，只要执行这些`insert`语句，即可将对应的数据还原。

这里，我们可以通过以下格式进行操作：

```shell
mysqldump [选项] 数据库名 [表名] > 脚本名
```

或

```shell
mysqldump [选项] --数据库名 [选项 表名] > 脚本名
```

或

```shell
mysqldump [选项] --all-databases [选项]  > 脚本名
```

| 参数名                             | 缩写       | 含义                |
|---------------------------------|----------|-------------------|
| --host                          | -h       | 服务器IP地址           |
| --port                          | -P       | 服务器端口号            |
| --user                          | -u	MySQL | 用户名               |
| --password                      | -p	MySQL | 密码                |
| --databases                     |          | 指定要备份的数据库         |
| --all-databases                 |          | 备份mysql服务器上的所有数据库 |
| --compact                       |          | 压缩模式，产生更少的输出      |
| --comments                      |          | 添加注释信息            |
| --complete-insert               |          | 输出完成的插入语句         |
| --lock-tables                   |          | 备份前，锁定所有数据库表      |
| --no-create-db/--no-create-info |          | 禁止生成创建数据库语句       |
| --force                         |          | 当出现错误时仍然继续备份操作    |
| --default-character-set         |          | 指定默认字符集           |
| --add-locks                     |          | 备份数据库表时锁定数据库表     |

### 举个例子

备份`dbname`下`tablename`表的表结构与数据。

```shell
mysqldump -u root -p dbname tablename > /backup/mysqldump/tablename.sql
```

上述命令中，`-u`表示了用户名，后面的`root`就是`-u`的参数；`-p`表示了密码，这里就会在执行此行命令后要求输入数据库中用户名为`root`的密码；`dbname`表示数据库名；`tablename`表示表名称；>表示输出重定向；`/backup/mysqldump/tablename.sql`表示了输出的文件路径。
