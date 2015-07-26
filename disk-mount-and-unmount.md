# Linux 学习记录--磁盘挂载与卸载

# 磁盘挂载与卸载

文件系统的格式化完毕后，需要将文件系统挂载到目录树上我们才可以使用，如果你要用来挂载的目录里面并不是空的，那么挂载了文件系统之后，原目录下的东西就会暂时的消失。举个例子来说，假设你的 /home 原本与根目录 (/) 在同一个文件系统中，底下原本就有 /home/test 与 /home/vbird 两个目录。然后你想要加入新的硬盘，并且直接挂载 /home 底下，那么当你挂载上新的分割槽时，则 /home 目录显示的是新分割槽内的数据，至于原先的 test 与 vbird 这两个目录就会暂时的被隐藏掉了！并不是被覆盖掉，而是暂时的隐藏了起来，等到新分割槽被卸除之后，则 /home 原本的内容就会再次的跑出来  

## 磁盘挂载  
**语法：**  
[root@www ~]# mount -a  
[root@www ~]# mount [-l]  
[root@www ~]# mount [-t 文件系统] [-LLabel 名] [-o 额外选项]   装置文件名  挂载点  
**选项与参数：**  
-a  ：依照配置文件/etc/fstab 的数据将所有未挂载的磁盘都挂载上来  
-l  ：单纯的输入 mount 会显示目前挂载的信息。加上-l 可增列 Label 名称！  
-t  ：与 mkfs 的选项非常类似的，可以加上文件系统种类来指定欲挂载的类型。常见的 Linux 支持类型有：ext2, ext3, vfat, reiserfs, iso9660(光盘格式),nfs, cifs,smbfs(此三种为网络文件系统类型)  
-n  ：在默认的情况下，系统会将实际挂载的情况实时写入 /etc/mtab 中，以利其他程序的运行。但在某些情况下(例如单人维护模式)为了避免问题，会刻意不写入。此时就得要使用这个 -n 的选项了。  
-L  ：系统除了利用装置文件名(例如 /dev/hdc6) 之外，还可以利用文件系统的标头名称   
    (Label)来进行挂载。最好为你的文件系统取一个独一无二的名称吧！  
-o  ：后面可以接一些挂载时额外加上的参数！比方说账号、密码、读写权限等：  
          ro, rw:      挂载文件系统成为只读(ro) 或可擦写(rw)  
    async, sync:  此文件系统是否使用同步写入(sync) 或异步 (async) 的内存机制，请参考文件系统运行方式。默认为 async。  
    auto, noauto: 允许此 partition 被以 mount -a 自动挂载(auto)  
    dev, nodev:   是否允许此 partition 上，可创建装置文件？ dev 为可允许  
    suid, nosuid: 是否允许此 partition 含有 suid/sgid 的文件格式？  
    exec, noexec: 是否允许此 partition 上拥有可运行 binary 文件？  
     user, nouser: 是否允许此 partition 让任何使用者运行 mount ？一般来说 mount 仅有 root 可以进行，但下达 user 参数，则可让一般 user 也能够对此 partition 进行 mount 。  
    defaults:     默认值为：rw,suid, dev, exec, auto, nouser, and async  
    remount:      重新挂载，这在系统出错，或重新升级参数时，很有用  

**举例1：挂载 EXT2/EXT3文件系统**  

```
[root@localhost ~]# mkdir /mnt/sda7
[root@localhost ~]# mount /dev/sda7/mnt/sda7
[root@localhost ~]# df
文件系统               1K-块        已用     可用 已用% 挂载点
/dev/sda2              9920624   4329132  5079424  47% /
/dev/sda3              4956316    141272   4559212  4% /home
/dev/sda1               101086     11726    84141  13% /boot
tmpfs                  1037452         0  1037452   0% /dev/shm
/dev/sda6              1976312     42072  1833836   3% /mnt/sda6
.host:/               80148252  59099424 21048828  74% /mnt/hgfs
/dev/sda7               194450      9016   175396   5% /mnt/sda7
```

**举例2：挂载 cd/dvd 光盘**  

```
[root@localhost ~]# mount -t iso9660/dev/cdrom /media/cdrom/
mount: block device /dev/cdrom iswrite-protected, mounting read-only
[root@localhost ~]# df
文件系统               1K-块        已用     可用 已用% 挂载点
/dev/sda2              9920624   4329132  5079424  47% /
/dev/sda3              4956316    141272  4559212   4% /home
/dev/sda1               101086     11726    84141  13% /boot
tmpfs                  1037452         0  1037452   0% /dev/shm
/dev/sda6              1976312     42072  1833836   3% /mnt/sda6
.host:/               80148252  59231380 20916872  74% /mnt/hgfs
/dev/sda7               194450      9016   175396   5% /mnt/sda7
/dev/hdc              1651852   1651852         0 100% /media/cdrom
```

**举例3：挂载 U 盘**  

```
[root@localhost ~]# mkdir /media/flash
[root@localhost ~]# mount -t vfat -o iocharset=cp950 /dev/sdb1 /media/flash
// iocharset为指定中文字符
[root@localhost ~]# df
文件系统               1K-块        已用     可用 已用% 挂载点
/dev/sda2              9920624   4329164  5079392  47% /
/dev/sda3              4956316    141272  4559212   4% /home
/dev/sda1               101086     11726    84141  13% /boot
tmpfs                  1037452         0  1037452   0% /dev/shm
/dev/sda6              1976312     42072  1833836   3% /mnt/sda6
.host:/               80148252  59231444 20916808  74% /mnt/hgfs
/dev/sda7               194450      9016   175396   5% /mnt/sda7
/dev/hdc               1651852   1651852         0 100% /media/cdrom
/dev/sdb1              3977678   1385740  2591938  35% /media/flash
```

**举例4：挂载信息会写入/etc/mtab 文件中**  

```
[root@localhost ~]# cat /etc/mtab
/dev/sda2 / ext3 rw 0 0
proc /proc proc rw 0 0
sysfs /sys sysfs rw 0 0
devpts /dev/pts devpts rw,gid=5,mode=620 00
/dev/sda3 /home ext3 rw 0 0
/dev/sda1 /boot ext3 rw 0 0
tmpfs /dev/shm tmpfs rw 0 0
/dev/sda6 /mnt/sda6 ext3 rw 0 0
none /proc/sys/fs/binfmt_misc binfmt_miscrw 0 0
.host:/ /mnt/hgfs vmhgfs rw,ttl=1 0 0
none /proc/fs/vmblock/mountPoint vmblock rw0 0
sunrpc /var/lib/nfs/rpc_pipefs rpc_pipefsrw 0 0
/dev/sda7 /mnt/sda7 ext3 rw 0 0
/dev/hdc /media/cdrom iso9660 ro 0 0
/dev/sdb1 /media/flash vfatrw,iocharset=cp950 0 0
```

**举例5：系统默认挂载信息会记录在/etc/fstab 中**  

```
[root@localhost~]# cat /etc/fstab
LABEL=/                 /                       ext3    defaults        1 1
LABEL=/home             /home                   ext3    defaults        1 2
LABEL=/boot             /boot                   ext3    defaults        1 2
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0
LABEL=SWAP-sda5         swap                    swap    defaults        0 0
/dev/sda6               /mnt/sda6               ext3    defaults   1 2
``` 

## 磁盘卸载  
**语法：**umount[-fn] 设备文件名或者挂载点  
选项和参数：  
-f:强制卸载  
-n:不更新/etc/mtab 文件  

**举例：**  

```
[root@localhost ~]# df
文件系统               1K-块        已用     可用 已用% 挂载点
/dev/sda2              9920624   4329164  5079392  47% /
/dev/sda3              4956316    141272  4559212   4% /home
/dev/sda1               101086     11726    84141  13% /boot
tmpfs                  1037452         0  1037452   0% /dev/shm
/dev/sda6              1976312     42072  1833836   3% /mnt/sda6
.host:/               80148252  59231444 20916808  74% /mnt/hgfs
/dev/sda7               194450      9016   175396   5% /mnt/sda7
/dev/hdc               1651852   1651852         0 100% /media/cdrom
/dev/sdb1              3977678   1385740  2591938  35% /media/PENDRIVE
/dev/sdb1              3977678   1385740  2591938  35% /media/flash
[root@localhost ~]# umount /media/flash
[root@localhost ~]# umount /media/cdrom
[root@localhost ~]# umount /dev/sda7
[root@localhost ~]# df
文件系统               1K-块        已用     可用 已用% 挂载点
/dev/sda2              9920624   4329164  5079392  47% /
/dev/sda3              4956316    141272  4559212   4% /home
/dev/sda1               101086     11726    84141  13% /boot
tmpfs                  1037452         0  1037452   0% /dev/shm
/dev/sda6              1976312     42072  1833836   3% /mnt/sda6
.host:/               80148252  59231444 20916808  74% /mnt/hgfs
/dev/sdb1              3977678   1385740  2591938  35% /media/PENDRIVE
```

## 磁盘参数修改
### 文件系统卷标 (Label) 修改
磁盘的挂载可以通过文件系统的卷标(Label)来进行,但是要保证这个值的唯一性
我们可以通过 mke2fs 进行磁盘格式化来指定这个值，也可以通过 elabel 或 tune2fs 来修改这个值
**e2label**  
**语法：**e2label 设备名称 新的 Label 名称  

**举例：**修改 sda7Label 名称   

```
[root@localhost ~]# e2label /dev/sda7"tkflabel"
[root@localhost ~]# df /dev/sda7
文件系统               1K-块        已用     可用 已用% 挂载点
-                      1037452       156  1037296   1% /dev
[root@localhost ~]# dumpe2fs  /dev/sda7
dumpe2fs 1.39 (29-May-2006)
Filesystemvolume name:   tkflabel
```

**举例2：使用新 Label 进行挂载**   

```
[root@localhost ~]# mount -L"tkflabel" /mnt/sda7
[root@localhost ~]# df
文件系统               1K-块        已用     可用 已用% 挂载点
/dev/sda2              9920624   4329164  5079392  47% /
/dev/sda3              4956316    141272  4559212   4% /home
/dev/sda1               101086     11726    84141  13% /boot
tmpfs                  1037452         0  1037452   0% /dev/shm
/dev/sda6              1976312     42072  1833836   3% /mnt/sda6
.host:/              80148252  59231444 20916808  74% /mnt/hgfs
/dev/sdb1              3977678   1385740  2591938  35% /media/PENDRIVE
/dev/sda7               194450      9016   175396   5% /mnt/sda7
```

**tune2fs**   
**语法：**tune2fs[-jlL] 设备名称   
**选项与语法：**   
-l:类似 dump2fs –h 将 superblock 信息读取出来   
-j:将 EXT2文件系统转换为 ext3   
-L:类似 e2labe 功能   

**举例：**    

```
[root@localhost ~]# tune2fs -L"newlabel" /dev/sda7
tune2fs 1.39 (29-May-2006)
[root@localhost ~]# tune2fs -l /dev/sda7
tune2fs 1.39 (29-May-2006)
Filesystemvolume name:   newlabel
```

## 开机挂载

前面说到过开机挂载主要是从/etc/fstab 文件中读取挂载信息进行挂载，话句话说主要进行更改这个文件，添加新的挂载信息就可以进行自动开机加载    

```
[root@www ~]# cat /etc/fstab
# Device        Mount point   filesystem parameters    dump fsck
LABEL=/1          /           ext3       defaults        1 1
LABEL=/home       /home       ext3       defaults        1 2
LABEL=/boot       /boot       ext3       defaults        1 2
tmpfs             /dev/shm    tmpfs     defaults        0 0
devpts            /dev/pts    devpts    gid=5,mode=620  0 0
sysfs             /sys        sysfs      defaults        0 0
proc              /proc       proc       defaults        0 0
LABEL=SWAP-hdc5   swap       swap       defaults        0 0
```

**Device：设备卷标(Label)**    
**Mountpoint ：挂载点**    
**Filesystem:文件系统类型**    
**Parameters:文件系统参数（-o 后面的参数）**    
**Dump:是否被 dump 备份**    
**Fsck:是否以 FSCK 检验扇区**    
启动的过程中，系统默认会以 fsck 检验我们的 filesystem 是否完整 (clean)。 不过，某些 filesystem 是不需要检验的，例如内存置换空间 (swap) ，或者是特殊文件系统例如 /proc 与 /sys 等等。所以，在这个字段中，我们可以配置是否要以 fsck 检验该 filesystem。 0 是不要检验， 1 表示最早检验(一般只有根目录会配置为 1)， 2 也是要检验，不过 1 会比较早被检验啦！ 一般来说，根目录配置为 1 ，其他的要检验的 filesystem 都配置为 2 就好了。   
## 特殊设备 loop 挂载   

假如我们分区不够合理，没有足够的空间在创建一个分区，那么我们可以在已有分区上创建一个大文件，并将这个大文件作为单独的文件系统进行挂载。这就用到了特殊文件挂载    

**作法：**   
**1.创建大文件**   
**2.格式化**   
**3.挂载**    

**举例1：创建大文件**    

```
[root@bogon ~]# df -h
文件系统              容量  已用 可用 已用% 挂载点
/dev/sda2             9.5G  4.1G  5.0G  45% /
/dev/sda3             4.8G  138M  4.4G   4% /home
/dev/sda1              99M   12M   83M  13% /boot
tmpfs                1014M     0 1014M   0% /dev/shm
.host:/                49G  6.5G   43G  14% /mnt/hgfs
[root@bogon ~]# dd if=/dev/zero of=/home/newdev bs=1M count=512
512+0 records in
512+0 records out
536870912 bytes (537 MB) copied, 6.97647 seconds, 77.0 MB/s
[root@bogon ~]# df -h
文件系统              容量  已用 可用 已用% 挂载点
/dev/sda2             9.5G  4.1G  5.0G  45% /
/dev/sda3             4.8G  651M  3.9G  15% /home
/dev/sda1              99M   12M   83M  13% /boot
tmpfs                1014M     0 1014M   0% /dev/shm
.host:/                49G  6.5G   43G  14% /mnt/hgfs
[root@bogon ~]# ll /home/newdev 
-rw-r--r-- 1 root root 536870912 02-27 20:14 /home/newdev
```

以上发现 home 文件系统使用量增大了512 M   

**举例2：格式化**    

```
[root@bogon ~]# mkfs -t ext3 /home/newdev 
mke2fs 1.39 (29-May-2006)
/home/newdev is not a block special device.
Proceed anyway? (y,n) y
Filesystem label=
OS type: Linux
Block size=1024 (log=0)
Fragment size=1024 (log=0)
……
```

**举例3：挂载**    

```
[root@bogon ~]# mount -o loop /home/newdev /media/cdrom
[root@bogon ~]# df -h
文件系统              容量  已用 可用 已用% 挂载点
/dev/sda2             9.5G  4.1G  5.0G  45% /
/dev/sda3             4.8G  651M  3.9G  15% /home
/dev/sda1              99M   12M   83M  13% /boot
tmpfs                1014M     0 1014M   0% /dev/shm
.host:/                49G  6.6G   43G  14% /mnt/hgfs
/home/newdev          496M   19M  452M   4% /media/cdrom
```

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)