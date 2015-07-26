# Linux 学习记录--磁盘分区，格式化与检验  

#磁盘分区，格式化与检验  

**磁盘分区：fdisk**  
**磁盘格式化：mkfs,mke2fs**  
**磁盘检测：fsck**  
**大容量磁盘分区：parted**  

## 磁盘分区  

**语法：**fdisk[-l] 设备名称  
-l:输出系统内所有分区  

**举例：**  

```
[root@localhost ~]# fdisk -l

Disk /dev/sda: 21.4 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280bytes

  Device Boot      Start         End      Blocks  Id  System
/dev/sda1  *           1          13     104391   83  Linux
/dev/sda2              14        1288   10241437+  83  Linux
/dev/sda3            1289        1925    5116702+  83  Linux
/dev/sda4            1926        2610    5502262+   5  Extended
/dev/sda5            1926        2052    1020096   82 Linux swap / Solaris
/dev/sda6            2053        2302    2008093+  83  Linux
```

**1.       查看磁盘文件名**    
[root@localhost ~]# df /  
文件系统               1K-块        已用     可用 已用% 挂载点  
/dev/sda2              9920624   4329108  5079448  47% /  

**2.       查看磁盘分区功能**  

```
[root@localhost ~]# fdisk /dev/sda  //这里不带数字
 
The number of cylinders for this disk isset to 2610.
There is nothing wrong with that, but thisis larger than 1024,
and could in certain setups cause problemswith:
1) software that runs at boot time (e.g.,old versions of LILO)
2) booting and partitioning software fromother OSs
  (e.g., DOS FDISK, OS/2 FDISK)
 
Command (m for help): m
Command action
  a   toggle a bootable flag
  b   edit bsd disklabel
  c   toggle the dos compatibilityflag
  d   delete a partition //删除磁盘分区
  l   list known partition types
  m   print this menu  //查看磁盘分区功能
  n   add a new partition //增加一个磁盘分区
  o   create a new empty DOSpartition table
  p   print the partition table //查看磁盘分区
  q   quit without saving changes
  s   create a new empty Sundisklabel
  t   change a partition's system id
  u   change display/entry units
  v   verify the partition table
  w   write table to disk and exit
  x   extra functionality (expertsonly)
```

### 删除磁盘分区  

```
[root@localhost ~]# fdisk /dev/sda
 
The number of cylinders for this disk isset to 2610.
There is nothing wrong with that, but thisis larger than 1024,
and could in certain setups cause problemswith:
1) software that runs at boot time (e.g.,old versions of LILO)
2) booting and partitioning software fromother OSs
  (e.g., DOS FDISK, OS/2 FDISK)
 
Command (m for help): p
 
Disk /dev/sda: 21.4 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280bytes
 
  Device Boot      Start         End     Blocks   Id  System
/dev/sda1  *           1          13      104391  83  Linux
/dev/sda2              14        1288   10241437+  83  Linux
/dev/sda3            1289        1925    5116702+  83  Linux
/dev/sda4            1926        2610    5502262+   5  Extended
/dev/sda5            1926        2052    1020096   82  Linux swap / Solaris
/dev/sda6            2053        2302    2008093+  83  Linux
```

由上可知我的磁盘主要分为6个分区，1,2,3为主分区，4为扩展分区，5为 swap 分区，6是逻辑分区  

```
Command (m for help): d
Partition number (1-6): 3
 
Command (m for help): p
 
Disk /dev/sda: 21.4 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280bytes
 
  Device Boot      Start         End      Blocks  Id  System
/dev/sda1  *           1          13      104391  83  Linux
/dev/sda2              14        1288   10241437+  83  Linux
/dev/sda4            1926        2610    5502262+   5  Extended
/dev/sda5            1926        2052    1020096   82  Linux swap / Solaris
/dev/sda6            2053        2302    2008093+  83  Linux
 
删除主分区 sad3 后可以看到磁盘信息不在包含 sad3
Command (m for help): d
Partition number (1-6): 4
 
Command (m for help): p
 
Disk /dev/sda: 21.4 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280bytes
 
  Device Boot      Start         End      Blocks  Id  System
/dev/sda1  *           1          13      104391  83  Linux
/dev/sda2              14        1288   10241437+  83  Linux
```

删除扩展分区 sad4 后可以看到扩展分区，逻辑分区都被删除（因为逻辑分区是由扩展分区衍生而来的）。  
  
### 增加磁盘分区  
磁盘分区最多只能有4个主分区+扩展分区组成，其中扩展分区最多只能有一个，剩下在创建的分区都是由扩展分区衍生出来的逻辑分区   

**举例1. 由于磁盘现分区分为3个主分区，1个扩展分区。因此在创建时将直接创建逻辑分区，而不在询问是否创建主分区或者扩展分区**   

```
[root@localhost ~]# fdisk /dev/sda
 
The number of cylinders for this disk isset to 2610.
There is nothing wrong with that, but thisis larger than 1024,
and could in certain setups cause problemswith:
1) software that runs at boot time (e.g.,old versions of LILO)
2) booting and partitioning software fromother OSs
  (e.g., DOS FDISK, OS/2 FDISK)
 
Command (m for help): p
 
Disk /dev/sda: 21.4 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280bytes
 
  Device Boot      Start         End      Blocks  Id  System
/dev/sda1  *           1          13      104391  83  Linux
/dev/sda2              14        1288   10241437+  83  Linux
/dev/sda3            1289        1925    5116702+  83  Linux
/dev/sda4            1926        2610    5502262+   5  Extended
/dev/sda5            1926        2052    1020096   82 Linux swap / Solaris
/dev/sda6            2053        2302    2008093+  83  Linux
 
Command (m for help): n
First cylinder (2303-2610, default 2303):
```

**举例2：创建主/扩展分区**   

```
[root@localhost ~]# fdisk /dev/sda
 
The number of cylinders for this disk isset to 2610.
There is nothing wrong with that, but thisis larger than 1024,
and could in certain setups cause problemswith:
1) software that runs at boot time (e.g.,old versions of LILO)
2) booting and partitioning software fromother OSs
  (e.g., DOS FDISK, OS/2 FDISK)
 
Command (m for help): d //先将主分区和逻辑分区删除（如果为4个则默认创建逻辑分区）
Partition number (1-6): 2
 
Command (m for help): d
Partition number (1-6): 4
 
Command (m for help): p
 
Disk /dev/sda: 21.4 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280bytes
 
  Device Boot      Start         End      Blocks  Id  System
/dev/sda1  *           1          13      104391  83  Linux
/dev/sda3            1289        1925    5116702+  83  Linux
 
Command (m for help): n
Command action
   e  extended
   p  primary partition (1-4)
```

提示用户选择是是创建主分区还是扩展分区   

**举例3.创建逻辑分区与扩展分区**   

```
root@localhost ~]# fdisk /dev/sda
 
The number of cylinders for this disk isset to 2610.
There is nothing wrong with that, but thisis larger than 1024,
and could in certain setups cause problemswith:
1) software that runs at boot time (e.g.,old versions of LILO)
2) booting and partitioning software fromother OSs
  (e.g., DOS FDISK, OS/2 FDISK)
 
Command (m for help): p
 
Disk /dev/sda: 21.4 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280bytes
 
  Device Boot      Start         End      Blocks  Id  System
/dev/sda1  *           1          13      104391  83  Linux
/dev/sda2             14        1288   10241437+  83  Linux
/dev/sda3            1289        1925    5116702+  83  Linux
/dev/sda4            1926        2610    5502262+   5  Extended
/dev/sda5            1926        2052    1020096   82  Linux swap / Solaris
/dev/sda6            2053        2302    2008093+  83  Linux
 
Command (m for help): d
Partition number (1-6): 4
 
Command (m for help): n
Command action
  e   extended
  p   primary partition (1-4)
e
Selected partition 4
First cylinder (1926-2610, default 1926):
Using default value 1926
Last cylinder or +size or +sizeM or +sizeK(1926-2610, default 2610):
Using default value 2610
 
Command (m for help): p
 
Disk /dev/sda: 21.4 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280bytes
 
  Device Boot      Start         End      Blocks  Id  System
/dev/sda1  *           1          13      104391  83  Linux
/dev/sda2              14        1288   10241437+  83  Linux
/dev/sda3            1289        1925    5116702+  83  Linux
/dev/sda4            1926        2610    5502262+   5  Extended
```

**sd4为新创建的扩展分区，大小为从柱面1926到2610**   

```
Command (m for help): n
Firstcylinder (1926-2610, default 1926):
Usingdefault value 1926
Lastcylinder or +size or +sizeM or +sizeK (1926-2610, default 2610): +500M
```

对于此处可以指定柱面号码，以可以通过+XXM 指定大小，让其自动分配柱面  
Command (m for help): p   
  
```
Disk /dev/sda: 21.4 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280bytes

  Device Boot      Start         End      Blocks  Id  System
/dev/sda1  *           1          13      104391  83  Linux
/dev/sda2              14        1288   10241437+  83  Linux
/dev/sda3            1289        1925    5116702+  83  Linux
/dev/sda4            1926        2610    5502262+   5  Extended
/dev/sda5            1926        1987      497983+ 83  Linux
```

**sd5为新创建的逻辑分区，大小为500M**   

### 内核查找分区
**当我们增加分区后，系统让我们 reboot 以加载分区。也可以不用重启，只需要通知内容重新查找分区即可**   

```
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition tablefailed with error 16: 设备或资源忙.
The kernel still uses the old table.
The new table will be used at the nextreboot.
Syncing disks.
[root@localhost~]# partprobe
```

## 磁盘格式化
分区完毕后要进行文件系统的格式化   

**mkfs**   
**语法：**mkfs[-t 文件系统格式] 设备文件名   
**选项与参数：**   
-t:文件系统格式，例如 ext3,ext2,vfat 等   

**举例**   
 
```
[root@localhost ~]# mkfs -t ext3 /dev/sda7
mke2fs 1.39 (29-May-2006)
Filesystemlabel=
OS type: Linux
Blocksize=1024 (log=0)
Fragment size=1024 (log=0)
50200 inodes, 200780 blocks
10039 blocks (5.00%) reserved for the superuser
First data block=1
Maximum filesystem blocks=67371008
25 block groups
8192 blocks per group, 8192 fragments pergroup
2008 inodes per group
Superblock backups stored on blocks:
       8193, 24577, 40961, 57345, 73729

Writing inode tables: done                           
Creating journal (4096 blocks): done
Writing superblocks and filesystemaccounting information: done

This filesystem will be automaticallychecked every 37 mounts or
180 days, whichever comes first.  Use tune2fs -c or -i to override.
```

其中文件系统 Label 以及 iBLOCK 大小均采用默认大小。如果对于 EXT2/EXT3 我们对这些信息由特殊的需求,可以使用 mke2fs   

**mke2fs**   
**语法：**mke2fs[-b block大小] [-i inode 大小] [-L 卷标] [-cj] 设备   
**选项与参数：**   
-b:设置 block 大小，目前支持1024,2048,4096   
-i:多少容量给予一个 inode   
-c:检查磁盘错误   
-L:卷标名称（Label）   
-j:自动加入日志系统成为 EXT3文件系统，不加在默认为 EXT2   

**举例**   

```
[root@localhost ~]# mke2fs -b 2048 -i 4096-L "TKFDISK" -j /dev/sda7
mke2fs 1.39 (29-May-2006)
Filesystemlabel=TKFDISK
OS type: Linux
Blocksize=2048 (log=1)
Fragment size=2048 (log=1)
50288 inodes, 100390 blocks
5019 blocks (5.00%) reserved for the superuser
First data block=0
Maximum filesystem blocks=103809024
7 block groups
16384 blocks per group, 16384 fragments pergroup
7184 inodes per group
Superblock backups stored on blocks:
       16384, 49152, 81920

Writing inode tables: done                           
Creatingjournal (4096 blocks): done
Writing superblocks and filesystemaccounting information: done

This filesystem will be automaticallychecked every 31 mounts or
180 days, whichever comes first.  Use tune2fs -c or -i to override.
```

## 磁盘检测(fsck)
**语法: **fsck [-t 文件系统格式] [-ACay]   
**选项与参数**   
-t  ：文件系统格式。   
-A  ：依据/etc/fstab 的内容，将需要的装置扫瞄一次。   
-a  ：自动修复检查到的有问题的扇区.    
-y  ：与 -a 类似，但是某些 filesystem 仅支持 -y 这个参数   
-C  ：可以在检验的过程当中，使用一个直方图来显示目前的进度！    

EXT2/EXT3 的额外选项功能：(e2fsck 这支命令所提供)   
-f  ：强制检查！一般来说，如果 fsck 没有发现任何 unclean 的旗标，不会主动进入细部检查的，如果您想要强制 fsck 进入细部检查，就得加上 -f    
-D  ：针对文件系统下的目录进行优化配置。    

**举例**   

```
[root@localhost ~]# fsck -Cf /dev/sda7
fsck 1.39 (29-May-2006)
e2fsck 1.39 (29-May-2006)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure                                          
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
TKFDISK: 11/50288 files (9.1%non-contiguous), 7673/100390 blocks 
```

**说明：**需要磁盘检查的分区不能挂载在系统上，需要先被卸载才能磁盘检测   
 
## 大容量磁盘分区(parted)  
由于 fdisk 无法支持到高于2 TB 以上的分区，此时就需要 parted 来处理了    

**语法：**parted [设备] [命令 [参数]]     
**选项与参数：**   
新增分区：mkpart [primary|logical|extended] [ext3|vfat]开始结束   
分区表：print   
删除分区：rm [partition]    

**举例1：查看分区表**    

```
[root@bogon ~]# parted /dev/sda print

Model: VMware, VMware Virtual S (scsi)
Disk /dev/sda: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start   End     Size    Type      File system  标志
 1      32.3kB  107MB   107MB   主分区    ext3         启动
 2      107MB   10.6GB  10.5GB  主分区    ext3             
 3      10.6GB  15.8GB  5240MB  主分区    ext3             
 4      15.8GB  21.5GB  5634MB  扩展分区                   
 5      15.8GB  16.9GB  1045MB  逻辑分区  linux-swap       

信息: 如果必要，不要忘记更新 /etc/fstab。  
```

通过以上信息可以看出，扩展分区到21.5 G，逻辑分区使用到16.9 G，那么16.9 G~21.5 G只部分空间还为被使用（未被分区）    

**举例2：新增分区**   

```
[root@bogon ~]# parted /dev/sda mkpart logical ext3 16.9G 18.9G
信息: 如果必要，不要忘记更新 /etc/fstab。                                 

[root@bogon ~]# parted /dev/sda print

Model: VMware, VMware Virtual S (scsi)
Disk /dev/sda: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start   End     Size    Type      File system  标志
 1      32.3kB  107MB   107MB   主分区    ext3         启动
 2      107MB   10.6GB  10.5GB  主分区    ext3             
 3      10.6GB  15.8GB  5240MB  主分区    ext3             
 4      15.8GB  21.5GB  5634MB  扩展分区                   
 5      15.8GB  16.9GB  1045MB  逻辑分区  linux-swap       
 6      16.9GB  18.9GB  2023MB  逻辑分区   
```

**举例3：删除分区**   

```
[root@bogon ~]# parted /dev/sda rm 6
信息: 如果必要，不要忘记更新 /etc/fstab。                                 

[root@bogon ~]# parted /dev/sda print

Model: VMware, VMware Virtual S (scsi)
Disk /dev/sda: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start   End     Size    Type      File system  标志
 1      32.3kB  107MB   107MB   主分区    ext3         启动
 2      107MB   10.6GB  10.5GB  主分区    ext3             
 3      10.6GB  15.8GB  5240MB  主分区    ext3             
 4      15.8GB  21.5GB  5634MB  扩展分区                   
 5      15.8GB  16.9GB  1045MB  逻辑分区  linux-swap       

信息: 如果必要，不要忘记更新 /etc/fstab。 
```

**说明：parted 分区提交即执行，因此使用起来需小心**   

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)