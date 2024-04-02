---
layout: post #Do not change.
category: [operate_system] #One, more categories or no at all.
title:  "Basic Linux Operations"
date:   2024-04-01 18:37:00 +0800
author: eef #Author's nick.
#nextPart: _posts/2021-01-30-example.md #Next part.
#prevPart: _posts/2021-06-16-syntax-example.md #Previous part.
#og_image: assets/example.png #Open Graph preview image.
og_description: "记录一些常用的linux操作" #Open Graph description.
fb_app_id: example
---

## 一些常用的linux指令
### tips

分享一个好用的学习linux及操作系统的网站[蓝桥](https://www.lanqiao.cn/)，网站上提供了实验平台可以让新手练习，非常友好。

### linux终端
我们在使用 Linux 时，一般是通过 Shell 程序来访问操作系统内核的服务。Shell 是指“提供给使用者使用界面”的软件（命令解析器），类似于 DOS 下的 command（命令行）和Windows下的 cmd.exe。普通意义上的 Shell 就是可以接受用户输入命令的程序。UNIX/Linux 操作系统下的 Shell 既是用户交互的界面，也是控制系统的脚本语言。在 UNIX/Linux 中比较流行的常见的 Shell 有 bash、zsh、ksh、csh 等等，Ubuntu 终端默认使用的是 bash。

#### 常用快捷键
`TAB` 自动补全命令 &emsp; `CTRL+C` 终止命令 &emsp;  `CTRL+D`   退出终端，相当于EXIT
#### 通配符
`*` 匹配0或多个字符  &emsp; `?`匹配任意一个字符  &emsp;`[list]` 匹配list中的单一字符  

`[^list] ` 匹配除list以外的单一字符  &emsp; `{string1,string2,...}` 匹配{...}中的其中一字符串 

`[c1-c2]`匹配c1-c2中的单一字符，如0-9，a-z  &emsp; `{c1,..,c2}`匹配c1-c2的全部字符 （中间是两个点）

#### 创建`touch`&删除`rm`

```bash
# 创建一个名为file的文件
$ touch file
# 删除一个名为file的文件
$ touch file
$ rm -f file # 强制删除
$ rm -r path # 删除目录 （强制+f）
```
#### 输出文本到显示窗口`echo`&打印文件内容到终端`cat`
```bash
# 在终端上输出 Hello World
$ echo "hello world"
hello world
# 向file文件(如果目录中没有file文件就创建一个)中写入内容"word1"
$ echo "word1">file
# 向file文件中追加内容"word2"
$ echo "word2" >> file
# 顺序打印内容到终端 逆序是tac
$ cat file # 输出
word1
word2
```

#### 目录相关

```bash
# 查看当前所在目录
$ pwd
# 进入一个目录
$ cd /etc/
# 创建目录 & 删除目录
$ mkdir file 
$ mkdir -p father/son # 同时创建父目录
$ rmdir file
# 查看文件权限
$ ls # 列出子文件 -l 长格式显示 -a 显示所有文件（包括隐藏）
```
显示格式

`drwxr-xr-x` &emsp; `person`  &emsp;`groups` &emsp; `1024`  &emsp;`April 4 2021`  &emsp;`Documents`

文件类型与权限  所有者  &emsp;用户组  &emsp;文件大小 &emsp; 最后修改时间 &emsp; 文件名

##### 关于文件权限

每个文件都有三个固定的权限（拥有者-用户组-其他用户），读、写、执行分别对应`r`、`w`、`x`，以二进制的形式表示，有权限为1，无权限为0，每三位为一个十进制整数。

例如某文件权限为`rw-rw-rw-` 对应`110110110`，即权限为`666`。


`ls -asSh` 显示所有文件的大小，以直观的形式显示 h(human-readable)

`ls -l a.txt` 查a.txt的信息


