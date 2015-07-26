# Linux 学习记录--文件备份|还原  

# 文件备份|还原  

## dump 备份  
## restore 还原  
## dd 数据备份   
## mkisofs 镜像文件制作  

## dump 备份    
dump 主要用于备份真个文件系统备份，虽然也可以备份单一目录，但是对目录的支持不足，单一目录还是建议使用打包压缩的方式进行备份    
dump 另一个只要功能就是制定等级，也就是可以进行增量备份。   

![](images/5.gif)  

dump 等级分为0~9 10个等级，0是完全备份，1是在0的基础上进行增量备份，依次类推   
**当待备份的数据为单一文件系统**   
可以利用了level 0~9进行备份，同时可以使用 dump 完整功能   
**当待备份的数据只是目录，并非单一文件系统**   
**限制：**   
所有备份数据必须都在该目录下   
仅能使用 level 0 进行数据备份   
不支持-u 参数，即无法创建/etc/dumpdates 这个 level 备份的时间记录文件   
**语法：**dump [-Suvj] [-level] [-f 备份文件]待备份数据       
             dump -W   
**选项与参数：**   
-S:仅列出后面的待备份数据需要多少磁盘空间才能够备份完毕   
-u:将这次备份记录到/etc/dumpdates 文件中      
-v:将 dump 文件过程显示出来   
-j:加入 bzip2的支持，将数据进行压缩，默认压缩等级2   
-level:备份等级0~9  
-f:备份文件   
-W:列出在/etc/fstab 里面的具有 dump 设置的分区是否有过备份   

**举例1：备份挂载到/boot 文件系统 level -0**   

```
[root@localhost ~]# dump -S /boot
16752640
[root@localhost ~]# dump -u -0 -f /root/boot.dump.0 /boot
  DUMP: Date of this level 0 dump: Fri Feb 28 15:05:56 2014
  DUMP: Dumping /dev/sda1 (/boot) to /root/boot.dump.0
  DUMP: Label: /boot
  DUMP: Writing 10 Kilobyte records
  DUMP: mapping (Pass I) [regular files]
  DUMP: mapping (Pass II) [directories]
  DUMP: estimated 16360 blocks.
  DUMP: Volume 1 started with block 1 at: Fri Feb 28 15:05:56 2014
  DUMP: dumping (Pass III) [directories]
  DUMP: dumping (Pass IV) [regular files]
  DUMP: Closing /root/boot.dump.0
  DUMP: Volume 1 completed at: Fri Feb 28 15:05:58 2014
  DUMP: Volume 1 16440 blocks (16.05MB)
  DUMP: Volume 1 took 0:00:02
  DUMP: Volume 1 transfer rate: 8220 kB/s
  DUMP: 16440 blocks (16.05MB) on 1 volume(s)
  DUMP: finished in 2 seconds, throughput 8220 kBytes/sec
  DUMP: Date of this level 0 dump: Fri Feb 28 15:05:56 2014
  DUMP: Date this dump completed:  Fri Feb 28 15:05:58 2014
  DUMP: Average transfer rate: 8220 kB/s
  DUMP: DUMP IS DONE   
[root@localhost ~]# cat /etc/dumpdates 
/dev/sda1 0 Fri Feb 28 15:05:56 2014 +0800
=>可以看出 etc/dumpdates 记录着这次备份信息
```

**举例2：查看文件系统备份记录**   

```
[root@localhost ~]# dump -W
Last dump(s) done (Dump '>' file systems):
> /dev/sda2     (     /) Last dump: never
> /dev/sda3     ( /home) Last dump: never
  /dev/sda1     ( /boot) Last dump: Level 0, Dat
> /dev/sda6     (/mnt/sda6) Last dump: never
=>可以看出 sda1已经进行了 level0备份，其他还未备份
```

**举例3:增量备份 level 1**   

```
[root@localhost ~]# dd if=/dev/zero of=/boot/bigfile.img bs=1M count=20
20+0 records in
20+0 records out
20971520 bytes (21 MB) copied, 0.320717 seconds, 65.4 MB/s
=>先创建一个20M 左右的文件
[root@localhost ~]# dump -u -1 -f /root/boot.dump.1 /boot
  DUMP: Date of this level 1 dump: Fri Feb 28 15:17:51 2014
  DUMP: Date of last level 0 dump: Fri Feb 28 15:05:56 2014
  DUMP: Dumping /dev/sda1 (/boot) to /root/boot.dump.1
  DUMP: Label: /boot
  DUMP: Writing 10 Kilobyte records
  DUMP: mapping (Pass I) [regular files]
  DUMP: mapping (Pass II) [directories]
  DUMP: estimated 20543 blocks.
  DUMP: Volume 1 started with block 1 at: Fri Feb 28 15:17:52 2014
  DUMP: dumping (Pass III) [directories]
  DUMP: dumping (Pass IV) [regular files]
  DUMP: Closing /root/boot.dump.1
  DUMP: Volume 1 completed at: Fri Feb 28 15:17:53 2014
  DUMP: Volume 1 20580 blocks (20.10MB)
  DUMP: Volume 1 took 0:00:01
  DUMP: Volume 1 transfer rate: 20580 kB/s
  DUMP: 20580 blocks (20.10MB) on 1 volume(s)
  DUMP: finished in 1 seconds, throughput 20580 kBytes/sec
  DUMP: Date of this level 1 dump: Fri Feb 28 15:17:51 2014
  DUMP: Date this dump completed:  Fri Feb 28 15:17:53 2014
  DUMP: Average transfer rate: 20580 kB/s
  DUMP: DUMP IS DONE
[root@localhost ~]# cat /etc/dumpdates 
/dev/sda1 0 Fri Feb 28 15:05:56 2014 +0800
/dev/sda1 1 Fri Feb 28 15:17:51 2014 +0800
=>这次配备写入备份记录中
[root@localhost ~]# dump -W
Last dump(s) done (Dump '>' file systems):
> /dev/sda2     (     /) Last dump: never
> /dev/sda3     ( /home) Last dump: never
  /dev/sda1     ( /boot) Last dump: Level 1, Date Fri Feb 28 15:17:51 2014
> /dev/sda6     (/mnt/sda6) Last dump: never
[root@localhost ~]# ll /root/boot* 
-rw-r--r-- 1 root root 16834560 02-28 15:05 /root/boot.dump.0
-rw-r--r-- 1 root root 21073920 02-28 15:17 /root/ boot.dump.1
=> boot.dump.1大小约为20M，可见是增量备份
```

**举例4：单一目录进行备份**   

```
[root@localhost ~]# dump -0 -f /root/etc.dump /etc
  DUMP: Date of this level 0 dump: Fri Feb 28 15:23:39 2014
  DUMP: Dumping /dev/sda2 (/ (dir etc)) to /root/etc.dump
DUMP: Label: /
  DUMP: Writing 10 Kilobyte records
  DUMP: mapping (Pass I) [regular files]
  DUMP: mapping (Pass II) [directories]
  DUMP: estimated 177675 blocks.
  DUMP: Volume 1 started with block 1 at: Fri Feb 28 15:23:41 2014
  DUMP: dumping (Pass III) [directories]
  DUMP: dumping (Pass IV) [regular files]
  DUMP: Closing /root/etc.dump
  DUMP: Volume 1 completed at: Fri Feb 28 15:24:23 2014
  DUMP: Volume 1 188600 blocks (184.18MB)
  DUMP: Volume 1 took 0:00:42
  DUMP: Volume 1 transfer rate: 4490 kB/s
  DUMP: 188600 blocks (184.18MB) on 1 volume(s)
  DUMP: finished in 42 seconds, throughput 4490 kBytes/sec
  DUMP: Date of this level 0 dump: Fri Feb 28 15:23:39 2014
  DUMP: Date this dump completed:  Fri Feb 28 15:24:23 2014
  DUMP: Average transfer rate: 4490 kB/s
  DUMP: DUMP IS DONE
[root@localhost ~]# ll /root/etc.dump 
-rw-r--r-- 1 root root 193126400 02-28 15:24 /root/etc.dump
```

## restore 还原
dump 备份的文件由 restore 进行还原   
**语法：**   
**查看 dump 文件：**restore –t [-f dumpfile] [-h]  
**比较 dump 与实际文件：**restore –C [-f dumpfile] –D 挂载点    
**进入互动模式(还原单个文件)：**restore –i [-f dumpfile]   
还原整个文件系统：restore –r [-f dumpfile]   
**选项与参数：**   
相关的各种模式，各种模式无法混用.例如不可以写 -tC   
-t:此模式用在察看 dump 起来的备份档中含有什么重要数据！类似 tar -t 功能；     
-C:此模式可以将 dump 内的数据拿出来跟实际的文件系统做比较，最终会列出[在 dump 文件内有记录  的，且目前文件系统不一样]的文件；   
-i:进入互动模式，可以仅还原部分文件，用在 dump 目录时的还原  
-r:将整个 filesystem 还原的一种模式，用在还原针对文件系统的 dump 备份；   
**其他较常用到的选项功能：**   
-h:察看完整备份数据中的 inode 与文件系统 label 等信息   
-f:后面就接你要处理的那个 dump 文件    
-D:与 -C 进行搭配，可以查出后面接的挂载点与 dump 内有不同的文件   

**举例1：查看 dump 备份文件**   

```
[root@localhost ~]# restore -t -f /root/boot.dump.0
Dump   date: Fri Feb 28 15:05:56 2014
Dumped from: the epoch
Level 0 dump of /boot on localhost.localdomain:/dev/sda1
Label: /boot
         2      .
        11      ./lost+found
     10041      ./grub
     10059      ./grub/grub.conf
…….
        14      ./System.map-2.6.18-371.el5
        15      ./config-2.6.18-371.el5
        16      ./symvers-2.6.18-371.el5.gz
        17      ./vmlinuz-2.6.18-371.el5
```

**举例2：比较文件差异**   

```
[root@localhost ~]# mv /boot/message /boot/message-back
[root@localhost ~]# restore -C -f /root/boot.dump.0 -D /boot
Dump   date: Fri Feb 28 15:05:56 2014
Dumped from: the epoch
Level 0 dump of /boot on localhost.localdomain:/dev/sda1
Label: /boot
filesys = /boot
restore: unable to stat ./message: No such file or directory
Some files were modified!  1 compare errors
```

**举例3：还原整个文件系统**    

```
[root@localhost ~]# dd if=/dev/zero of=/home/newfile bs=1M count=200
200+0 records in
200+0 records out
209715200 bytes (210 MB) copied, 3.83857 seconds, 54.6 MB/s
[root@localhost ~]# mkfs -t ext3 /home/newfile 
mke2fs 1.39 (29-May-2006)
/home/newfile is not a block special device.
……
180 days, whichever comes first.  Use tune2fs -c or -i to override.
[root@localhost ~]# mount -o loop /home/newfile /mnt
[root@localhost ~]# df -h
文件系统              容量  已用 可用 已用% 挂载点
/dev/sda2             9.5G  4.4G  4.7G  49% /
/dev/sda3             4.8G  339M  4.2G   8% /home
/dev/sda1              99M   42M   53M  45% /boot
tmpfs                1014M     0 1014M   0% /dev/shm
/home/newfile         194M  5.6M  179M   4% /mnt
=>创建一个文件挂载到 mnt 下
[root@localhost ~]# cd /mnt
[root@localhost mnt]# restore -r -f /root/boot.dump.0
restore: ./lost+found: File exists
[root@localhost mnt]# ll
总计 16149
-rw-r--r-- 1 root root    70400 10-01 21:10 config-2.6.18-371.el5
drwxr-xr-x 2 root root     1024 02-18 09:51 grub
-rw------- 1 root root  2748313 02-18 09:46 initrd-2.6.18-371.el5.img
drwx------ 2 root root    12288 02-14 18:00 lost+found
-rw-r--r-- 1 root root    80032 2009-03-13 message
-rw------- 1 root root    27676 02-28 15:54 restoresymtable
-rw-r--r-- 1 root root   117436 10-01 21:10 symvers-2.6.18-371.el5.gz
-rw-r--r-- 1 root root   996296 10-01 21:10 System.map-2.6.18-371.el5
-rw-r--r-- 1 root root 10485760 02-28 13:25 testing.img
-rw-r--r-- 1 root root  1912148 10-01 21:10 vmlinuz-2.6.18-371.el5
=>还原 level 0备份
[root@localhost mnt]# restore -r -f /root/boot.dump.1
[root@localhost mnt]# ll
总计 36711
-rw-r--r-- 1 root root 20971520 02-28 15:17 bigfile.img
-rw-r--r-- 1 root root    70400 10-01 21:10 config-2.6.18-371.el5
drwxr-xr-x 2 root root     1024 02-18 09:51 grub
-rw------- 1 root root  2748313 02-18 09:46 initrd-2.6.18-371.el5.img
drwx------ 2 root root    12288 02-14 18:00 lost+found
-rw-r--r-- 1 root root    80032 2009-03-13 message
-

------- 1 root root    27724 02-28 15:55 restoresymtable
-rw-r--r-- 1 root root   117436 10-01 21:10 symvers-2.6.18-371.el5.gz
-rw-r--r-- 1 root root   996296 10-01 21:10 System.map-2.6.18-371.el5
-rw-r--r-- 1 root root 10485760 02-28 13:25 testing.img
-rw-r--r-- 1 root root  1912148 10-01 21:10 vmlinuz-2.6.18-371.el5
=>还原 level 1备份可以看到多了 bigfile.img 这个增量文件
```

## dd  
dd 功能不仅限于创建文件，更多功能在于“备份”，cp,dump 只是简单的文件数据拷贝，而 dd 可以读取设备的所有内容，比如 superblock ,boot sector,mete data 等  
**语法：**dd if=”input file” of=”output file” bs=”block” count=”number”  
**选项与参数：**   
if:输入文件，也可以是设备    
of:输出文件，也可以是设备  
bs:每个 block 的大小，默认是512 K    
count:block 数量  

**举例1.文件备份**    

```
[root@localhost ~]# dd if=~/.bashrc of=/tmp/bashrc 
0+1 records in
0+1 records out
176 bytes (176 B) copied, 7.3142e-05 seconds, 2.4 MB/s
[root@localhost ~]# ll /tmp/bashrc 
-rw-r--r-- 1 root root 176 02-28 16:17 /tmp/bashrc
```

**举例2：文件系统备份**     

```
[root@localhost ~]# dd if=/dev/sda1 of=/tmp/boot.dd bs=1M 
101+1 records in
101+1 records out
106896384 bytes (107 MB) copied, 9.60492 seconds, 11.1 MB/s
[root@localhost ~]# ll /tmp/boot.dd
-rw-r--r-- 1 root root 106896384 02-28 16:19 /tmp/boot.dd
```

**举例3：文件系统还原**    

```
[root@localhost ~]# dd if=/tmp/boot.dd of=/dev/sda1 bs=1M
```

**举例4.文件系统完全复制**    
Dump 备份时，我们需要先用 Dump 将文件系统备份，然后创建新的文件系统，格式化，再将备份文件还原到新的文件系统。   
使用 dd 可以不用格式化，就可以完全复制一个文件系统，因为 dd 将 uperblock ,boot sector,mete data 等信息都进行复制，格式化要做的不也正是这些事吗    

```
[root@bogon ~]# fdisk /dev/sda
…….
Command (m for help): n
……
Command (m for help): P
……
   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1          13      104391   83  Linux
……
/dev/sda7            2116        2134      152586   83  Linux

Command (m for help): w
……
[root@bogon ~]# partprobe
=>创建完分区
[root@bogon ~]# dd if=/dev/sda1 of=/dev/sda7 
208782+0 records in
208782+0 records out
106896384 bytes (107 MB) copied, 23.5363 seconds, 4.5 MB/s
[root@bogon ~]# mount /dev/sda7 /mnt
[root@bogon ~]# ll /mnt
总计 5838
-rw-r--r-- 1 root root   70400 10-01 21:10 config-2.6.18-371.el5
drwxr-xr-x 2 root root    1024 02-18 20:26 grub
-rw------- 1 root root 2748762 02-27 19:45 initrd-2.6.18-371.el5.img
drwx------ 2 root root   12288 02-19 03:59 lost+found
-rw-r--r-- 1 root root   80032 2009-03-13 message
-rw-r--r-- 1 root root  117436 10-01 21:10 symvers-2.6.18-371.el5.gz
-rw-r--r-- 1 root root  996296 10-01 21:10 System.map-2.6.18-371.el5
-rw-r--r-- 1 root root 1912148 10-01 21:10 vmlinuz-2.6.18-371.el5
=> /mnt和/boot 下的内容一样 并且没有进行格式化
```

## mkisofs(镜像文件备份)   
**语法：**mkisofs [-o 镜像文件] [-rv] [-m file]待备份的文件 [-V vol] –graft-point    isodir=sysdir  
**选项与参数:**   
-o:镜像文件  
-r:产生 UNIX/Linux 支持的文件数据   
-v:显示构建 ISO 的过程   
-m:排除的文件  
-V:卷标名称  
-graft-point:目录对照名称，如果不进行指定所以的信息都会保持在根目录  

**举例：**   

```
[root@bogon ~]# mkisofs -o /tmp/system.img -r -m /home/lost+found -V 'tkf_file' -graft-point /root=/root /home=/home /etc=/etc
[root@bogon ~]# mount -o loop /tmp/system.img /mnt
[root@bogon ~]# ll /mnt
dr-xr-xr-x 114 root root 34816 03-01 14:31 etc
dr-xr-xr-x   3 root root  2048 03-01 14:31 home
dr-xr-xr-x  18 root root  4096 03-01 14:31 root
```

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)