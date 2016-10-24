title: Ubuntu14.04上搭建lamp环境并修改www目录
tags: [ubuntu]
layout: post
categories: Love
date: 2014-05-14
---

## 前言

前几天换了个系统ubuntu14.04。我也推荐下，很好用。没游戏很适合学生用。哈哈哈

接着步入正题吧。

首先搭建lamp系统有很多种方法，我这是通过终端下载软件库一步一步搭建并配置一些基本的东西。这样来呢。是免得麻烦，相应的php，apache，mysql版本也还够新。

这是我14年5月10号安装是各个软件的版本：

<!--more-->

apache:Apache/2.4.7 (Ubuntu)

php:5.5.9-1ubuntu4

mysql:5.5.37

可以看出ubuntu的库还是比较新的。对了，我的软件源是网易的。如果不晓得软件源怎么更换请自行百度。

## 安装apache

``` bash
$ sudo apt-get update
$ sudo apt-get install apache2
```

apache默认是对外全部开放的，如果只是自己做开发用，为了安全着想，应该修改apache对外服务的端口:

``` bash
$ sudo gedit /etc/apache2/ports.conf
```

将下面的
Listen 80
改成
Listen 127.0.0.1:80
然后保存.
测试:
http://localhost or http://127.0.0.1
显示Apache2 Ubuntu Default Page则说明安装成功。

## 安装php

``` bash
$ sudo apt-get install php5
```
安装php与apache协同工作模块
sudo apt-get install libapache-mod-php5
其实这一步可以不做，因为安装php5的时候已经自动安装这个模块。

测试:
重启Apache

``` bash
$ sudo /etc/init.d/apache2 restart
```

```
sudo gedit /var/www/html/test.php
```

(这里有些人的地址是/var/www)输入

``` php
<?php phpinfo(); ?>
```

保存，在浏览器输入

http://localhost/test.php

显示php信息说明安装成功

*目录/var/www/html是安装apache后自动生成的，apache提供服务的根目录。（后面有如何改根目录的内容）

### 安装mysql

``` bash
$ sudo apt-get update
$ sudo apt-get install mysql-server
```

安装过程要输入mysql密码
安装mysql之后默认只有本机可以访问，如果需要开放给其他机器或者internet，需修改mysql配置

``` bash
$ sudo gedit /etc/mysql/my.cnf
```

重启mysql

``` bash
$ sudo /edt/init.d/mysql restart
```

### 安装mysql与apache协同工作模块

``` bash
$ sudo apt-get install libapache2-mod-auth-mysql
```

### 安装mysql与php协同工作模块

``` bash
$ sudo apt-get install php5-mysql
```
测试mysql

``` bash
$ sudo gedit /var/www/mysql_test.php
```

你可以在这里直接测试mysql是否安装上。用以下代码(或则安装phpmyadmin的时候再测试)：

``` php
<?php
$link = mysql_connect(“localhost”, “root”, “your password”);
if (!$link) {
die(‘Can not connect to mysql’ . mysql_error());
} else {
echo ‘Mysql done’;
}
mysql_close($link);
?>
```

## 安装phpmyadmin

``` bash
$ sudo apt-get install phpmyadmin
```

安装过程选择服务器，选择apache，期间还要输入phpmyadmin密码，还有要输入mysql密码
建议phpmyadmin运行目录到apache网站服务根目录的软连接
``` bash
$ sudo ln -s /usr/share/phpmyadmin /var/www/html(有的为var/www)
```

测试
http://localhost/phpmyadmin
用户名:phpmyadmin
密码:安装过程中输入的密码

## 修改文件根目录

网上有很多种…其实都是同一种。需要修改两个东西。
一个是下面的红色字体改为你认定的目录。我这就是/home/hiluluke/www

```
<Directory /home/hiluluke/www>
Options Indexes FollowSymLinks
AllowOverride None
Require all granted
</Directory>
```

然后是将下面DocumentRoot也改为上面一样的目录。

```
DocumentRoot /home/hiluluke/www
```

为啥要这样讲呢。。。因为这两个东西可能不在一个配置文件中。
但是一般都在两个文件中，一个是000-default.conf
一个是apache2.conf
这两个文件一般都在/etc/apache2中。用搜索就能找到。
