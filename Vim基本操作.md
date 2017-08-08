---
title: Vim 基本操作
date: 2017-06-27 02:16:16
tags: [Linux,Vim]
categories: 技术
---

## 1.打开文件

安装 vim，进入相应文件夹，输入

$ vim filename

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707270006.png)

即可打开相应的文件，我们这里打开一个名为 **vimtest.txt** 的文件。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707270007.png)

**K、J、H、K** 分别对应着上下左右，当然，小键盘左边的方向键也是可以实现光标移动的。



## 2.插入字符

现在我们想要在文件的末尾插入一行字符：**helloworld !** 

把光标移动到文件末尾，然后按下字母键 **i** 。我们可以看到光标从覆盖整个字符变成了一个白色的下划线。

然后在文件的末尾输入我们想要输入的字符：**helloworld！**

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707270008.png)

现在我们已经输入进去一行字符，那么，怎么保存退出呢？

首先按下【ESC】进入命令模式，然后输入一个命令：

```
:wq
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707270009.png)

然后我们就成功地保存退出了。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707270010.png)

再次打开这个文件，发现我们刚才保存的内容仍然是存在的。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707270011.png)

学会了插入，删除也是同理，至少现在我们已经能够将 vim 当做记事本用了。但是，如果我们仅仅掌握这点功能的话，还不如使用 notepad++ 来得方便，所以接下来我们介绍一些高级点的技能。



## 3.字符搜索

输入：

```
/hello
```

即可完成在全文中对字符串 hello 的搜索。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707270012.png)

输入：

```
n
```

即可跳转到下一处。当然你也可以按 **i** 进入插入模式来修改字符，不过如果你想替换全文中所有的 **hello** 的话，那么 :s 也许是一个不错的选择。

```
:s/hello/hi/g
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707270013.png)

如图所示，我们已经把文中所有的 **hello** 替换成了 **hi** 。这貌似也没什么大不了，毕竟我用 **notepad++** 也可以实现同样的工作。那么下面的问题可就是 **notepad++** 实现不了的了。

我们现在要把前三行所有的 **i** 替换成 **w**。

注意，我们只替换前三行。嗯，这个问题对于 notepad++ 来说可能有点变态，不过对于 vim ……

```
:1,3s/i/w/g
```

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707270014.png)

只有前三行的 **i** 被替换成了 **w**。命令结尾的 **g** 表示会对所有符合情况的 i 进行操作，否则只替换第一个。



## 4.分屏操作

说到 vim，就一定不能够错过它的分屏功能。输入

```
:sp
```

就能得到上下两个屏幕，这对于只能显示黑框框的 shell 来说确实是很必要的功能。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707270015.png)

当然，如果两个屏幕只能看一个文件，那还不如一个屏幕，选择一个屏幕（如果没有鼠标的话可以用）

```
Ctrl + w + j  //(j 的意思是“下”)
```

来实现切换，然后输入：

```
:open car.txt
```

来实现文档的切换。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201707270016.png)

**vim** 还有一个类似的功能叫做 **newtab**，可以像浏览器一样显示标签，具体我就不在赘述了，有兴趣的话可以查阅后面的表格。



## 5.常用功能一览

### 5.1.跳转

```
*          当光标停留在一个单词上，* 键会在文件内搜索该单词，并跳转到下一处；
#          当光标停留在一个单词上，# 在文件内搜索该单词，并跳转到上一处；
(/)        移动到 前/后 句 的开始；
{/}        跳转到 当前/下一个 段落 的开始。
g_         到本行最后一个不是 blank 字符的位置。
fa         到下一个为 a 的字符处，你也可以fs到下一个为s的字符。
t,         到逗号前的第一个字符。逗号可以变成其它字符。
3fa        在当前行查找第三个出现的 a。
F/T        和 f 和 t 一样，只不过是相反方向;
gg         将光标定位到文件第一行起始位置；
G          将光标定位到文件最后一行起始位置；
NG或Ngg    将光标定位到第 N 行的起始位置。
```

### 5.2.搜索

```
/str1                正向搜索字符串 str1；
n                    继续搜索，找出 str1 字符串下次出现的位置；
N                    继续搜索，找出 str1 字符串上一次出现的位置；
?str2                反向搜索字符串 str2 。
```

### 5.3.删除

```
rc                 用 c 替换光标所指向的当前字符；
nrc                用 c 替换光标所指向的前 n 个字符；
5rA                用 A 替换光标所指向的前 5 个字符；
x                  删除光标所指向的当前字符；
nx                 删除光标所指向的前 n 个字符；
3x                 删除光标所指向的前 3 个字符；
dw                 删除光标右侧的字；
ndw                删除光标右侧的 n 个字；
3dw                删除光标右侧的 3 个字；
db                 删除光标左侧的字；
ndb                删除光标左侧的 n 个字；
5db                删除光标左侧的 5 个字；
dd                 删除光标所在行，并去除空隙；
ndd                删除（剪切） n 行内容，并去除空隙；
3dd                删除（剪切） 3 行内容，并去除空隙；
```

### 5.4.替换

```
s                用输入的正文替换光标所指向的字符；
S                删除当前行，并进入编辑模式；
ns               用输入的正文替换光标右侧 n 个字符；
nS               删除当前行在内的 n 行，并进入编辑模式；
cw               用输入的正文替换光标右侧的字；
cW               用输入的正文替换从光标到行尾的所有字符（同 c$ )；
ncw              用输入的正文替换光标右侧的 n 个字；
cb               用输入的正文替换光标左侧的字；
ncb              用输入的正文替换光标左侧的 n 个字；
cd               用输入的正文替换光标的所在行；
ncd              用输入的正文替换光标下面的 n 行；
c$               用输入的正文替换从光标开始到本行末尾的所有字符；
c0               用输入的正文替换从本行开头到光标的所有字符。
```

### 5.5.复制粘贴

```
yy              复制当前行到内存缓冲区；
nyy             复制 n 行内容到内存缓冲区；
5yy             复制 5 行内容到内存缓冲区；
“+y             复制 1 行到操作系统的粘贴板；
“+nyy           复制 n 行到操作系统的粘贴板。
p               小写字母 p，将缓冲区的内容粘贴到光标的后面；
P               大写字母 P，将缓冲区的内容粘贴到光标的前面。
```

### 5.6.正则表达式

```
^                放在字符串前面，匹配行首的字；
$                放在字符串后面，匹配行尾的字；
\<               匹配一个字的字头；
\>               匹配一个字的字尾；
.                匹配任何单个正文字符；
[str]            匹配 str 中的任何单个字符；
[^str]           匹配任何不在 str 中的单个字符；
[a-b]            匹配 a 到 b 之间的任一字符；
*                匹配前一个字符的 0 次或多次出现；
\                转义后面的字符。
```



## 6.参考链接

[vim 入门基础](http://www.jianshu.com/p/bcbe916f97e1)

[vim 官方文档](http://vimdoc.sourceforge.net/)