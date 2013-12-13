---
title: 使用ubuntu搭建wordpress本地环境
author: gsh199449
layout: post
permalink: /?p=38
categories:
  - 运维
format: aside
---
Ubuntu确实很好玩。有喜欢的命令行，简洁的界面，不同于Window要的感觉。偶尔换换环境工作，学习Linux的思维方式，是一种不错的做法。之前也折腾过Ubuntu，不过，因为网络的问题，一直没有深度去用。这次，网络方便了，并且，想在Linux下学习某些开发（主要还是和代码打交道），Ubuntu当然是最好不过的选择，并且刚发布了9.04呢。  
开发，当然就会需要环境。Wordpress是自己熟悉的，并且工作在LAMP上，所以，配个LAMP环境，来学习PHP和其他开发，多好！今天，就来记下使用Ubuntu搭建wordpress本地环境的手记，嘿，记录记录，相信也会帮上某些朋友的忙。  
1. 安装apache、php5、mysql  
首先，我们来安装apache和php5，按下面的步骤，一步一步来安装：  
sudo apt-get install apache2  
sudo apt-get install php5  
sudo apt-get install libapache2-mod-php5  
sudo /etc/init.d/apache2 restart // 重启apache，此时php5已经可用了  
接下来，我们安装mysql：  
sudo apt-get install mysql-server  
sudo apt-get install libapache2-mod-auth-mysql  
sudo apt-get install php5-mysql  
sudo /etc/init.d/apache2 restart // 再次重启apache，使新服务正常激活  
然后在终端输入：  
sudo ls /etc/apache2/mods-enabled  
看看这个目录下，有没有php5.conf 和 php5.load，如果没有则：  
sudo a2enmod php5  
启用 php 模块，然后重启apache即可。哦耶，这里，apache、php5、mysql都已经可用了。这里，当然，也可以安装wordpress了。不过，别急，让我们来做一些让我们使用起来更方便的准备先。  
2.安装phpmyadmin  
在phpmyadmin网站上下载软件包，解压缩到本地目录/var/www/phpmyadmin（/home/user/www/phpmyadmin）。在终端下执行：  
sudo cp /var/www/phpmyadmin/config.sample.inc.php /var/www/phpmyadmin/config.inc.php  
sudo gedit /var/www/phpmyadmin/config.inc.php  
找到&#8221;blowfish_secret&#8221;在后面填上任意字母。保存，退出！并开始安装php5-mcrypt：  
sudo apt-get install php5-mcrypt  
编辑php配置文件:  
sudo gedit /etc/php5/apache2/php.ini  
在extension下面加上  
extension=php5-mcrypt.so  
3.安装wordpress  
这里，你可以到wordpress.org下载最新的wordpress安装包，解压，把wordpress包放到/var/www/目录下，就可以按WP的安装方法来安装了。哦也，你一定很高兴。并且，你很想修改主题，对其进行工作。  
但，我知道，你发现了一个问题！类似于：主题文件不能在后台修改/后台Import不了xml。囧！是的，这个问题很烦，不过，只要你打开终端，输入下面的命令：  
sudo chmod -R 777 /var/www/  
把/var/www/目录的权限设计为777，DONE！哦也，你可以为此欢呼了。嘿嘿！