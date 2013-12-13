---
title: Map/Reduce爬虫
author: gsh199449
layout: post
permalink: /?p=18
categories:
  - Hadoop
format: aside
---
input -> map -> shuffle -> reduce -> output

> 1.  input时先把文件变成<行偏移量，此行的文字>
> 2.  map函数将input的结果进行处理，变成<K,V>的形式，然后Sort
> 3.  然后通过Shuffle在当前节点将相同的Key的Value合并(merge)，变成<K,[V1,V2,V3····]>
> 4.  然后传到Reducer节点进行reduce处理

因为从Mapper节点向Reducer节点传输消耗网络带宽，所以要尽可能在Mapper上把能处理的数据尽情处理，不需要的数据丢掉。这样在向Reducer上copy时就可以尽可能的节省带宽。

map，shuffle都在map节点进行，reduce在另外的reduce上节点进行。

<!--more-->

&nbsp;