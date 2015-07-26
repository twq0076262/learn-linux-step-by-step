# Linux 学习记录--内存交换空间的构建

# 内存交换空间的构建

我们知道 CPU 计算与数据的存储都会使用到内存，使用内存可以大大减少从磁盘读取的时间，但是当物理内存不足时，就需要暂时将用不到的程序和数据挪到内存交换空间(swap)

**作法：**  
**1.创建分区（fdisk ,文件）**   
**2.格式化为 swap**  
**3.启动**  
**4.查看**   

## 创建分区

**举例**  

```
[root@bogon ~]# fdisk /dev/sda

The number of cylinders for this disk is set to 2610.
There is nothing wrong with that, but this is larger than 1024,
and could in certain setups cause problems with:
1) software that runs at boot time (e.g., old versions of LILO)
2) booting and partitioning software from other OSs
   (e.g., DOS FDISK, OS/2 FDISK)

Command (m for help): p

Disk /dev/sda: 21.4 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1          13      104391   83  Linux
/dev/sda2              14        1288    10241437+  83  Linux
/dev/sda3            1289        1925     5116702+  83  Linux
/dev/sda4            1926        2610     5502262+   5  Extended
/dev/sda5            1926        2052     1020096   82  Linux swap / Solaris
/dev/sda6            2053        2115      506016   83  Linux

Command (m for help): t
Partition number (1-6): 6
Hex code (type L to list codes): 82
Changed system type of partition 6 to 82 (Linux swap / Solaris)

Command (m for help): p

Disk /dev/sda: 21.4 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1          13      104391   83  Linux
/dev/sda2              14        1288    10241437+  83  Linux
/dev/sda3            1289        1925     5116702+  83  Linux
/dev/sda4            1926        2610     5502262+   5  Extended
/dev/sda5            1926        2052     1020096   82  Linux swap / Solaris
/dev/sda6            2053        2115      506016   82  Linux swap / Solaris

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: 设备或资源忙.
The kernel still uses the old table.
The new table will be used at the next reboot.
Syncing disks.
part[root@bogon ~]# partprobe
这里需要在进行设置下 system ID
```

## 格式化  
**语法：**mkswap 设备名称  

**举例**  
 
```
[root@bogon ~]# mkswap /dev/sda6

Setting up swapspace version 1, size = 518156 kB
```

## 启动|关闭   
**语法：**swapon [-s]设备名称   
swapoff 设备名称   
**选项与参数：**   
-s:查看所有 swap 文件系统   

**举例1：启动 swap**    

```
[root@bogon ~]# swapon /dev/sda6
```

**举例2：查看所有 swap**    

```
[root@bogon ~]# swapon -s
Filename                                Type            Size    Used    Priority
/dev/sda5                               partition       1020088 0       -1
/dev/sda6                               partition       506008  0       -2
```

## 查看  
**语法：**free   

**举例**   

```
             total       used       free     shared    buffers     cached
Mem:       2074972    1380996     693976          0     106740    1000288
-/+ buffers/cache:     273968    1801004
Swap:      1526096          0    1526096
```

可以看到 Swap 空间增加1526096   

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)