# Linux 学习记录--ACL 权限控制

# ACL 权限控制

## 设置 ACL 权限：setfacl  
## 查看 ACL 权限：getfacl  

ACL 权限控制主要目的是提供传统的 owner,group,other 的 read,wirte,execute 权限之外的具体权限设置，可以针对单一用户或组来设置特定的权限   
比如：某一目录权限为     
drwx------ 2 root root 4096 03-10 13:51./acldir   
用户 user 对此目录无任何权限因此无法进入此目录，ACL 可单独为用户 user 设置这个目录的权限，使其可以操作这个目录   
 
## ACL 启动
要使用 ACL 必须要有文件系统支持才行，目前绝大多数的文件系统都会支持，EXT3文件系统默认启动ACL 的   
 
### 查看文件系统是否支持 ACL

```
[root@localhost tmp]# dumpe2fs -h /dev/sda2
dumpe2fs 1.39 (29-May-2006)
……
sparse_super large_file
Default mount options:    user_xattr acl
```
 
### 加载 ACL 功能
 
如果 UNIX LIKE 支持 ACL 但是文件系统并不是默认加载此功能，可自己进行添加    

```
[root@localhost tmp]# mount -o remount,acl /
[root@localhost tmp]# mount
/dev/sda2 on / type ext3 (rw,acl)
```

同样也可以修改磁盘挂在配置文件设置默认开机加载   

```
[root@localhost tmp]# vi /etc/fstab
LABEL=/                 /                       ext3    defaults,acl        1 1
```

## 查看 ACL 权限
 
**语法**：getfacl filename   

## 设置 ACL 权限

**语法**：setfacl [-bkRd]  [-m|-x acl 参数]  目标文件名   
**选项与参数**：      
-m:设置后续的 acl 参数，不可与-x 一起使用   
-x: 删除后续的 acl 参数，不可与-m 一起使用   
-b:删除所有的 acl 参数  
-k:删除默认的 acl 参数  
-R:递归设置 acl 参数  
-d:设置默认 acl 参数，只对目录有效  
 
### 针对特殊用户  

**设置格式**：u:用户账号列表：权限  
**权限**：rwx 的组合形式  
如用户列表为空，代表设置当前文件所有者权限   
 
**举例**：  

```
[root@localhost tmp]# mkdir -m 700 ./acldir; ll -d ./acldir 
drwx------ 2 root root 4096 03-10 13:51 ./acldir
[root@localhost tmp]# su tkf
[tkf@localhost tmp]$ cd ./acldir/
bash: cd: ./acldir/: 权限不够  =>用户无 X 权限
[tkf@localhost tmp]$ exit
exit
[root@localhost tmp]# setfacl -m u:tkf:x ./acldir/ 
=>针对用户 tkf 设置 acldir 目录的权限为 x
[root@localhost tmp]# ll -d ./acldir/
drwx--x---+ 2 root root 4096 03-10 13:51 ./acldir/ 
=>通过 ACL 添加权限在权限末尾会增加多个一个“+”同时文件原本权限也发生变化。
=>可通过 getfacl 查看原始目录权限
[root@localhost tmp]# getfacl ./acldir/
# file: acldir
# owner: root
# group: root
user::rwx
user:tkf:--x  =>记录 tkf 用户针对此目录有 acl 权限
group::---
mask::--x
other::---
=>这里需要特殊说明，只是 tkf 这个用户具有 X 权限，其他用户还是无权限的
[root@localhost tmp]# su tkf
[tkf@localhost tmp]$ cd ./acldir/
[tkf@localhost acldir]$ 
=>用户 tkf 可以具有 x 权限可以进入目录
```

### 针对特定用户组  

设置格式：g:用户组列表：权限
权限：rwx 的组合形式
如用户组列表为空，代表设置当前文件所属用户组权限
**举例**：

```
[root@localhost tmp]# setfa
setfacl   setfattr  
[root@localhost tmp]# setfacl -m g:users:rx ./acldir/
[root@localhost tmp]# getfacl ./acldir/
# file: acldir
# owner: root
# group: root
user::rwx
user:tkf:--x
group::--- => 其他用户组(非 acl 设置)的权限
group:users:r-x => 记录 users 用户组针对此目录有 acl 权限
mask::r-x
other::---
```

### 针对有效权限设置
有效权限（mask）就是 acl 权限设置的极限值，也就是你所设置的 acl 权限一定是 mask 的一个子集，如果超出 mask 范围会将超出的权限去掉
设置格式：m:权限
权限：rwx 的组合形式

**举例**：

```
[root@localhost tmp]# setfacl -m m:x ./acldir/
[root@localhost tmp]# getfacl ./acldir/
# file: acldir
# owner: root
# group: root
user::rwx
user:tkf:--x
group::r-x                      #effective:--x
group:users:r-x                 #effective:--x
mask::--x
other::---
```

### 针对默认权限设置  

我们前面都是针对一个目录为一个用户（组）设置特定权限，但是如果这个目录下在新创建的文件是不具有这些针对这个用户的特定权限的。为了解决这个问题，就需要设置默认 acl 权限，使这个目录下新创建的文件有和目录相同的 ACL 特定权限  
 
**设置格式**：d:[u|g]:用户(组)列表：权限  
 
**举例**   

```
[root@localhost tmp]# mkdir -m 711 ./defdir
[root@localhost tmp]# setfacl -m u:tkf:rxw ./defdir
[root@localhost tmp]# ll -d ./defdir/
drwxrwx--x+ 2 root root 4096 03-10 15:23 ./defdir/
=>目录权限具有 acl 特定权限（后面+）
[root@localhost tmp]# touch ./defdir/a.file;ll ./defdir/
-rw-r--r--  1 root root 0 03-10 15:25 a.file
=>新创建的文件不具有 acl 特定权限（后面无+）
[root@localhost tmp]# setfacl -m d:u:tkf:rxw ./defdir
=>设置默认权限
[root@localhost tmp]# getfacl ./defdir/
# file: defdir
# owner: root
# group: root
user::rwx
user:tkf:rwx
group::--x
mask::rwx
other::--x
default:user::rwx
default:user:tkf:rwx
default:group::--x
default:mask::rwx
default:other::--x

[root@localhost tmp]# touch ./defdir/b.file;ll ./defdir/
-rw-r--r--  1 root root 0 03-10 15:25 a.file
-rw-rw----+ 1 root root 0 03-10 15:26 b.file
=>新创建文件默认带有 acl 特定权限
[root@localhost tmp]# getfacl ./defdir/b.file 
# file: defdir/b.file
# owner: root
# group: root
user::rw-
user:tkf:rwx                    #effective:rw-
group::--x                      #effective:---
mask::rw-
other::---
=>这快我有个疑问，为什么 mask 值是 rw，我猜测和文件最大权限有关，
=>对于文件来时默认最大权限是666即 UMASK 为0000.那个对于可执行文件来说
=>没有 X,难道还需要使用 chmod 设置？ 疑问！！
```

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)