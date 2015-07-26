# Linux 学习记录--命令与文件的查询

# 命令与文件的查询

## 脚本与文件名查询：which
## 文件名查找：whereis ,locate find
## 数据库更新：updatedb

## 脚本文件名的查询(which)

**语法**：which [-a] command  
**选项和参数**：  
-a:将由 PATH 目录中能找到的指令都列出   
**说明**：which 执行更具当前用户环境变量指定的位置去寻找 command,并返回第一个找到的结果(-a 则返回所有)  

```

[root@localhost tmp]# echo $PATH
/usr/kerberos/sbin:/usr/kerberos/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/usr/X11R6/bin:/root/bin
[root@localhost tmp]# which ifconfig
/sbin/ifconfig
[root@localhost tmp]#
```

## 文件名查找

### whereis  
**语法**：whereis [-bmsu]文件或目录    
选项和参数：    
-b: 只找二进制格式的文件     
-m:只找在说明文件 manual 路径下的文件     
-s:只找 source 源文件     
-u:查找不在上述三个选项的其他文件    
  
```

[root@localhost tmp]# whereis ifconfig
ifconfig: /sbin/ifconfig /usr/share/man/man8/ifconfig.8.gz
```
 
**说明**：whereis 并不是从 PATH 指定路径查找，而是利用数据库查询     
 
## locate  
**语法**: locate [-ir] keyword  
**选项和参数**：  
-i:忽略大小写  
-r:后可接正则表达式  
 
**举例**：  

```
    
[root@localhost tmp]# locate passwd
/etc/passwd
```
 
**说明**：linux 会将所有文件都记录在数据库中，locate 和 whereis 从这个数据库进行查询，并不是扫描硬盘，因此可以提高效率，但是也带来一个问题就是不能保准数据库的信息和硬盘式同步的。
为了避免上述问题，可以手都去更新数据库，updatedb
 
## Find  
**语法**：find [PATH] [OPTION] [ACTION]  
PATH:要查找的路径  
OPTION：  
## 与时间相关的参数  
-atime,-ctime,-mtime，以-mtime 为例  
-mtime n :表示在 n 天之前的“一天之内”被更改过的文件  
-mtime +n ：列出在 n 天之前，不包含 n 天，被更改的文件  
-mtime –n : 列出在 n 天之内，含 n 天本身被更改的文件  
-newer  file: file 为一个存在的文件。列出比 file 还新的文件  

![](images/2.gif)

**举例**：   

```

[root@bogon ~]# find / -mtime 0
[root@bogon ~]# find  /etc –newer  /etc/passwd
```
 
## 与用户和用户组相关的参数  
-uid n: 查询 UID(用户 ID)为 n 的文件  
-gid n: 查询 GID(用户组 ID)为 n 的文件  
-user name：查询所属用户名为 name 的文件  
-group name: 查询所属用户组为 name 的文件  
-nouser:查询不属于任何用户的文件  
-nogroup查询不属于任何用户组的文件  
 
**举例**：

```
  
[root@bogon ~]# find /home -user tkf  
```
 
## 与文件权限及名称有关的参数  
-name filename: 查找文件名为 filename 支持模糊查询  
-size [+-]SIZE: 查询比 SIZE 大或小的的文件。大小单位 c 代表 byte ,k 代表 kb  
-type TYPE: 查找文件类型为 TYPE 的文件  
-perm mode ：搜寻文件权限刚好等于 ode 的文件，这个 mode为类似 chmod 的属性值，举例来说， -rwsr-xr-x的属性为 4755  
-perm -mode ：搜寻文件权限必须要全部囊括 mode 的权限的文件，举例来说，我们要搜寻 -rwxr--r--，亦即 0744的文件，使用 -perm -0744，当一个文件的权限为 -rwsr-xr-x，亦即 4755时，也会被列出来，因为 -rwsr-xr-x的属性已经囊括了 -rwxr--r--的属性了。  
-perm +mode ：搜寻文件权限包含任一 mode 的权限的文件，举例来说，我们-rwxr-xr-x，亦即 -perm +755时，但一个文件属性为 -rw-------也会被列出来，因为他有 -rw....的属性存在！
 
**举例**：[root@bogon ~]# find / -name *http*  
 
## 其他可进行的操作：  
-exec  command: command 为其他命令 –exec 后可接其他命令来处理查询到的结果  
-print:将结果打印到屏幕上，默认操作  
 
**举例**：

```

root@bogon ~]# find / -name *http*  -exec  ls -l {} \;  
-rw-r--r-- 1 root root 97 2008-05-24 /etc/pam.d/system-config-httpd
-rw-r--r-- 1 root root 82 2008-05-24 /etc/security/console.apps/system-config-httpd
-rw------- 1 root root 464 2008-05-24 /etc/alchemist/switchboard/system-config-httpd.switchboard.adl
```

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)