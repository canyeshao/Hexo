---
title: scp命令
date: 2016-03-10 20:20:07
tags: Linux
categories: "Linux"
---


#  scp命令

这篇文章主要是参考了[http://blog.csdn.net/jiangkai_nju/article/details/7338177](http://blog.csdn.net/jiangkai_nju/article/details/7338177)这个博客，要看详细的内容可以参考这个博客进行学习研究，但是我觉得在以下的一些基本可以满足我们的文件传输要求了。

scp是linux中功能最强大的文件传输命令，可以实现从本地到远程以及远程到本地的轻松文件传输操作。下面简单的讲解一些关于scp命令的操作，给有用的人一些参考：

首先是本地到远程的操作：操作的格式如下

```bash
scp local_file remote_username@remote_ip:remote_folder
或者
scp local_file remote_username@remote_ip:remote_file
或者
scp local_file remote_ip:remote_folder
或者
scp local_file remote_ip:remote_file
```
第1,2个指定了用户名，命令执行后需要再输入密码，第1个仅指定了远程的目录，文件名字不变，第2个指定了文件名；
第3,4个没有指定用户名，命令执行后需要输入用户名和密码，第3个仅指定了远程的目录，文件名字不变，第4个指定了文件名；
* 例子：
```bash
scp /home/space/music/1.mp3 root@www.cumt.edu.cn:/home/root/others/music
scp /home/space/music/1.mp3 root@http://www.cumt.edu.cn:/home/root/others/music/001.mp3
scp /home/space/music/1.mp3 www.cumt.edu.cn:/home/root/others/music
scp /home/space/music/1.mp3 http://www.cumt.edu.cn:/home/root/others/music/001.mp3
```
* 复制目录：
* 命令格式：
```bash
scp -r local_folder remote_username@remote_ip:remote_folder
或者
scp -r local_folder remote_ip:remote_folder
```
第1个指定了用户名，命令执行后需要再输入密码；
第2个没有指定用户名，命令执行后需要输入用户名和密码；
* 例子：
```bash
scp -r /home/space/music/ root@www.cumt.edu.cn:/home/root/others/
scp -r /home/space/music/ www.cumt.edu.cn:/home/root/others/
```
上面 命令 将 本地 music 目录 复制 到 远程 others 目录下，即复制后有 远程 有 ../others/music
2、是实现从远程到本地的文件传输操作：
从 远程 复制到 本地，只要将 从 本地 复制到 远程 的命令 的 后2个参数 调换顺序 即可；
例如：
```bash
scp root@www.cumt.edu.cn:/home/root/others/music /home/space/music/1.mp3
scp -r www.cumt.edu.cn:/home/root/others/ /home/space/music/
```
最简单的应用如下 :
scp 本地用户名 @IP 地址 : 文件名 1 远程用户名 @IP 地址 : 文件名 2
[ 本地用户名 @IP 地址 :] 可以不输入 , 可能需要输入远程用户名所对应的密码 .
可能有用的几个参数 :
-v 和大多数 linux 命令中的 -v 意思一样 , 用来显示进度 . 可以用来查看连接 , 认证 , 或是配置错误 .
-C 使能压缩选项 .
-P 选择端口 . 注意 -p 已经被 rcp 使用 .
-4 强行使用 IPV4 地址 .
-6 强行使用 IPV6 地址 .
Linux scp命令的使用方法应该可以满足大家对Linux文件和目录的复制使用了

#scp命令的一个实例
Linux下scp的用法
scp就是secure copy，一个在linux下用来进行远程拷贝文件的命令。
有时我们需要获得远程服务器上的某个文件，该服务器既没有配置ftp服务器，也没有做共享，无法通过常规途径获得文件时，只需要通过简单的scp命令便可达到目的。
一、将本机文件复制到远程服务器上
```
#scp /home/administrator/news.txt root@192.168.6.129:/etc/squid
```
/home/administrator/ 本地文件的绝对路径
news.txt 要复制到服务器上的本地文件
root 通过root用户登录到远程服务器（也可以使用其他拥有同等权限的用户）
192.168.6.129 远程服务器的ip地址（也可以使用域名或机器名）
/etc/squid 将本地文件复制到位于远程服务器上的路径

如图通过root用户登录远程服务器，输入yes表示同意建立ssh连接

按提示输入root用户的密码

如图所示建立连接后开始传输文件，显示百分比、实际时间和传送速度等信息
二、将远程服务器上的文件复制到本机
```
#scp remote@www.abc.com:/usr/local/sin.sh /home/administrator
```
remote 通过remote用户登录到远程服务器（也可以使用其他拥有同等权限的用户）
www.abc.com 远程服务器的域名（当然也可以使用该服务器ip地址）
/usr/local/sin.sh 欲复制到本机的位于远程服务器上的文件
/home/administrator 将远程文件复制到本地的绝对路径
注意两点：
1.如果远程服务器防火墙有特殊限制，scp便要走特殊端口，具体用什么端口视情况而定，命令格式如下：
```
#scp -p 4588 remote@www.abc.com:/usr/local/sin.sh /home/administrator
```
2.使用scp要注意所使用的用户是否具有可读取远程服务器相应文件的权限。



附：scp命令也符合 scp 源地址  目的地址 的写法
