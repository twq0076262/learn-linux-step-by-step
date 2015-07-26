# Linux 学习记录--文件系统简单操作

# 文件系统简单操作  

**磁盘的容量查看 df**  
**目录的容量查看 du**  
**连接文件 ln**  

## 磁盘的容量查看(df)
**语法：**df[-ahikhtm] 目录或文件名
**选项与参数：**
-a:列出所有的文件系统，包括系统特有的 proc 等文件系统
-k:以 KB 为单位显示
-m:以 MB 为单位显示
-h:以 GB,MB,KB 等格式显示
-H:以 M=1000 K 代替 M=1024 K 显示
-T:连同该分区的文件系统名称一起列出
-i:以 inode 的数量来显示

**举例：**

```
[root@localhost ~]# df -hT
文件系统      类型    容量  已用 可用 已用% 挂载点
/dev/sda2     ext3    9.5G  4.2G  4.9G  47% /
/dev/sda3     ext3    4.8G  138M  4.4G   4% /home
/dev/sda1     ext3     99M   12M   83M  13% /boot
tmpfs        tmpfs   1014M     0 1014M   0% /dev/shm
/dev/sda6     ext3    1.9G   42M  1.8G   3% /mnt/sda6
.host:/     vmhgfs     77G   57G   21G  74% /mnt/hgfs
[root@localhost ~]# df -ihT
文件系统      类型     Inode (I)已用 (I)可用 (I)已用% 挂载点
/dev/sda2     ext3      2.5M    168K    2.3M    7% /
/dev/sda3     ext3      1.3M      22    1.3M    1% /home
/dev/sda1     ext3       26K      35     26K    1% /boot
tmpfs        tmpfs      219K       1    219K    1% /dev/shm
/dev/sda6     ext3      247K      11    247K    1% /mnt/sda6
.host:/     vmhgfs         0       0       0    -  /mnt/hgfs
```

## 目录的容量查看(du)   

**语法：**du[-ahskm] 目录或文件名   
**选项与参数：**   
-a:列出所有文件与目录容量  
-h:以 G/M 容量格式显示  
-s:列出总量，不在列出目录下面文件量  
-S:不包括子目录下的统计()  
-k:以 KB 为单位显示   
-m:以 MB 为单位显示    
 
**举例：**   
[root@localhost ~]# du 
8       /bin 
6       /boot 
1       /dev
… 
216     /tmp 
4077    /usr 
99      /var 
[root@localhost ~]# 

## 连接文件 ln
**语法：**ln [-sf]源文件 目标文件   
**选项与参数：**   
-s:如果不加任何参数默认是 hardlink ,加上-s 是 symboliclink   
-f:如果目标文件存在，就主动将目标文件删除后创建    

### Hard link(硬连接)   
Hard link 只是在某个目录下新建一个文件名连接到某个 inode 上     

**说明：**
1.      创建文件 F1，文件系统为其分配一个 INODE(F1I)和若干 IBLOCK, 此时连接到 INODE(F1I)只有 F1因此 INODE(F1I)连接数为1   

```
[root@localhost ~]# touch f1
[root@localhost ~]# ll -i f1
846433 -rw-r--r-- 1 root root 0 02-24 09:33 f1
```

2.      创建 F1的 Hard Link FH1, Hard link 并不会分配新的 INODE 和 IBLOCK，只是将文件名连接都 F1的 INode 上  

```
[root@localhost ~]# ln f1 fh1
[root@localhost ~]# ll -i f1 fh1
846433 -rw-r--r-- 2 root root 0 02-24 09:33 f1
846433 -rw-r--r-- 2 root root 0 02-24 09:33 fh1
```

可以看到 inode 有1变成了2，INODE 所指向的文件现在是 f1,fh1,指向的数据还是以前的那份iblock  

**硬连接的好处：**   
1.不会创建新的 INODE 和 iblock   
2.硬连接文件或源文件删除不会影响其他(删除只是接触 inode 与文件的连接关系，猜想只要连接数不为0，就不会删除)   

### Symbolic link   
symbolic link 创建的文件时一个独立的新文件会占用一个新的 INODE 和若干 iblock    

**说明：**   
1.      创建文件 F2，文件系统为其分配一个 INODE(F2I)和若干 IBLOCK, 此时连接到 INODE(F2I)只有 F1因此 INODE(F2I)连接数为1   

```
[root@localhost ~]# touch f2
[root@localhost ~]# ll -i f2
846434 -rw-r--r-- 1 root root 0 02-24 09:49 f2
```

2．  创建 F2的符号文件 F2S, 文件系统会分配一个新的 INODE(F2SI)和若干 IBLOCK 给 F2S   

```
[root@localhost ~]# ln -s f2 f2s
[root@localhost ~]# ll -i f2 f2s
846434 -rw-r--r-- 1 root root 0 02-24 09:49 f2
846435 lrwxrwxrwx 1 root root 2 02-24 09:51 f2s -> f2
```

可以看到 f2,f2S 的 INODE 不是同一个，并且连接数都是1.说明他们是不同的独立文件，但是 f2S 对 f2进行符号链接的呢？原因就是 f2s 的 iblock 其大小为2，记录就是 f2的文件名。因此可以这样理解，\       
1.      f2s 对应的 INODE(F2SI)记录了 iblock 编号    
2.      iblock 里记录了 F2的文件名      
3.      通过 F2的文件名就可以找到 F2对应的 INODE 和 iblock       
所以加入我们删除了 F2 那么 F2S 就无法再开启，印在 F2S 需要去讯在 F2这个文件，此时已经被删除了       

```
[root@localhost ~]# rm -f f2
[root@localhost ~]# cat f2s
cat: f2s: 没有那个文件或目录
```

### 目录的连接数量
当我们创建一个目录是默认会在这个目录下创建两个隐藏文件“.与..” 其中.指的是本层目录..指的是上层目录     

```
[root@localhost ~]# ll -id /tmp
745569 drwxrwxrwt 26 root root 409
[root@localhost ~]# cd /tmp
[root@localhost tmp]# mkdir newdir
[root@localhost tmp]# ll -id /tmp /tmp/newdir
 745569 drwxrwxrwt 27 root root 4096 02-24 10:05 /tmp
1008319 drwxr-xr-x  2 root root 4096 02-24 10:05 /tmp/newdir
```

由上面可以看出  
1.      在未创建 newdir 是，tmp 文件夹对应 INODE 的连接数26  
2.      当创建 newdir 后，系统默认创建“.与..”文件，一个指向自己，一个指向上一层（/tmp）  
3.      “.与..”文件都是以硬链接的方式连接，因此可以看到此时，tmp 文件夹对应 INODE 连接数27，newdir 的连接数为2（一个是 newdir 连接，一个是“.”连接）  

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)