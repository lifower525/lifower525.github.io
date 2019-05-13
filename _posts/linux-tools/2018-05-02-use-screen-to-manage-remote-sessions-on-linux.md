---
layout: post
title: Linux 上使用 Screen 管理远程会话
category: [linux-tools]
author: Howie Lee
tags: [Linux,Screen]
---

## 关于Screen
**Screen**是一款由GNU计划开发的用于命令行终端切换的自由软件。用户可以通过该软件同时连接多个本地或远程的命令行会话，并在其间自由切换。GNU Screen可以看作是窗口管理器的命令行界面版本。它提供了统一的管理多个会话的界面和相应的功能。

对于经常需要SSH或者telnet的后端开发人员来说，某些需要长时间运行的任务经常因为网络原因等问题而中途连接断开导致前功尽弃，使用Screen管理远程会话可以很好的解决这些问题。

## 安装Screen
Screen的安装很简单，以CentOS为例，直接通过如下命令进行安装即可。

```sh
yum install screen
```
Screen的官网链接是：<http://www.gnu.org/software/screen/>

可以通过`git clone git://git.savannah.gnu.org/screen.git` 获取Screen的源码。
## Screen常用命令
```sh
$ screen [-AmRvx -ls -wipe][-d <作业名称>][-h <行数>][-r <作业名称>][-s ][-S <作业名称>]
```
各个命令的参数解释如下：

```sh
-A 　将所有的视窗都调整为目前终端机的大小。
-d <作业名称> 　将指定的screen作业离线。
-h <行数> 　指定视窗的缓冲区行数。
-m 　即使目前已在作业中的screen作业，仍强制建立新的screen作业。
-r <作业名称> 　恢复离线的screen作业。
-R 　先试图恢复离线的作业。若找不到离线的作业，即建立新的screen作业。
-s 　指定建立新视窗时，所要执行的shell。
-S <作业名称> 　指定screen作业的名称。
-v 　显示版本信息。
-x 　恢复之前离线的screen作业。
-ls或--list 　显示目前所有的screen作业。
-wipe 　检查目前所有的screen作业，并删除已经无法使用的screen作业。
```

## Screen常用参数
```bash
screen -S task -> 新建一个叫task的session
screen -ls -> 列出当前所有的session
screen -r task -> 回到task这个session
screen -d task -> 远程detach某个session
screen -d -r task -> 结束当前session并回到task这个session
```
在每个screen session 下，所有命令都以 ctrl+a(C-a) 开始。

```
C-a ? -> 显示所有键绑定信息
C-a c -> 创建一个新的运行shell的窗口并切换到该窗口
C-a n -> Next，切换到下一个 window 
C-a p -> Previous，切换到前一个 window 
C-a 0..9 -> 切换到第 0..9 个 window
Ctrl+a [Space] -> 由视窗0循序切换到视窗9
C-a C-a -> 在两个最近使用的 window 间切换 
C-a x -> 锁住当前的 window，需用用户密码解锁
C-a d -> detach，暂时离开当前session，将目前的 screen session (可能含有多个 windows) 丢到后台执行，并会回到还没进 screen 时的状态，此时在 screen session 里，每个 window 内运行的 process (无论是前台/后台)都在继续执行，即使 logout 也不影响。 
C-a z -> 把当前session放到后台执行，用 shell 的 fg 命令则可回去。
C-a w -> 显示所有窗口列表
C-a t -> time，显示当前时间，和系统的 load 
C-a k -> kill window，强行关闭当前的 window
C-a [ -> 进入 copy mode，在 copy mode 下可以回滚、搜索、复制就像用使用 vi 一样
    C-b Backward，PageUp 
    C-f Forward，PageDown 
    H(大写) High，将光标移至左上角 
    L Low，将光标移至左下角 
    0 移到行首 
    $ 行末 
    w forward one word，以字为单位往前移 
    b backward one word，以字为单位往后移 
    Space 第一次按为标记区起点，第二次按为终点 
    Esc 结束 copy mode 
C-a ] -> paste，把刚刚在 copy mode 选定的内容贴上
```
## 使用Screen
Screen是一个可以在多个进程之间多路复用一个物理终端的窗口管理器。Screen中有会话的概念，用户可以在一个screen会话中创建多个screen窗口，在每一个screen窗口中就像操作一个真实的telnet/SSH连接窗口那样。
### 创建新的会话
直接采用`screen`命令就可以创建一个Screen会话，为了管理方便，推荐给每个会话起一个单独的名字

```bash
# 创建一个名为task1的会话
screen -S task1
```

### 会话分离与恢复
Screen允许用户在不中断正在运行的程序的情况下断开Screen会话的连接，在需要重新控制该会话中运行的程序时候再重新连接该会话。

```bash
# 新建一个Screen会话
screen vim hello_world.c
# 通过Ctrl+a，输入命令d来断开会话后，查看会话列表
screen -list
# 找到刚才断开的会话的ID，重新连接该会话的ID，以会话ID为9645为例
screen -r 9645
```
如果没有分离会话，就没有办法恢复会话，这个时候需要强制分离会话，通过以下命令：

```bash
screen -d
```
### 会话清除
人为杀死会话或者由于某种原因会话被kill，这个时候`screen -list`显示该会话为`dead`状态，此时需要清除该会话。

```bash
screen -wipe
```

## 参考资料
GNU Screen的官方网站：<http://www.gnu.org/software/screen/>

Man Screen命令为本文提供了大量的信息支持
