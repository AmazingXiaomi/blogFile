---
title: linux用户、组学习
date: 2018-01-14 22:43:32
description: 'linux常用建立用户分配权限'
tags: linux
---




#linux常用建立用户分配权限
```bash
 新建用户
```
   <code>useradd xiaomi</code>

 ```bash
  修改密码
 ```
  <code> passwd xiaomi</code>    


 或者  
  <code>useradd  -n  xiaomi   -p  xiaomi</code>

 ```bash
  将文件私有化
 ```
 
 首先创建文件
 
  <code>mkdir /usr/local/xiaomi</code>

将所有权赋给 xiaomi用户

 <code>chown  xiaomi  /usr/local/xiaomi /code> 

修改文件权限

 <code>chmod 700  /usr/local/xiaomi</code>
 *700代表的是读写权限，也可以用
 chmod   a-rwx  /usr/local/xiaomi和  u+rwx  /usr/local/xiaomi实现独享*
 
  ``` bash
   添加组
  ```
 <code>groupadd xiaomi</code>
 
 添加组成员
 
   <code>useradd  -g  xiaomi  -n   xiaomi</code>
  
  
  小例子
  
  >
  
          规划一个用户与组群：有程序开发员5人，项目管理员2人，分别取名为：prg01~prg05，mgr01,mgr2，并分别从属于组program与manage，现按下列要求规划：
          (1)、每个开发员拥有自己的帐户，用户名：prg??，密码：prog?? ；
          (2)、每个开发员从属于program组，并共享两个子目录：program与source，而且拥有所有权限；
          (3)、每个管理员拥有自己的帐户，用户名mgr??，密码：mngr?? ；
          (4)、每个管理员从属于manage组，并共享两个子目录：project与document，而且拥有所有权限  ;          
          (5)、开辟一个公共子目录/home/public，让它被所有的用户共享，而且拥有所有权限，但不能被非属主删除？
          ：创建两个组群
          #groupadd  program
              #groupadd  manage
              添加五个开发员
              #useradd  -g  program  -n  prg 01  -p  prog01
              #useradd  -g  program  -n  prg 02  -p  prog02
              #useradd  -g  program  -n  prg 03  -p  prog03
              #useradd  -g  program  -n  prg 04  -p  prog04
              #useradd  -g  program  -n  prg 05  -p  prog05
          添加两个管理员
          #useradd  -g  manage  -n  mgr01  mngr01
          #useradd  -g  manage  -n  mgr02  mngr02
          创建四个子目录
          #mkdir   /home/program
          #mkdir   /home/source
          #mkdir   /home/project
          #mkdir   /home/document            
          #chmod  770     /home/program
          #chgrp  program  /home/program
          #chmod  770     /home/source
          #chgrp  program  /home/source
          #chmod  770     /home/project
          #chgrp  manage   /home/project
          #chmod  770     /home/document
          #chgrp  manage  /home/document          
          开辟一个公共子目录
          #mkdir   /home/public
          #chmod  a+rwxt   /home/public
          #chmod  777   /home/public
          #chmod  a+t    /home/public
          6、修改用户分组：
          #usermod -G 组名 用户名
          #usermod -a -G 组名 用户名  就可以追加额外组了 
