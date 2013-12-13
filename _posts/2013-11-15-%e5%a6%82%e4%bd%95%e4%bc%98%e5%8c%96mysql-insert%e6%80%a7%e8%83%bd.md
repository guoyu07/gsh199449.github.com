---
title: 如何优化MySQL insert性能
author: gsh199449
layout: post
permalink: /?p=24
categories:
  - Java
format: aside
---
&nbsp;

对于一些数据量较大的系统，面临的问题除了是查询效率低下，还有一个很重要的问题就是插入时间长。我们就有一个业务系统，每天的数据导入需要4-5个钟。这种费时的操作其实是很有风险的，假设程序出了问题，想重跑操作那是一件痛苦的事情。因此，提高大数据量系统的MySQL insert效率是很有必要的。

经过对MySQL的测试，发现一些可以提高insert效率的方法，供大家参考参考。

**1. 一条SQL语句插入多条数据。**

常用的插入语句如：

<div>
  <code>INSERT</code> <code>INTO</code> <code>`insert_table` (`datetime`, `uid`, `content`, `type`) </code><code>VALUES</code> <code>(</code><code>'0'</code><code>, </code><code>'userid_0'</code><code>, </code><code>'content_0'</code><code>, 0);</code>
</div>

<div>
  <code>INSERT</code> <code>INTO</code> <code>`insert_table` (`datetime`, `uid`, `content`, `type`) </code><code>VALUES</code> <code>(</code><code>'1'</code><code>, </code><code>'userid_1'</code><code>, </code><code>'content_1'</code><code>, 1);</code>
</div>

<div>
  <div id="highlighter_293906">
  </div>
</div>

修改成：

`INSERT` `INTO` `` `insert_table` (`datetime`, `uid`, `content`, `type`)  ```VALUES` `(``'0'``, ``'userid_0'``, ``'content_0'``, 0), (``'1'``, ``'userid_1'``, ``'content_1'``, 1);`

修改后的插入操作能够提高程序的插入效率。这里第二种SQL执行效率高的主要原因有两个，一是减少SQL语句解析的操作， 只需要解析一次就能进行数据的插入操作，二是SQL语句较短，可以减少网络传输的IO。

这里提供一些测试对比数据，分别是进行单条数据的导入与转化成一条SQL语句进行导入，分别测试1百、1千、1万条数据记录。

<a title="如何优化MySQL insert性能" href="http://cdn2.jobbole.com/2012/10/sql-insert-01.png" rel="lightbox[29432]"><img title="如何优化MySQL insert性能" alt="如何优化MySQL insert性能" src="http://cdn2.jobbole.com/2012/10/sql-insert-01.png" width="314" height="140" /></a>

**  
****2. 在事务中进行插入处理。**  
把插入修改成：

<div>
  <code>START </code><code>TRANSACTION</code><code>;</code>
</div>

<div>
  <code>INSERT</code> <code>INTO</code> <code>`insert_table` (`datetime`, `uid`, `content`, `type`) </code><code>VALUES</code> <code>(</code><code>'0'</code><code>, </code><code>'userid_0'</code><code>, </code><code>'content_0'</code><code>, 0);</code>
</div>

<div>
  <code>INSERT</code> <code>INTO</code> <code>`insert_table` (`datetime`, `uid`, `content`, `type`) </code><code>VALUES</code> <code>(</code><code>'1'</code><code>, </code><code>'userid_1'</code><code>, </code><code>'content_1'</code><code>, 1);</code>
</div>

<div>
  <code>...</code>
</div>

<div>
  <code>COMMIT</code><code>;</code>
</div>

使用事务可以提高数据的插入效率，这是因为进行一个INSERT操作时，MySQL内部会建立一个事务，在事务内进行真正插入处理。通过使用事务可以减少创建事务的消耗，所有插入都在执行后才进行提交操作。

这里也提供了测试对比，分别是不使用事务与使用事务在记录数为1百、1千、1万的情况。

<a title="如何优化MySQL insert性能" href="http://cdn2.jobbole.com/2012/10/sql-insert-02.png" rel="lightbox[29432]"><img title="如何优化MySQL insert性能" alt="如何优化MySQL insert性能" src="http://cdn2.jobbole.com/2012/10/sql-insert-02.png" width="260" height="147" /></a>

**性能测试：**  
这里提供了同时使用上面两种方法进行INSERT效率优化的测试。即多条数据合并为同一个SQL，并且在事务中进行插入。

<a title="如何优化MySQL insert性能" href="http://cdn2.jobbole.com/2012/10/sql-insert-03.png" rel="lightbox[29432]"><img title="如何优化MySQL insert性能" alt="如何优化MySQL insert性能" src="http://cdn2.jobbole.com/2012/10/sql-insert-03.png" width="354" height="121" /></a>

从测试结果可以看到，insert的效率大概有50倍的提高，这个一个很客观的数字。**  
**

**注意事项：**

1. SQL语句是有长度限制，在进行数据合并在同一SQL中务必不能超过SQL长度限制，通过max\_allowed\_packet配置可以修改，默认是1M。

2. 事务需要控制大小，事务太大可能会影响执行的效率。MySQL有innodb\_log\_buffer_size配置项，超过这个值会日志会使用磁盘数据，这时，效率会有所下降。所以比较好的做法是，在事务大小达到配置项数据级前进行事务提交。