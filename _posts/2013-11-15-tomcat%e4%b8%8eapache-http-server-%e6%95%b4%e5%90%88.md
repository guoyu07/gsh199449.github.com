---
title: Tomcat与Apache HTTP server 整合
author: gsh199449
layout: post
permalink: /?p=12
categories:
  - 运维
format: aside
---
在/etc/httpd/conf/httpd.conf末尾加入：

> ProxyPass /\*.jsp\* ajp://127.0.0.1:8009/  
> ProxyPass /caifeng ajp://127.0.0.1:8009/  
> ProxyPassReverse / ajp://127.0.0.1:8009/

将所有名为caifeng的请求转发至tomcat 而不影响其他php应用

详情请查看：[轻松实现Apache_Tomcat集群和负载均衡][1]

 [1]: http://202.206.64.193/wordpress/wp-content/uploads/2013/11/轻松实现Apache_Tomcat集群和负载均衡.doc