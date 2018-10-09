---
title: Linux下NFS服务器的搭建 
date: 2018-10-9 22:43:32
description: ' NFS（Network File System）即网络文件系统，是FreeBSD支持的文件系统中的一种，它允许网络中的计算机之间通....'
tags: linux
---


---------



## nfs简介
``` bash
$ 介绍
```
>NFS（Network File System）即网络文件系统，是FreeBSD支持的文件系统中的一种，它允许网络中的计算机之间通过TCP/IP网络共享资源。在NFS的应用中，本地NFS的客户端应用可以透明地读写位于远端NFS服务器上的文件，就像访问本地文件一样。

``` bash
$ 优点
```
>以下是NFS最显而易见的好处：
>1. 节省本地存储空间，将常用的数据存放在一台NFS服务器上且可以通过网络访问，那么本地终端将可以减少自身存储空间的使用。
>2. 用户不需要在网络中的每个机器上都建有Home目录，Home目录可以放在NFS服务器上且可以在网络上被访问使用。
>3. 一些存储设备如软驱、CDROM和Zip（一种高储存密度的磁盘驱动器与磁盘）等都可以在网络上被别的机器使用。这可以减少整个网络上可移动介质设备的数量。


``` bash
  $ 组成
```
 >NFS体系至少有两个主要部分：  
一台NFS服务器和若干台客户机，如右图所示。
客户机通过TCP/IP网络远程访问存放在NFS服务器上的数据。
在NFS服务器正式启用前，需要根据实际环境和需求，配置一些NFS参数。
  
  
-----------------------

## 配置nfs并远程挂载

   首先是服务端配置，服务端提供文件系统供客户端来挂载使用，配置过程如下：
   
1. 首先查看linux系统内是否已经安装相关服务，nfs和rpc：

  <code>rpm -qa | grep nfs-utils
    rpm -qa | grep rpcbind
    </code>
2. 如果这两个包存在那么可以直接使用，一般服务器安装的时候都会存在，如果没有的话执行下面命令安装:

  <code>yum -y install nfs-utils
     yum -y install rpcbind</code>  
3. 安装完成以后，我们可以在/etc/exports中增加配置内容: 

  <code>/data ip(rw,async)</code>
例如:
 <code>/mnt/data 47.89.17.7(rw,async)</code>
 
4. 现在我们可以启动我们的repc和nfs服务了，不同系统的linux可能不一样：

  <code>/etc/init.d/rpcbind start   </code>  
  <code>/etc/init.d/nfs start </code>  
测试是否成功可在客户端服务器上运行下面命令：
<code>showmount -e 172.16.1.31<code>
5. 完成上面的步骤，服务端的nfs就已经启动完成，按照同样方法在客户端上安装NFS，但是不必启动。然后运行挂载命令：

<code>mount  -t  nfs  -o  vers=3  ip:/mnt/data   /mnt/data</code>

挂载完成使用使用 df -h 命令查看
卸载  
  <code>umount /mnt/data </code>  


## 安装中遇到的坑
``` bash
$ 角色权限混乱
```
在完成上述步骤以后我发现，客户端的文件拥有者是nobody，权限非常低，我们是无法写入文件的。后来才发现，两个服务器的文件拥有着需要同一个用户。
他们的用户和组都必须相同才能完成匹配在服务端设置的权限。
修改用户id和组id
<code>usermod -u 999 xiaomi
 groupmod -g 999 xiaomi</code>
好了，今天就到这里。
嘿嘿，继续学习。