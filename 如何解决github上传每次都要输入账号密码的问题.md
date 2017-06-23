---
title: 如何解决github上传每次都要输入账号密码的问题
date: 2017-06-23 22:35:01
tags: [SCAtoolbox,Matlab]
categories: 技术
---

### 1.需要输入账号密码的原因

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706230001.png)

之所以每次上传都需要输入账号密码，是因为使用了 https 的方式进行 push。只要改成 ssh 的方式就不必输入账号密码了。



### 2.操作步骤

1. git remote -v
2. git remote rm origin
3. git remote add origin git@github.com:JackSmithThu/MarkdownPhotos.git
4. git push origin
5.  git push --set-upstream origin master

上面的 **jackSmithThu** 和 **MarkDownPhotos** 需要改成你 **github** 的**用户名**和**项目名**( 第5步中，set 前面是两个 “-”，出于 hexo 的原因。直接复制可能无法使用，建议粘贴 **git bash** 里面的提示)。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201706230002.png)

### 3.参考链接
[push到github时，每次都要输入用户名和密码的问题](http://blog.csdn.net/yuquan0821/article/details/8210944)