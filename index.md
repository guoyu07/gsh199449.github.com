---
layout: page
title: 高莘的博客
tagline: Supporting tagline
---
{% include JB/setup %}

欢迎来到高莘的博客

## 我的主要项目

- 基于Hadoop的分布式爬虫 [DistributeCrawler](https://github.com/gsh199449/DistributeCrawler)
- 多线程单节点爬虫 [MyLucene](https://github.com/gsh199449/MyLucene)
- 家庭电量管理系统 ([window版](https://github.com/gsh199449/ElecForm) | [Web版](https://github.com/gsh199449/Elec))
- 电量管理系统的WebServer [ElecWebService](https://github.com/gsh199449/ElecWebService)
- 模拟登录河北科技大学教务系统并读取成绩的成绩查看器  [URPScanner](https://github.com/gsh199449/URPScanner)
- 图书管理系统 [LibrarySystem](https://github.com/gsh199449/LibrarySystem)
    
## 日志列表

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## 联系我

Email : gsh199449@hotmail.com

微博 : <a href="http://weibo.com/u/2511405337?s=6uyXnP" target="_blank"><img border="0" src="http://service.t.sina.com.cn/widget/qmd/2511405337/ef835547/7.png"/></a>


