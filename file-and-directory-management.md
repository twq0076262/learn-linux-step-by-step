# Linux 学习记录--文件与目录管理

# 文件与目录管理

**cd:切换目录   
pwd:显示当前目录  
mkdir:新建一个新的目录  
rmdir:删除一个空的目录   
ls:查看目录与文件  
cp:复制  
rm:删除  
mv:移动|重命名文件与目录**  

## 切换目录(CD)  

**语法：cd  [相对路径或绝对路径]**  

**举例**：

```

[root@localhost ~]# cd ~tkf  //~ 指定用户的主文件夹 
[root@localhost tkf]# cd  //默认为当前用户的主文件夹
[root@localhost ~]# cd .. //发挥上一层
[root@localhost /]# cd /var/spool/mail   //绝对路径
[root@localhost mail]# cd ../mqueue/   //相对路径
[root@localhost mqueue]# pwd
/var/spool/mqueue
[root@localhost mqueue]# cd -        //前一个工作目录   
/var/spool/mail
```

## 显示当前路径(PWD)  

**语法：pwd  [-P]**  
**选项与参数**：  
-P:显示当前路径，而非使用连接(link)路径  

**举例**：  

```

root@localhost ~]# pwd
/root
[root@localhost ~]# cd /var/mail/
[root@localhost mail]# pwd 
/var/mail
[root@localhost mail]# pwd –P //  /var/mails是链接文件，实际路径是/var/spool/mail
/var/spool/mail
[root@localhost mail]#
```

## 新建目录(mkdir)  
**语法**：mkdir  [-mp] 目录名称  
**选项与参数**：  
-m:直接配置目录的权限  
-p:帮助你直接将所需要的目录，递归创建起来  

**举例**： 

```
 
[root@localhost ~]# cd /tmp
[root@localhost tmp]# mkdir test
[root@localhost tmp]# ls -ald ./test
drwxr-xr-x 2 root root 4096 02-20 10:47 ./test
[root@localhost tmp]# mkdir test1/test2/test3
mkdir: 无法创建目录 “test1/test2/test3”: 没有那个文件或目录
[root@localhost tmp]# mkdir -p test1/test2/test3
[root@localhost tmp]# ls -ald ./test1/
drwxr-xr-x 3 root root 4096 02-20 10:48 ./test1/
[root@localhost tmp]# mkdir -m 711 test5
[root@localhost tmp]# ls -ald ./test5
drwx--x--x 2 root root 4096 02-20 10:48 ./test5
[root@localhost tmp]#
```

## 删除空目录(rmdir)  

**语法**：rmdir [-p] 目录名称  
**选项与参数**：  
-p:连同上层“空的”目录一起删除  

**举例**：

```
  
[root@localhost tmp]# rmdir test
[root@localhost tmp]# rmdir  test1
rmdir: test1: 目录非空
[root@localhost tmp]# rmdir -p  test1/test2/test3/
```

## 查看目录与文件(ls)
**语法**:
[root@www ~]# ls [-aAdfFhilnrRSt] 目录名称  
[root@www ~]# ls [--color={never,auto,always}] 目录名称  
[root@www ~]# ls [--full-time] 目录名称  

**选项与参数**：  
-a：全部的文件，连同隐藏档( 开头为 . 的文件) 一起列出来(常用)  
-A：全部的文件，连同隐藏档，但不包括 . 与 .. 这两个目录  
-d：仅列出目录本身，而不是列出目录内的文件数据(常用)  
-f：直接列出结果，而不进行排序 (ls 默认会以档名排序！)  
-F：根据文件、目录等资讯，给予附加数据结构，例如：  
   *:代表可运行档； /:代表目录； =:代表 socket 文件； |:代表 FIFO 文件；  
-h：将文件容量以人类较易读的方式(例如 GB, KB 等等)列出来；  
-i：列出 inode 号码，inode 的意义下一章将会介绍；  
-l：长数据串列出，包含文件的属性与权限等等数据；(常用)  
-n：列出 UID 与 GID 而非使用者与群组的名称 (UID 与 GID 会在帐号管理提到！)  
-r：将排序结果反向输出，例如：原本档名由小到大，反向则为由大到小；  
-R：连同子目录内容一起列出来，等于该目录下的所有文件都会显示出来；  
-S：以文件容量大小排序，而不是用档名排序；  
-t：依时间排序，而不是用档名。  
--color=never  ：不要依据文件特性给予颜色显示；  
--color=always ：显示颜色  
--color=auto   ：让系统自行依据配置来判断是否给予颜色   
--full-time    ：以完整时间模式 (包含年、月、日、时、分) 输出  
--time={atime,ctime} ：输出 access 时间或改变权限属性时间 (ctime)  
                      而非内容变更时间(modification time)  
## 复制(cp)  
**语法**：  
cp  [-adfilprsu] 源文件目标文件  
**选项与参数**：  
-a:相当于-pdr  
-d:若源文件为链接文件的属性，则复制连接文件属性而非文件本身  
-f: 若目标文件已经存在且无法复制，则删除后在尝试一次  
-i:若目标文件已经存在时，在覆盖是会先询问操作的进行  
-l:进行硬链接  
-p:连同文件的属性一起复制  
-r:递归持续复制  
-s:复制成符号链接文件  
-u:若目标文件比源文件旧才更新  

**举例1：文件复制**

```
  
[root@localhost tmp]# cp /var/log/wtmp wtmpTest
[root@localhost tmp]# cp -i /var/log/wtmp wtmpTest  //参数 i
cp：是否覆盖“wtmpTest”? y
[root@localhost tmp]# ll /var/log/wtmp wtmpTest 
-rw-rw-r-- 1 root utmp 125952 02-20 10:25 /var/log/wtmp
-rw-r--r-- 1 root root 125952 02-20 13:18 wtmpTest    //属性变成了当前用户
[root@localhost tmp]# cp -a /var/log/wtmp wtmpTest_a //-a 连同属性一起复制
[root@localhost tmp]# ll /var/log/wtmp wtmpTest wtmpTest_a
-rw-rw-r-- 1 root utmp 125952 02-20 10:25 /var/log/wtmp
-rw-r--r-- 1 root root 125952 02-20 13:18 wtmpTest
-rw-rw-r-- 1 root utmp 125952 02-20 10:25 wtmpTest_a
[root@localhost tmp]# su tkf
[tkf@localhost tmp]$ cp -a /var/log/wtmp wtmpTest_t 
[tkf@localhost tmp]$ ll  wtmpTest_t
-rw-rw-r-- 1 tkf tkf 125952 02-20 10:25 wtmpTest_t  //当用户权限不足时，即使-a 也无法更改属性  
```

1.      源文件所在需要具有的权限 RX  
2.      目的文件所在目录需要 WX 权限  
3.      在权限不足的情况下即使-a 也无法更改文件属性  

**举例2：目录复制**

```
  
[root@localhost tmp]# ll ./copydir/
-rw-r--r-- 1 root root 0 02-20 13:54 afile
-rw-r--r-- 1 root root 0 02-20 13:54 bfile
[root@localhost tmp]# cp /etc ./copydir/  // etc文件夹还有文件，直接复制失败
cp: 略过目录 “/etc”
[root@localhost tmp]# cp -r /etc ./copydir/  // 递归复制
[root@localhost tmp]# ll ./copydir/
-rw-r--r--   1 root root     0 02-20 13:54 afile
-rw-r--r--   1 root root     0 02-20 13:54 bfile
drwxr-xr-x 114 root root 12288 02-20 13:59 etc
```

**举例3：软硬连接复制**

```
  
[root@localhost tmp]# cp -s passwd passwd_slink
[root@localhost tmp]# cp -l passwd passwd_hlink
[root@localhost tmp]# ll passwd passwd_*
-rw-r--r-- 3 root root 2219 02-17 12:22 passwd
-rw-r--r-- 3 root root 2219 02-17 12:22 passwd_hlink
lrwxrwxrwx 1 root root    6 02-20 14:07 passwd_slink -> passwd  //连接文件
[root@localhost tmp]# cp passwd_slink passed_slink1
[root@localhost tmp]# cp -d passwd_slink passed_slink2

[root@localhost tmp]# ll pass*
-rw-r--r-- 1 root root 2219 02-20 14:20 passed_slink1 //源文件
lrwxrwxrwx 1 root root    6 02-20 14:21 passed_slink2 -> passwd // 连接文件
-rw-r--r-- 3 root root 2219 02-17 12:22 passwd
```

## 删除(rm)  

**语法**:rm [-fir] 文件或目录  
**选项与参数**：  
-f:强制模式，不会进行询问  
-i:互动模式  
-r:递归删除  

**举例**： 

```
 
[root@localhost tmp]# ll copydir/
-rw-r--r--   1 root root     0 02-20 13:54 afile
-rw-r--r--   1 root root     0 02-20 13:54 bfile
drwxr-xr-x 114 root root 12288 02-20 13:59 etc
[root@localhost tmp]# rm -r ./copydir/
rm：是否进入目录 “./copydir/”? y  
rm：是否删除 一般空文件 “./copydir//afile”? y
…
rm：是否删除 一般文件 “./copydir//etc/tux.mime.types”? y
可以rm -rf ./copydir/ 来避免提示  
```

## 移动|重命名文件与目录(mv)  
**语法**:rm [-fiu] 源文件，目标目录  
**选项与参数**：  
-f:强制模式，不会进行询问  
-i:互动模式  
-u:若目标文件已经存在，且源文件比较新才会更新  

**举例**：

```
  
[root@localhost tmp]# mkdir movedir
[root@localhost tmp]# cp ~/.bashrc ./bashrc
[root@localhost tmp]# mv -i bashrc ./movedir/  //文件的移动
[root@localhost tmp]# ll ./movedir/
-rw-r--r-- 1 root root 176 02-20 14:55 bashrc
[root@localhost tmp]# mv ./movedir/bashrc ./movedir/b1 //文件的重命名
[root@localhost tmp]# ll ./movedir/
-rw-r--r-- 1 root root 176 02-20 14:55 b1
```

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)