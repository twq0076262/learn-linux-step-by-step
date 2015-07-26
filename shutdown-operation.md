# Linux 学习记录--关机相关操作

# 关机相关指令

将数据同步写入硬盘指令 sync  
关机指令 shutdown   
重启，关机指令，reboot halt poweroff  
只有 root 用户可以进行关机操作  

## 数据同步写入磁盘 sync  

由于所有的数据都要数据都要读入到内存才能被 CPU 所处理，但有时数据又需要由内存写回硬盘中，为了提高性能，已经加载到内存的中的数据不会理解被写回硬盘，当内存数据更改单位同步到硬盘中如果断电回引起数据，因此 sync 指令时强行将内存数据写入硬盘

reboot/shutdown/halt 执行前都会自动调用 sync  

## 关机指令 shutdown

**shutdown [-t 秒][arkhncfF] 时间 [警告信息]**  
-t sec: -t 后面加秒数，也即“过几秒后关机”的意思  
-k:不是真的关机，知识发出警告信息  
-r:将系统服务停掉后立即重启  
-h:将系统服务停掉后立即关机  
-n:不经过 init 程序,直接以 shutdown 功能来关机  
-f:关机并开机之后，强制略过 fsck 的磁盘检查  
-F:系统重启之后，强制进行 fsck 的磁盘检查  
-c:取消已经在进行的 shutdown 命令内容  

**举例**：  
shutdown -h  10 “I will  shutdown after 10 mins”  
告诉大家10分钟后服务器重启  
shutdown  –h  now  
立刻关机  
shutdown  -h 20:15  
20:15分自动关机  
Shutdown  -r now  
立刻重启启动  
Shutdown  -r 30  “The System will Reboot”  
30分钟后重新启动并通知在线用户  
Shutdown  -k  now  “The System will Reboot”  
仅发出警告，并不会重启  

## 关机指令 halt

halt 就是调用 shutdown –h  now  halt 执行时﹐杀死应用进程﹐执行 sync 系统调用﹐文件系统写操作完成后就会停止内核
**halt [-dfinpw]**
   -d :不要在 wtmp 中记录。 
   -f :不论目前的 runlevel 为何，不调用 shutdown 即强制关闭系统。 
   -i :在 halt 之前，关闭全部的网络界面。 
   -n :halt 前，不用先执行 sync。 
   -p :halt 之后，执行 poweroff。 
   -w :仅在 wtmp 中记录，而不实际结束系统。 

 halt 会先检测系统的 runlevel。若 runlevel 为0或6，则关闭系统，否则即调用 shutdown 来关闭系统。


## 切换执行等级 init

linux 操作系统自从开始启动至启动完毕需要经历几个不同的阶段，这几个阶段就叫做 runlevel，通常有8个 runlevel  
Runlevel System State  
 0 Halt the system  
 1 Single user mode  
 2 Basic multi user mode  
 3 Multi user mode  
 5 Multi user mode with GUI  
 6 Reboot the system  
 S, s Single user mode  
多数的桌面的 linux 系统缺省的 runlevel 是5，用户登陆时是图形界面，而多数的服务器版本的linux 系统缺省的 runlevel 是3，用户登陆时是字符界面，runlevel 1和2除了调试之外很少使用，runlevel s 和 S 并不是直接给用户使用，而是用来为 Single user mode 作准备。

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)