---
title: Oracle的一些命令
tags:
  - Oracle
published: true
abbrlink: 64170
date: 2024-01-19 17:20:16
category:
	- 技术
---
老是遇到一些Orcale的问题，这里整理一下。

## 账户相关

### 查询某个账户的配置

`select profile from dba_users where username = '${username}';`

- `${username}`表示了查询的账户，这里要用单引号。

### 查询账户配置细节

与**查询某个账户的配置**结合使用。

`select * from dba_profiles where profile = '${profilename}';`

- `${profilename}`表示了账户配置的名称，这里要用单引号。

### 修改账户密码

`alter user ${username} identified by ${password};`

- `${username}`表示了要修改的账户。
- `${password}`表示了新密码。

### 解锁账户

`alter user ${username} account unlock;`

- `${username}`表示了要解锁的账户。

### 查询账户密码过期信息

`SELECT username, account_status, expiry_date FROM dba_users;`

## 数据相关

### 查看行数据修改记录

`SELECT * FROM ALL_TAB_MODIFICATIONS WHERE TABLE_NAME = '${tableName}';`

- `${tableName}`表示了表名称，这里要用单引号。

### 正则查询

正则查询使用的是`REGEXP_LIKE`函数，实际上还有`REGEXP_INSTR`、`REGEXP_SUBSTR`、`REGEXP_REPLACE`等，都是支持正则表达式输入。

`SELECT * FROM ${tableName} WHERE REGEXP_LIKE(${colname}, '${regex}')`

- `${tableName}`表示表名
- `${colname}`表示需要匹配的列名
- `${regex}`表示列数据匹配的正则表达式，这里要用单引号。

`REGEXP_LIKE`还支持参数设置，例如`REGEXP_LIKE(${colname}, '${regex}', 'i')`表示忽略大小写。
