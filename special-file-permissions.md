# Linux 学习记录--文件特殊权限  

# 文件特殊权限  

文件除了读写(r),写(w),执行(x) 权限，还有些特殊权限(s,t) 
 
## SUID  

**功能**：   
SUID 权限仅对二进制程序有效  
执行者对于程序需要有 X 可执行的权限  
执行者将均有改程序所有者的权限  
本权限只在执行程序过程中有效  
 
**举例**：  
普通用户也可以通过命令 passwd 修改自己的密码。修改的密码内容将会记录/etc/shadow 文件中，但是普通用户对这个文件无任何权限，那如何修改这个文件呢？  
 
**以上步骤可以理解为这样**  
普通用户执行 passwd 命令修改密码 èèpasswd 命令程序修改/etc/shadow 文件将密码记录其中  
 
[root@localhost /]# ll /etc/shadow/usr/bin/passwd    
-r-------- 1 root root  1352 02-14 10:36 /etc/shadow   
-rwsr-xr-x 1 root root 23420 2010-08-11/usr/bin/passwd   
 
这就是 SUID 的功能，当普通用户在执行 passwd 程序命令时，由于 passwd 具有 SUID 权限，同时普通用户对于 passwd 命令具有 X 权限，那么在 passwd 执行过程中普通用户将程序所有者(root)的权限，因此/etc/shadow 就可以被修改  
 
## SGID 
 
SGID 对于二进制程序来说，功能和 SUID 差不多  
SGID 权限对二进制程序有效  
执行者对于程序需要有 X 可执行的权限  
执行者将均有改程序用户组的权限  
本权限只在执行程序过程中有效  
 
SGID 也可以针对目录设置，功能如下     
用户在具有 SGID 权限的目录下创建的文件或目录其所属的用户组就是目录所有的有户组    
 
**说明，默认情况下用户创建的文件所属的用户组为用户的有效用户组**    
 
**举例**    

```
[root@localhost /]# mkdir -m 777 /tmp/newdir;ll -d /tmp/newdir
drwxrwxrwx 2 root root 4096 03-10 12:35 /tmp/newdir
[root@localhost /]# cd /tmp/newdir/
[root@localhost newdir]# touch rootfile
[root@localhost newdir]# ll rootfile 
-rw-r--r-- 1 root root 0 03-10 12:35 rootfile
=>所属用户和所属用户组都是 root
[root@localhost newdir]# su tkf
=>切换到普通用户
[tkf@localhost newdir]$ touch tkffile
[tkf@localhost newdir]$ ll 
-rw-r--r-- 1 root root 0 03-10 12:35 rootfile
-rw-rw-r-- 1 tkf  tkf  0 03-10 12:36 tkffile
=>所属用户和所属用户组都是 tkf

[root@localhost ~]# chmod g+s /tmp/newdir/
=>给 newdir 添加 SGID 权限
[root@localhost ~]# ll -d /tmp/newdir/
drwxrwsrwx 2 root root 4096 03-10 12:36 /tmp/newdir/

[root@localhost ~]# su tkf
[tkf@localhost root]$ touch /tmp/newdir/sgidfile;ll /tmp/newdir/
-rw-r--r-- 1 root root 0 03-10 12:35 rootfile
-rw-rw-r-- 1 tkf  root 0 03-10 12:40 sgidfile
-rw-rw-r-- 1 tkf  tkf  0 03-10 12:36 tkffile
=> sgidfile 文件所属用户组发生变化，和目录(newdir)的用户组一样
```
 
## SBIT
 
**SBIT 只针对目录有效，其主要功能是**
当用户拥有目录的 WX 权限时，用户可以删除（删除，重命名，移动）目录下的任意文件
当目录拥有 SBIT 权限时，即使用户拥有目录的 WX 权限，用户只能删除自己创建的文件(可以修改不是自己创建的文件)。root 用户都可以删除
**举例 **
 
```
[root@localhost ~]# mkdir -m 1777 /tmp/bitdir
[root@localhost ~]# su tkf
[tkf@localhost root]$ ll -d /tmp/bitdir/
drwxrwxrwt 2 root root 4096 03-10 12:59 /tmp/bitdir/
=>tkf 用户拥有目录的 rwx 权限
[tkf@localhost root]$ touch /tmp/bitdir/tkffile ;ll /tmp/bitdir/
-rw-rw-r-- 1 tkf tkf 0 03-10 12:59 tkffile
=>在目录下创建文件

[tkf@localhost root]$ su userA
=>切换另一个用户
[userA@localhost root]$ ll -d /tmp/bitdir/
drwxrwxrwt 2 root root 4096 03-10 12:59 /tmp/bitdir/
=> userA 用户拥有目录的 rwx 权限

[userA@localhost root]$ cd /tmp/bitdir/
[userA@localhost bitdir]$ touch userfile;ll
-rw-rw-r-- 1 tkf   tkf   0 03-10 12:59 tkffile
-rw-rw-r-- 1 userA userA 0 03-10 13:04 userfile
=>在目录下创建文件 userfile

[userA@localhost bitdir]$ rm tkffile 
rm：是否删除有写保护的 一般空文件 “tkffile”? y
rm: 无法删除 “tkffile”: 不允许的操作
=>由于目录具有 SBIT 权限 虽然 userA 对目录具有 WX 权限，但是不能删除非他创建的文件

[root@localhost ~]# chmod o-t /tmp/bitdir/
=>将目录 SBIT 权限去掉
[root@localhost ~]# su userA
[userA@localhost root]$ cd /tmp/bitdir/
[userA@localhost bitdir]$ ll
-rw-rw-r-- 1 tkf   tkf   0 03-10 12:59 tkffile
-rw-rw-r-- 1 userA userA 0 03-10 13:04 userfile
[userA@localhost bitdir]$ rm tkffile 
rm：是否删除有写保护的 一般空文件 “tkffile”? y
[userA@localhost bitdir]$ ll
-rw-rw-r-- 1 userA userA 0 03-10 13:04 userfile
=>可以删除不是自己创建的文件
```
 
## 权限设置方法  
 
和设置和基本权限(rwx)方法基本，可以通过数字设置也可以通过符号设置  

### 数字设置 
SUID:4  
SGID:2  
SBIT:1  
###符号设置(+-=) 
SUID：u+s  
SGID：g+s  
SBIT：o+t  
**举例**：  

```
[root@localhost ~]# touch test
[root@localhost ~]# chmod 4755 test; ll test 
-rwsr-xr-x 1 root root 0 03-10 13:14 test
[root@localhost ~]# chmod 6755 test; ll test 
-rwsr-sr-x 1 root root 0 03-10 13:14 test
[root@localhost ~]# chmod 1755 test; ll test 
-rwxr-xr-t 1 root root 0 03-10 13:14 test
[root@localhost ~]# chmod 7666 test; ll test 
-rwSrwSrwT 1 root root 0 03-10 13:14 test
=>这里 S,T 都是大写是因为文件本身不具有X权限，而 S,T 权限设置成功的
=>前提是文件具有 X 权限，因此大写的 ST 代表文件无这些特殊权限
```

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)