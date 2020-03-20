---
layout: post
title:  "从数据库中生成pdm"
date:   2019-01-29
categories:
 - PowerDesigner
tags:
 - mysql
color: rgb(230,230,250)
cover: '../assets/blogimg/2018-10-24.png'
---
# 从 mysql 中导出 pdm 步骤记录

- mysql 版本：5.7
- PowerDesigner 版本：15.2

首选导出建表语句，然后打开PowerDesigner， 选择文件-> 反向工程 -> database
	
![](/images/pdm/1.png)

设置模型名称，点击确定。选择
## 配置path环境变量
	
	MYSQL_HOME：mysql安装路径
	<img src="/assets/imgages/2019/01290003.png">
	PATH:%MYSQL_HOME%\bin
	<img src="/assets/imgages/2019/01290004.png">

## 初始化服务信息
	
	- 在mysql安装目录下 即bin文件夹同级 新建 data文件夹和my.ini文件,my.ini内容如下，用window记事本打开保存，ANSI编码。

		<pre>
		[mysqld]
		# 设置3306端口
		port=3306

		# 设置mysql的安装目录
		basedir=D:/mysql-8.0.14

		# 设置mysql数据库的数据的存放目录
		datadir=D:/mysql-8.0.14/data
		# 允许最大连接数

		max_connections=200
		# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
		max_connect_errors=10
		# 服务端使用的字符集默认为UTF8
		character-set-server=utf8
		# 创建新表时将使用的默认存储引擎
		default-storage-engine=INNODB
		[mysql]
		# 设置mysql客户端默认字符集
		default-character-set=utf8mb4
		[client]
		# 设置mysql客户端连接服务端时默认使用的端口
		port=3306
		default-character-set=utf8
		</pre>

	- 以管理员的身份打开cmd窗口 初始化服务：

	mysqld --initialize --user=mysql --console
	
	初始化完成之后，会生成一个临时密码,将此密码复制保存

	<img src="/assets/imgages/2019/01290005.png">

## 安装服务

	mysqld -install

## 启动服务

	net start mysql

	<img src="/assets/imgages/2019/01290006.png">

## 修改密码 

	mysql -u root -p登录数据库 输入上面的临时密码

	ALTER USER root@localhost IDENTIFIED  BY '123456'; 将密码为123456

[jekyll-docs]: https://www.baidu.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
