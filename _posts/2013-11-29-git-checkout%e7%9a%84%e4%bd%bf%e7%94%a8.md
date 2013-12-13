---
title: git checkout的使用
author: gsh199449
layout: post
permalink: /?p=173
video_format_choose:
  - youtube
qode_hide-featured-image:
  - no
categories:
  - Git
---
HEAD可以理解为“头指针”，表示当前工作区的“基础版本”，当执行提交的时候，Head的指向将作为新提交的父提交。

<pre name="code" class="java">$ git branch -v # 查看当前在那个分支
</pre>

下面的操作就叫做git的检出

<pre name="code" class="java">$ git checkout 76fb
M       README.md
Note: checking out '76fb'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b new_branch_name

HEAD is now at 76fb938... add Bye.xtx
</pre>

分离头指针状态（detached HEAD state）就是说HEAD指向的是一个具体的提交，而不是一个引用（分支）。  
此时查看reflog即可发现HEAD由指向master分支变为指向一个一个提交ID

<pre name="code" class="java">$ git reflog --oneline -1
76fb938 HEAD@{0}: checkout: moving from master to 76fb
</pre>

checkout与reset不同，master并没有改变。  
下面测试在“断头模式”下的提交

<pre name="code" class="java">$ git checkout 7945
M       README.md
Note: checking out '7945'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b new_branch_name

HEAD is now at 79452f4... add  Hi
</pre>

首先checkout到一个提交，然后新建一个文件，add然后commit。这时我们可以在日志里面看到提交信息。

<pre name="code" class="java">GS@GS-PC /d/Workspaces/GitHubRepository/GitStudy ((79452f4...))
$ git commit -m "commit in detached HEAD"
[detached HEAD d4406c9] commit in detached HEAD
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 detachedHEAD.txt
</pre>

在提交中也会提示你现在处于“断头模式”。这时日志中出现我们刚刚的提交。

<pre name="code" class="java">GS@GS-PC /d/Workspaces/GitHubRepository/GitStudy ((d4406c9...))
$ git log --oneline
d4406c9 commit in detached HEAD #最新的提交
79452f4 add  Hi
74d5a3d First Comm
2307227 Merge https://github.com/gsh199449/GitStudy
43b9c5e First Commit
e8493e4 First
6754302 Initial commit
</pre>

然后我们checkout到master分支

<pre name="code" class="java">GS@GS-PC /d/Workspaces/GitHubRepository/GitStudy ((d4406c9...))
$ git checkout master
M       README.md
Warning: you are leaving 1 commit behind, not connected to
any of your branches: #在这里我们看到这个提交没有在连接到任何分支上面

  d4406c9 commit in detached HEAD

If you want to keep them by creating a new branch, this may be a good time
to do so with:

 git branch new_branch_name d4406c9

Switched to branch 'master'
Your branch is behind 'GitStudy/master' by 6 commits, and can be fast-forwarded.

  (use "git pull" to update your local branch)
</pre>

再次查看日志我们发现刚才的提交已经没了，而且在工作区中文件也消失了。

<pre name="code" class="java">GS@GS-PC /d/Workspaces/GitHubRepository/GitStudy (master)
$ git log --oneline
76fb938 add Bye.xtx
79452f4 add  Hi
74d5a3d First Comm
2307227 Merge https://github.com/gsh199449/GitStudy
43b9c5e First Commit
e8493e4 First
6754302 Initial commit
</pre>

虽然使用show命令还可以查看，但是git不知道什么时间就会把他彻底的从历史中抹去。

<pre name="code" class="java">$ git show d4406
commit d4406c941f7d154e0fe87afc41e3e578d2303733
Author: gsh199449 &lt;gsh199449@hotmail.com>
Date:   Fri Nov 29 12:37:48 2013 +0800

    commit in detached HEAD

diff --git a/detachedHEAD.txt b/detachedHEAD.txt
new file mode 100644
index 0000000..e69de29
</pre>

如果要挽救这个提交可以使用merge命令

<pre name="code" class="java">$ git merge d4406
Merge made by the 'recursive' strategy.
 detachedHEAD.txt | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 detachedHEAD.txt
</pre>

merge命令就是把某个分支合并到当前分支，这是我们可以看到日志和工作区都出现了那次提交。

<pre name="code" class="java">$ git log --oneline
3f6488e Merge commit 'd4406'
d4406c9 commit in detached HEAD
76fb938 add Bye.xtx
79452f4 add  Hi
74d5a3d First Comm
2307227 Merge https://github.com/gsh199449/GitStudy
43b9c5e First Commit
e8493e4 First
6754302 Initial commit
</pre>

同时，在76fb上出现了开发分支

<pre name="code" class="java">$ git log --graph --oneline
*   3f6488e Merge commit 'd4406'
|\
| * d4406c9 commit in detached HEAD
* | 76fb938 add Bye.xtx
|/
* 79452f4 add  Hi
</pre>

我们看到这一次的提交出现了两个父提交

<pre name="code" class="java">$ git cat-file -p HEAD
tree d42b037f806ee5afd2265c94e0fe15f6cb4578c6
parent 76fb93819096c6f7c182218eeaeb18b2ad275b48
parent d4406c941f7d154e0fe87afc41e3e578d2303733
author gsh199449 &lt;gsh199449@hotmail.com> 1385700536 +0800
committer gsh199449 &lt;gsh199449@hotmail.com> 1385700536 +0800

Merge commit 'd4406'
</pre>

git checkout &#8212; filename命令可以用暂存区里面的filename文件来覆盖工作区中的的filename文件。