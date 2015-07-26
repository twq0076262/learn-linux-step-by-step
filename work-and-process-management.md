# Linux 学习记录--工作管理与进程管理

# 工作管理与进程管理

始终不能明白进程的正确理解和定义。就说我自己的理解吧   
进程是 CPU 调度的基本单位，对于 unix like 来说，当我们登录取得 bash 时，系统会根据用户的uid 和 gid 分配给我们一个进程，在当前 bash 下，这个进程就是所有进程的父进程，当我们执行一些命令时，每个命令都由一个新的子进程来完成。  
 
## 工作管理  

在单一终端下，可以同时进行多项工作，如：一边复制数据，一边查询文件。每一项工作都由独立的子进程来完成，他们的父进程就是当前终端对应 bash 的那个进程。  
 
**对于终端来说分为前台和后台**     
前台：你可以控制于执行命令的那个环境（串行工作）  
后台：可以自行运行的工作，无法使用 ctrl+c 终端它（并行工作）  

如果所有的工作都由前台来做，那么必须等一个工作处理完成才能进行下一个工作，这样做效率很低，因此我们可以把一些不需要人工交互的工作放到后台，使多个工作可以共同执行  

**说明：前台和后台是针对同一个终端来说，tty1环境是无法管理 tty2的**  

## 查看目前的后台工作状态(jobs)  

**语法**：jobs[-lrs]  
**选项与参数**：  
-l:列出工作与命令串之外，同时列出 PID  
-r:仅列出后台 run 的工作  
-s: 仅列出后台 stop 的工作  

**举例**  

```
[root@localhost tmp]# jobs -l
[1]-  9154 停止                  vim newfile.txt
[2]+  9368 停止                  find / -print
[3]   9374 Running                 tar -jcpP -f /tmp/etc1.tar.bz2 /etc &
```

**以上输出的格式为：工作序号|顺序 PID 状态 命令**  
**顺序**：分为(+,-,空白),+号代表最后一个被放到后台的工作；-号代表倒数第二个被放到后台的工作，倒数第三个及以后用空白    

## 直接将命令放到后台执行 ( &)  

[root@localhost tmp]# tar -jcpP -f/tmp/etc.tar.bz2 /etc &  
[2] 8985 
其中【2】表示工作号，8985表示处理这个工作的子进程号  
如果我们将压缩信息显示出来   
[root@localhost tmp]# tar –jcpP -v -f/tmp/etc.tar.bz2 /etc &  
虽然这个工作在后台进行，但是输出信息还是会在前台输出的，因此可以应用数据流重定向将输出信息写到文件  
  
## 将目前工作放到后台并暂停(ctrl+z)  

[root@localhost tmp]# vim newfile.txt  
=>按下 ctrl+z  
[1]+ Stopped                 vimnewfile.txt  

## 将后台工作拿到前台来处理(fg)  

**语法**：fg %工作序号  
           fg –  
           fg +  

## 将后台工作由停止变为运行(bg)  

**语法**：fg %工作序号  

**举例**：  

```
[root@localhost tmp]# jobs -l;bg %2;jobs -l
[1]-  9154 停止                  vim newfile.txt
[2]+  9729 停止                  tar -jcpP -f /tmp/etc1.tar.bz2 /etc
[2]+ tar -jcpP -f /tmp/etc1.tar.bz2 /etc &
[1]+  9154 停止                  vim newfile.txt
[2]-  9729 Running                 tar -jcpP -f /tmp/etc1.tar.bz2 /etc &
```

## 后台任务管理(kill)  

**语法**：kill –signal  %工作序号  
**Sighal**:  
1:重新读取一次参数的配置文件  
2:代表有 ctrl+c 同样的操作  
9:立刻强制删除一个工作  
15.以正常方式终止工作  

说明：unix like 中的信号64个，比较复杂以后在整体学习  

**举例**：  

```
[root@localhost tmp]# jobs -l;kill -9 %1; jobs -l
[1]+  已杀死               tar -jcpP -f /tmp/etc1.tar.bz2 /etc
```

## 进程管理  

### 进程的查看  
**静态进程查看(ps)**  


**语法**：  
ps aux|ps -lA 查看系统所有进程数据  
ps –l 查看自己相关进程  
ps –axjf 查看进程树   
  
**举例**：  
 
```
[root@localhost ~]# su -l tkf
[tkf@localhost tmp]$ tar -cjPp -f ./home.bz2 /home 

[1]+  Stopped                 tar -cjPp -f ./home.bz2 /home

查看 ps -l
[tkf@localhost tmp]$ ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S   500 10894 10893  0  75   0 -  1224 wait   pts/1    00:00:00 bash
0 T   500 10951 10894  0  78   0 -  1224 finish pts/1    00:00:00 tar
0 T   500 10952 10951  0  78   0 -  2289 finish pts/1    00:00:01 bzip2

查看 ps -lA
[tkf@localhost tmp]$ ps –lA
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0    1     0  0  75   0 -   544 stext  ?        00:00:00 init
0 R     0 10691    1  0  75   0 - 20262 stext  ?        00:00:01 gnome-terminal
4 S     0 10694 10691  0  77   0 -   649 stext  ?        00:00:00 gnome-pty-help
0 S     0 10695 10691  0  76   0 -  1253 wait   pts/1    00:00:00 bash
4 S     0 10893 10695  0  78   0 -  1319 wait   pts/1    00:00:00 su
4 S   500 10894 10893  0  75   0 -  1224 wait   pts/1    00:00:00 bash
0 T   500 10951 10894  0  78   0 -  1224 finish pts/1    00:00:00 tar
0 T   500 10952 10951  0  78   0 -  2289 finish pts/1    00:00:01 bzip2
```

处理 Init 程序的进程为所有进程的父进程 tar 命令内部会调用 bzip2命令因此产生10952进程，且bzip2进程的父进程就是 tar 进程  
  
l  F：代表这个程序旗标 (process flags)，说明这个程序的总结权限，常见号码有：  
 若为 4 表示此程序的权限为 root ；  
 若为 1 则表示此子程序仅进行复制(fork)而没有实际运行(exec)。  
l  S：代表这个程序的状态 (STAT)，主要的状态有：  
 R (Running)：该程序正在运行中；  
 S (Sleep)：该程序目前正在睡眠状态(idle)，但可以被唤醒(signal)。  
 D ：不可被唤醒的睡眠状态，通常这支程序可能在等待 I/O 的情况(ex>列印)  
 T ：停止状态(stop)，可能是在工作控制(背景暂停)或除错 (traced) 状态；  
 Z (Zombie)：僵尸状态，程序已经终止但却无法被移除至内存外。  
l  UID/PID/PPID：代表[此程序被该 UID 所拥有/程序的 PID 号码/此程序的父程序 PID 号码]  
l  C：代表 CPU 使用率，单位为百分比；  
l  PRI/NI：Priority/Nice 的缩写，代表此程序被 CPU 所运行的优先顺序，数值越小代表该程序越快被 CPU 运行。  
l  ADDR/SZ/WCHAN：都与内存有关，ADDR 是 kernel function，指出该程序在内存的哪个部分，如果是个 running 的程序，一般就会显示[- ] / SZ 代表此程序用掉多少内存 / WCHAN 表示目前程序是否运行中，同样的，若为 - 表示正在运行中。  
l  TTY：登陆者的终端机位置，若为远程登陆则使用动态终端介面 (pts/n)；  
l  TIME：使用掉的 CPU 时间，注意，是此程序实际花费 CPU 运行的时间，而不是系统时间；  
l  CMD：简单描述命令  
 
```
查看 ps aux
[tkf@localhost tmp]$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0   2176   640 ?        Ss   08:50   0:00 init [5]  
root     10691  0.1  0.8  81336 17104 ?        Sl   15:03   0:04 gnome-terminal
root     10694  0.0  0.0   2596   676 ?        S    15:03   0:00 gnome-pty-helper
root     10695  0.0  0.0   5012  1476 pts/1    Ss   15:03   0:00 bash
root     10893  0.0  0.0   5276  1312 pts/1    S    15:16   0:00 su -l tkf
tkf      10894  0.0  0.0   4896  1444 pts/1    S    15:16   0:00 -bash
tkf      10951  0.0  0.0   4896   992 pts/1    T    15:18   0:00 tar -cjPp -f ./home.bz2 /home
tkf      10952  0.0  0.3   9156  7792 pts/1    T    15:18   0:01 bzip2
```

 USER：该 process 属於那个使用者帐号的？  
 PID ：该 process 的程序识别码。  
 %CPU：该 process 使用掉的 CPU 资源百分比；  
 %MEM：该 process 所占用的实体内存百分比；  
 VSZ ：该 process 使用掉的虚拟内存量 (Kbytes)  
 RSS ：该 process 占用的固定的内存量 (Kbytes)  
 TTY ：该 process 是在那个终端机上面运行，若与终端机无关则显示 ?，另外， tty1-tty6 是本机上面的登陆者程序，若为 pts/0 等等的，则表示为由网络连接进主机的程序。  
 STAT：该程序目前的状态，状态显示与 ps -l 的 S 旗标相同 (R/S/T/Z)  
 START：该 process 被触发启动的时间；  
 TIME ：该 process 实际使用 CPU 运行的时间。  
 COMMAND：详细描述命令  

```
查看 ps axjf
[tkf@localhost tmp]$ ps axjf
PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
    1 10691  4628  4628 ?           -1 Sl       0   0:06 gnome-terminal
10691 10694  4628  4628 ?           -1 S        0   0:00  \_ gnome-pty-helper
10691 10695 10695 10695 pts/1    11743 Ss       0   0:00  \_ bash
10695 10893 10893 10695 pts/1    11743 S        0   0:00      \_ su -l tkf
10893 10894 10894 10695 pts/1    11743 S      500   0:00          \_ -bash
10894 10951 10951 10695 pts/1    11743 T      500   0:00              \_ tar -cjPp -f ./home.bz2 /home
10951 10952 10951 10695 pts/1    11743 T      500   0:01              |   \_ bzip2
```

### 动态进程查看(top)  

**语法**：top[-d 数字] [-bn  数字] [-p pid]  
**选项与参数**  
-d:动态刷新时间间隔  
-b:以批次方式执行，与-n 连用  
-n:执行次数  
-p:查看单个进程信息  

**在top 执行过程中可以使用的命令**  
 ？:显示帮助文档  
 P:以 CPU 使用资源排序  
 M: 以 mem 使用资源排序  
 N: 以 PID 排序  
 T: 以 TIME+排序  
 k:给某个 PID 一个信号  
 r:给某个 PID 设置新的 Ni 值  
 q:离开  
 Z|z:设置|取消颜色  
 B|b:设置|取消关键字加粗  
 W:将设置信息写入配置文件  
 n:显示进程数量  
 c:显示完整的 command  
 u:设置查看某个 USER 的进程   

**举例**：  
  
```
[tkf@localhost ~]$ top

top - 10:08:12 up 55 min,  2 users,  load average: 0.01, 0.01, 0.00
Tasks: 162 total,   1 running, 160 sleeping,   0 stopped,   1 zombie
Cpu(s):  1.5%us,  0.7%sy,  0.0%ni, 97.7%id,  0.0%wa,  0.2%hi,  0.0%si,  0.0
Mem:   2074908k total,   640268k used,  1434640k free,    41856k buffers
Swap:  1020088k total,        0k used,  1020088k free,   387260k cached

PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND  
4784 root      15   0 64580  17m  10m S  5.4  0.9   0:06.63 gnome-terminal                                                                    
4502 root      16   0  148m  11m 5964 S  0.6  0.6   0:12.18 Xorg 
```
    
**特殊字段说明**  
us 用户空间占用 CPU 百分比  
sy 内核空间占用 CPU 百分比   
ni 用户进程空间内改变过优先级的进程占用 CPU 百分比   
id 空闲 CPU 百分比  
wa 等待输入输出的 CPU 时间百分比  
hi 硬件中断  
si 软件中断  
st: 实时  
 
USER：该 process 所属的使用者；   
PR ：Priority 的简写，程序的优先运行顺序，越小越早被运行；  
NI ：Nice 的简写，与 Priority 有关，也是越小越早被运行；  
%CPU：CPU 的使用率；   
%MEM：内存的使用率；   
TIME+：CPU 使用时间的累加；   

### 进程树查看(pstree)   
**语法**：pstree[-A|-U] [-up]   
**选项与参数**  
-A  ：各程序树之间的连接以 ASCII 字节来连接；  
-U  ：各程序树之间的连接以 unicode 的字节来连接。在某些终端介面下可能会有错误；  
-p  ：并同时列出每个 process 的 PID；   
-u  ：并同时列出每个 process 的所属帐号名称。   

**举例**   

```
[tkf@localhost ~]$ pstree -Apu
init(1)-+-/usr/bin/sealer(4774)
        |-acpid(3891)
        |-atd(4227)
        |-auditd(3599)-+-audispd(3601)---{audispd}(3602)
        |              `-{auditd}(3600)
        |-automount(3994)-+-{automount}(3995)
        |                 |-{automount}(3996)
        |                 |-{automount}(3999)
        |                 `-{automount}(4002)
```

### 进程的管理   

进程的管理就是给进程发送一个信号，告诉进程做什么事情   
**语法**：kill –signal PID   
           killall [-iIe] –signal 服务名称   

**说明 使用 kill 需要知道 PID，killall 根据服务发送信号**   

**举例1：给 bash 进程发送信号1**   
[root@localhost ~]#  kill -1 $(ps aux |awk '{print $11 "" $2}'|grep '^bash'|awk '{print $2}')

**举例2：给 bash 这个服务对应的进程发送信号1**   
[root@localhost ~]#  killall -i -1 bash
Kill bash(10202) ? (y/N)

### 设置进程优先级(nice)   

进程的优先级相关的只要是 PRI 与 NI 这两个值, 数值越小优先级越高，PRI 是系统内核设置的，用户无法设置，用户只能是指 NI 值来改变进程的优先级   
**进程的优先级=PRI+NI**   
 
**在执行命令时给予新的 NI 值**：   
**语法**：nice –n 数值 命令   
数值的范围从-20~19   

**设置以及启动的进程**   
**语法**：renice 数值 PID   

**举例**：    

```
[root@localhost ~]# nice -n 4 vim &
[1] 11467
[root@localhost ~]# ps -l|grep 'vim'
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
0 T     0 11467 10202  0  81   4 -  2525 finish pts/1    00:00:00 vim

[root@localhost ~]# renice -6 $(ps -l|grep 'vim'|awk '{print $4}')
11467: old priority 4, new priority -6
[root@localhost ~]# ps -l|grep 'vim'
0 T     0 11467 10202  0  71  -6 -  2525 finish pts/1    00:00:00 vim
```

### 查询文件|目录与进程使用关系   

我们经常会遇到这种情况，要删除某个文件或卸载某个文件系统，提示我们正在使用，无法被删除，此时我们就需要找到哪些进程正在使用它们，“杀死“这些进程后我们才能删除这些文件    
**查询文件被哪个进程使用(fuser)**   

**语法**：fuser[-umv] [-ki -signal] file/dir   
**选项与参数**：   
-u:列出 user  
-m:会查询后面文件所在的文件系统   
-v:列出详细命令   
-k:找出使用该文件 PID 并试图发送 SIGKILL 这个信号给这个进程   
-i:与-k 连用，发送信号之前询问用户   

**举例**   
 
```
[root@localhost tmp]# tar -jtv -f ./etc.tar.bz2 > ./newfile.txt 
= >按下 ctrl+z 工作在后台暂停
[1]+  Stopped                 tar -jtv -f ./etc.tar.bz2 > ./newfile.txt
[root@localhost tmp]# fuser -uv ./etc.tar.bz2 
                     USER        PID ACCESS COMMAND
./etc.tar.bz2:       root      11822 f.... (root)bzip2
= >这个文件正在被11822这个进程所使用
```

**查询进程正在使用的文件(lsof)**   

**语法**:lsof [-aUu][+d]   
**选项与参数**：   
-a  ：多项数据需要同时成立才显示出结果时   
-U  ：仅列出 Unixlike 系统的 socket 文件类型；   
-u  ：后面接 username，列出该使用者相关程序所开启的文件；   
+d  ：后面接目录,找出某个目录底下已经被开启的文件   

**举例:查看 tar 对应经常所打开的文件**   

```
[root@localhost tmp]# lsof |grep '^tar'
COMMAND     PID      USER   FD      TYPE     DEVICE SIZE/OFF       NODE NAME
tar       11821      root  cwd       DIR        8,2     4096     745569 /tmp
tar       11821      root  rtd       DIR        8,2     4096          2 /
tar       11821      root  txt       REG        8,2   229652    1199406 /bin/tar
……..
tar       11821      root    2u      CHR      136,1      0t0          3 /dev/pts/1
tar       11821      root    3r     FIFO        0,6      0t0      38124 pipe
```

**查询正在执行进程的 PID**   

**语法**:pidof [-sx] 程序名   
**选项与参数**：   
-s:仅列出一个 PID   
-x:列出程序所对应进程的父进程的 PID   

**举例**：   
[root@localhost tmp]# ps aux |grep $(pidoftar)|grep -v 'grep'
root    11821  0.0  0.0  4880   944 pts/1    T<  13:09   0:00 tar -jtv -f./etc.tar.bz2

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)