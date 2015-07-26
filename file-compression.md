# Linux 学习记录--文件压缩

# 文件压缩

## 机器语言与程序语言
对于机器来说只能识别0,1，我们如果让机器运行必须输入机器能够识别的语言，可是机器语言不利于人们使用可理解，因此科学家就开发出人类能看的懂的程序语言，然后再创造出“编译器”将程序语言转换为机器语言。  

## 压缩的简单原理   
我们都知道1 byte=8 bit. 比如，对于这1这个数字来说可以表示为0000 0001，前7个 bit 都是“空的”只有最后一个 bit，有实际意义。压缩的原理就是通过复杂的计算方式将这个“空的“内容尽可能的去掉以减少文件的存储空间    

## 常见压缩|打包命令  
Linux 常见的压缩命令式 gzip,bzip2,这些压缩命令都是针对于一个文件进行压缩，因此当要压缩很多文件时，就需要先进行打包（tar）然后再进行压缩。   

*.Z :compress 程序压缩的文件    
*.gz:gzip 程序压缩的文件   
*.bz2:bzip2程序压缩的文件   
*.tar:打包文件，并未进行压缩  
*.tar.gz:打包文件并以 gzip 程序压缩打包文件  
*tar.bz2: 打包文件并以 bzip2程序压缩打包文件  

### gzip
gzip 可以解开 compress,zip,gzip 等软件压缩的文件   

**语法：**gzip[cdtv#] 文件名   
**选项与参数：**  
-c: 将压缩数据输出到屏幕上  
-d:解压缩  
-t:可以检验一个压缩文件的一致性，看文件有无错误  
-v:显示源文件/压缩文件的压缩比等信息  
-#:压缩等级，-1最快，-9最慢，默认值时-6  

**举例1：压缩文件**  

```
[root@bogon ~]# cp /etc/man.config /tmp/man.config
[root@bogon ~]# gzip -v /tmp/man.config 
/tmp/man.config:         56.1% -- replaced with /tmp/man.config.gz
[root@bogon ~]# ll /etc/man.config /tmp/man.config.gz 
-rw-r--r-- 1 root root 4617 2012-05-30 /etc/man.config
-rw-r--r-- 1 root root 2057 02-27 22:26 /tmp/man.config.gz
```

**举例2：解压缩**  

```
[root@bogon ~]# gzip -d /tmp/man.config.gz 
[root@bogon ~]# ll  /tmp/man.config 
-rw-r--r-- 1 root root 4617 02-27 22:26 /tmp/man.config
```

**举例3：数据流重定向(压缩后保留原来文件)**  

```
[root@bogon ~]# gzip -c /tmp/man.config > /tmp/man.config.gz 
[root@bogon ~]# ll /tmp/man.config /tmp/man.config.gz
-rw-r--r-- 1 root root 4617 02-27 22:26 /tmp/man.config
-rw-r--r-- 1 root root 2057 02-27 22:31 /tmp/man.config.gz
```

**可以 zcat 来读取由 gzip 压缩的文件**  
[root@bogon ~]# zcat /tmp/man.config.gz  

### bzip2  
bzip2的压缩比比 gzip 还要好  

**语法：**bzip2[-cdkzv#] 文件名  
**选项与参数：**  
-c:将压缩数据输出到屏幕上  
-d:解压缩  
-k:保留原始文件  
-z:压缩  
-v:显示源文件/压缩文件的压缩比等信息  
-#:压缩等级，-1最快，-9最慢  

**可以 bzcat 来读取由 bzip2压缩的文件**    

### tar   
**语法：**  
打包与压缩：tar [-j|-z] [-cv] [-f 新建的文件名] filename  
查看文件名：tar [-j|-z] [-tv] [-f 新建的文件名]  
解压缩：tar [-j|-z] [-xv] [-f 新建的文件名] [-C 目录]   
**选项与参数：**  
-c:新建打包文件    
-t:查看打包文件内容   
-x:加压缩打包文件   

-j:使用 bzip2进行压缩/解压缩  
-z:使用该 gzip 进行压缩/解压缩  

-v:在压缩过程中，将正在处理的文件名显示出来   
-f filename:需要被压缩成(解压缩)的文件名   
-C:解压缩到的目录   

-p:保留备份数据的原有权限和属性   
-P:保留绝对路径     
--exclude=File:在压缩中不将 FILE 打包   
--newer-mtime=”时间”：打包比指定时间新的文件   

**举例1:对文件打包压缩**   

```
[root@localhost ~]# tar -jcv -f /root/etc.tar.bz2 /etc
……压缩文件信息
[root@localhost ~]# tar -zcv -f /root/etc.tar.gz /etc
……压缩文件信息
[root@localhost ~]# ll --block-size=M /root/etc.tar.bz2 /root/etc.tar.gz ;du -sm /etc
-rw-r--r-- 1 root root 10M 02-28 10:42 /root/etc.tar.bz2
-rw-r--r-- 1 root root 16M 02-28 10:43 /root/etc.tar.gz
179     /etc
```

可以看到压缩后，文件小了很多   

**举例2：查看打包压缩文件内容**   

```
[root@localhost ~]# tar -ztv -f /root/etc.tar.gz |grep 'shadow*'
-r-------- root/root      1352 2014-02-14 10:36:09 etc/shadow
-r-------- root/root       657 2014-02-14 10:36:09 etc/gshadow
-r-------- root/root       648 2014-02-14 10:36:09 etc/gshadow-
-r-------- root/root      1352 2014-02-14 10:36:09 etc/shadow-
```

**举例3：解压缩**    

```
[root@localhost ~]# tar -jxv -f /root/etc.tar.bz2 -C /tmp
……解压缩文件信息
[root@localhost ~]# ll -d /tmp/etc/
drwxr-xr-x 114 root root 12288 02-28 10:15 /tmp/etc/
```

当不使用绝对路径压缩时，解压后则解压到指定路径下，如压缩文件/etc,解压后直接放在了/tmp/etc
使用绝对路径压缩，则在解压缩后可以使用文件的绝对路径解压缩到文件的原来目录   

**举例4：打包目录，但排除一些文件**    

```
[root@localhost ~]# tar -jcv -f /root/system.tar.bz2 --exclude=/root/etc* --exclude=/root/system.tae.bz2 /root /etc
……压缩文件信息
[root@localhost ~]# ll /root/system.tar.bz2 
-rw-r--r-- 1 root root 10531659 02-28 11:19 /root/system.tar.bz2
```

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)