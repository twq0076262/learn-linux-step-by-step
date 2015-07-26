# Linux 学习记录--文件权限

文件权限
Linux 针对文件权限分为三组，即用户，用户组，其他
可通过 ll(ls -l) 查看文件权限,此命令后续介绍

[root@localhost ~]# ll /etc/termcap   
`-rw-r--r--1` `rootroot` 807103 2007-01-07/etc/termcap  
 红色部分代表文件权限  
 黄色部分代表该文件所属用户  
 绿色部分代表该文件所属用户组  

对于文件权限可分为3种（严格说并不是3种）

|**权限种类**|**值**|**描述**|
|:------|:--|:----|
|r|4|可读|
|w|2|可写|
|x|1|可执行|

## 字符意义
文件权限相关共10个字符，其意义分别为  
第1个字符：文件类型  
[d]表示文件目录  
[-]表示文件  
[|]表示连接文件  
[b]表示设备文件里的可供存储的接口设备  
[c]表示设备文件里面的串行端口设备，如键盘  
第2~4个字符：用户权限  
第5~7个字符：用户组权限  
第8~10个字符：其他用户权限  

**举例**
`-rw-r----x`1  A  G1 807103 2007-01-07 /file  
由上信息可看出：  
该文件属于用户 A,属于用户组 G1  

该文件对于用户权限为 rw- 即为可读写权限  
该文件对于用户组权限为 r-- 即为可读权限  
该文件对于其他权限为--x 即为可执行权限  

假设 A,B,C 三个用户，A B 属于用户组 G1 ，C 属于用户组 G2  
那么 A 就有拥有 rw-权限  
由于 B 属于用户组 G1，因此 B 拥有 r--权限  
由于 C 属于用户组 G2，因此 C 拥有--x 权限  

## 权限与属性的更改  
**chgrp:更改文件所属用户组**  
**chown:更改文件所有者**  
**chmod：更改文件权限**  

**chgrp**  
这个命令就是 change group 的简称，不过要被改变的组名要在/etc/group/文件内存在才行，否则会报错  

**语法**：chgrp [-R]  用户组 dirname/filename  
选项与参数：  
-R：递归参数(recursive) 的持续更改，连同子目录下的所有文件，目录一起更改  

**举例**： 

```
 
[root@localhost ~]# ll install.log  //查看 install.log属性
-rw-r--r-- 1 root root 35014 02-14 10:29 install.log
[root@localhost ~]# chgrp users install.log //更改用户组为 users
[root@localhost ~]# ll install.log //查看 install.log 属性
-rw-r--r-- 1 root users 35014 02-14 10:29 install.log
[root@localhost ~]# chgrp nogronp install.log //输入一个无效的组
chgrp: 无效的组 “nogronp”
```

**chown**   
这个命令就是 change owenr 的简称，不过要被改变的用户要在/etc/passwd/文件内存在才行，否则会报错  

**语法**：chown[-R] 用户 文件/目录  
chown[-R] 用户：组名文件/目录  
选项与参数：  
-R：递归参数(recursive) 的持续更改，连同子目录下的所有文件一起更改  

**举例**：

```
  
[root@localhost ~]# ll install.log
-rw-r--r-- 1 root root 35014 02-14 10:29 install.log
[root@localhost ~]# chown bin install.log
[root@localhost ~]# ll install.log
-rw-r--r-- 1 bin root 35014 02-14 10:29 install.log
 have new mail in /var/spool/mail/root
[root@localhost ~]# chown tkf:users install.log
[root@localhost ~]# ll install.log
-rw-r--r-- 1 tkf users 35014 02-14 10:29 install.log
```

**chmod**  
权限设置分为2种，分别可以使用数字和符号  

**语法**：chmod  [-R]  权限 文件/目录 
 
chmod  [-R]  符号表达式文件/目录   
|:-----|:---------------|:-------|:--------|
|chmod|u(user)g(group)o(other)a(all)|+(加入)- (除去)=(设置)|文件或目录|

选项与参数：
-R：递归参数(recursive) 的持续更改，连同子目录下的所有文件一起更改

**举例**

```

[root@localhost ~]# ll baa
-rw-r--r-- 1 root root 176 2007-01-06 baa
[root@localhost ~]# chmod 754 baa
[root@localhost ~]# ll baa
-rwxr-xr-- 1 root root 176 2007-01-06 baa
[root@localhost ~]# chmod u=rw,g=x,o=x baa 
[root@localhost ~]# ll baa
-rw---x--x 1 root root 176 2007-01-06 baa
[root@localhost ~]# chmod a+w baa 
[root@localhost ~]# ll baa
-rw--wx-wx 1 root root 176 2007-01-06 baa
[root@localhost ~]# 
```

## 目录与文件权限的意义

R(Read):可读取此文件的实际内容，如读取文本文件的文件内容
当你具一个目录读取 r 权限。表示你可以查看该目录下的文件名结构。
W(write): 可以编辑，新增或者是修改该文件的内容（但不含删除该文件）
当你具一个目录写入 w 权限。表示你可以更改该目录结构
1.      新建新的文件与目录  
2.      删除已经存在的文件和目录（不论该文件的权限是什么）  
3.      将以存在的文件或目录进行重命名   
4.      转移该目录内的文件，目录位置   
X(execute)：该文件具有可以被系统执行的权限（对于目录来说 X 就是进入文件夹的权限）   

**举例**

```

[root@localhost ~]# mkdir -m 000 /tmp/testdir //root 用户权限为000的 文件夹 testdir
[root@localhost ~]# cd /tmp/testdir/   //超级用户无权限也可进入
[root@localhost testdir]# touch testfile  //创建文件
[root@localhost testdir]# chown tkf . // 将文件夹 testdir 用户变更为 tkf,以便一会切换用户操作
[root@localhost testdir]# ls -ald /tmp/testdir/ ./testfile 
-rw-r--r-- 1 root root    0 02-19 12:59 ./testfile
d--------- 2 tkf  root 4096 02-19 12:59 /tmp/testdir/
[root@localhost testdir]# su tkf //切换用户
[tkf@localhost testdir]$ cd ..
[tkf@localhost tmp]$ ll  ./testdir/        //无 r 权限
ls: ./testdir/: 权限不够
[tkf@localhost tmp]$ chmod u+r ./testdir  //分配 r 权限
[tkf@localhost tmp]$ ll  ./testdir/
?--------- ? ? ? ?          ? testfile    //可以看到目录结构 但是因为无 X 权限，目录下文件属性看不到
[tkf@localhost tmp]$ cd testdir/           //无 x 权限
bash: cd: testdir/: 权限不够
[tkf@localhost tmp]$ chmod u=rx ./testdir  //分配 rx 权限
[tkf@localhost tmp]$ ll  ./testdir/    
-rw-r--r-- 1 root root 0 02-19 12:59 testfile  //可以看到目录结构和文件属性
[tkf@localhost tmp]$ cd testdir/
[tkf@localhost testdir]$ rm -f testfile  //无 W 权限，不能删除目录下的文件
rm: 无法删除 “testfile”: 权限不够
[tkf@localhost tmp]$ chmod u=w ./testdir  //仅分配 W 权限
[tkf@localhost tmp]$ ls -ald ./testdir/
d-w------- 2 tkf root 4096 02-19 12:59 ./testdir/
[tkf@localhost tmp]$ rm ./testdir/testfile //缺少 X 权限 因此删不了
rm: 无法删除 “./testdir/testfile”: 权限不够 
[tkf@localhost tmp]$ chmod u=rw ./testdir  //分配 RW 权限
[tkf@localhost tmp]$ rm ./testdir/testfile  //缺少 X 权限 因此删不了
rm: 无法删除 “./testdir/testfile”: 权限不够
[tkf@localhost tmp]$ chmod u=wx ./testdir //分配 XW 权限
[tkf@localhost tmp]$ rm ./testdir/testfile //可以删除文件，同时文件的用户属于 root ,在 tkf 用户仍可以删除
rm：是否删除有写保护的 一般空文件 “./testdir/testfile”? y
```

1. 如果目录只有 R 权限。可以查看目录下文件结构，但是看不到文件属性，并且进入不了目录（cd）
2. 如果目录有 RX 权限,可以查看目录下文件结构和属性，并且可以进入目录
3. 要删除目录下的文件。目录至少需要 WX 权限

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)
