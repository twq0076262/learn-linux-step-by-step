# Linux 学习记录--服务

# 服务

常驻在内存中的进程，且提供一些系统功能，就是服务。这个进程称为 daemon.换另外一种说法：服务包括一个提供系统功能的程序以及一个执行该程序的进程       
每个服务对应设备的一个端口       

## 服务主要分类   

**按照服务的启动方式可以分为2类:**    
**自启动的服务：**大部分为开机就会启动的服务。每一个服务都有一个进程进行控制     
**统一控制启动服务:**由一个独立进程负责启动这些服务，至于何时启动由用户进行控制。这个独立的进程就是 xinetd

**统一控制启动服务也是一个自启动服务，只是其控制的服务不一定开机就启动**    

### 几个重要的目录
/etc/init.d/*:所有服务启动脚本存放处(学习 shell script 语法好去处)      
/etc/sysconfig/*(各服务的初始化环境配置文件)    
/etc/xined.conf**统一控制启动服务总体配置文件**    
/etc/xined.d/*  **统一控制启动服务配置文件（每个服务的配置文件）**   
/etc/*:自启动服务各自的配置文件  
/var/lib/*自启动服务各自的配置文件   
/var/run/*:各个服务的程序的 PID 记录处     

以自启动服务 syslogd 为例     
[root@localhost~]# ll /etc/sysconfig/syslog /etc/init.d/syslog /etc/syslog.conf  
-rwxr-xr-x1 root root 2043 2010-04-03 /etc/init.d/syslog =>记录程序文件  
-rw-r--r--1 root root610 2010-04-03/etc/sysconfig/syslog =>记录初始化信息   
-rw-r--r--1 root root938 02-14 10:10/etc/syslog.conf =>记录配置信息   


## 自启动服务的操作
自启动服务在系统启动的时候可能会启动(需要配置)，当然我们也可以控制它的启动和停止以及以下其他操作。   

### 直接执行服务脚本
前面说到所有服务的启动脚本都存放在/etc/init.d/* ，我们就以 syslog 服务为例   

**syslog 服务对应的 shellscript**    

```
case "$1" in
  start)
        start
        ;;
  stop)
        stop
        ;;
  status)
        rhstatus
        ;;
  restart)
        restart
        ;;
  reload)
        reload
        ;;
  condrestart)
        [ -f /var/lock/subsys/syslog ] && restart || :
        ;;
  *)
        echo $"Usage: $0 {start|stop|status|restart|condrestart}"
        exit 2
esac
```

通过以上可以粗略的看到这里包含6个方法(start,stop,rhstatus….) 调用这些方法的条件是 执行 shell script 后面跟的参数（start|stop|status|restart|condrestart）    
通过以上分析，如果我们要知道一个服务有哪些操作，可以之间查看这个服务的脚本文件     

```
[root@localhost init.d]# ./syslog status
syslogd (pid  3637) 正在运行...
klogd (pid  3640) 正在运行...
[root@localhost init.d]# ./syslog restart
关闭内核日志记录器：                                       [确定]
关闭系统日志记录器：                                       [确定]
启动系统日志记录器：                                       [确定]
启动内核日志记录器：                                       [确定]
```

### 通过 service 指令执行服务   

**语法：**service[服务名称] 执行操作    
    service --status-all    

**选项与参数：**    
执行操作:服务需要进行的工作（start|stop|status|restart….）    
--status-all:将系统所有自启动服务显示   

**举例：**    

```
[root@localhost ~]# service syslog restart
关闭内核日志记录器：                                       [确定]
关闭系统日志记录器：                                       [确定]
启动系统日志记录器：                                       [确定]
启动内核日志记录器：                                       [确定]
[root@localhost ~]# service --status-all
acpid (pid 3901) 正在运行...
anacron 已停
atd (pid 4240) 正在运行...
auditd (pid  3609) 正在运行...
…….
```

## 统一控***务的操作   

前面提到统一控***务是由一个特殊的进程(xinetd)来控制其他服务的行为   

### 整体配置文件   
如果针对个体服务配置文件未配置下面项目，那么服务的设置值将去下面内容作为默认值  

```
[root@localhost etc]# vim /etc/xinetd.conf
defaults
{
# 服务启动成功或失败，以及相关登陆行为的记录文件
        log_type        = SYSLOG daemon info  
        log_on_failure  = HOST  
        log_on_success  = PID HOST DURATION EXIT 
# 允许或限制联机的默认值
        cps         = 50 10  
        instances   = 50    
        per_source  = 10   
# 网络 (network) 相关的默认值
        v6only         
# 环境参数的配置
        groups         
        umask         
}
```

### 服务配置文件分析  

**举例：rsync 是统一控***务中的一个，下面是这个服务的以下配置**  

```
[root@localhost etc]# vim /etc/xinetd.d/rsync
# default: off
# description: The rsync server is a good addition to an ftp server, as it \
#       allows crc checksumming etc.
service rsync
{
        disable = yes
        socket_type     = stream
        wait            = no
        user            = root
        server          = /usr/bin/rsync
        server_args     = --daemon
        log_on_failure  += USERID
}
```

### 配置文件标识说明  

= ： 表示后面的配置参数就是这样  
+= ： 表示后面的配置为在原来的配置里头加入新的参数  
-= ： 表示后面的配置为在原来的参数舍弃这里输入的参数  

**以下图表来自鸟哥私房菜**  

<table cellpadding="0" cellspacing="0" width="99%"><tbody><tr><td style="background:#0;"><p>attribute (功能)</p></td><td style="background:#0;"><p>说明与范例</p></td></tr><tr><td colspan="2" style="background:#0;" width="562"><p>一般配置项目：服务的识别、启动与程序</p></td></tr><tr><td><p>disable<br>(启动与否)</p></td><td><p>配置值：[yes|no]，默认 disable = yes</p><p>，此值可配置该服务是否要启动若要启动就得要配置为[ disable = no ]</p></td></tr><tr><td><p>id<br>(服务识别)</p></td><td><p>配置值：[服务的名称]</p><p>虽然服务在配置文件开头[ service 服务名称]已经指定了，不过有时后会有重复的配置值，此时可以用 id 来取代服务名称。</p></td></tr><tr><td><p>server<br>(程序文件名)</p></td><td><p>配置值：[程序的绝对路径名]</p><p>这个就是指出这个服务的启动程序.例如 /usr/bin/rsync 为启动 rsync 服务的命令，所以这个配置值就会成为： [ server = /usr/bin/rsync ]</p></td></tr><tr><td><p>server_args<br>(程序参数)</p></td><td><p>配置值：[程序相关的参数]</p><p>这里应该输入的就是你的 server 那里需要输入的一些参数.例如 rsync 需要加入 --daemon ， 所以这里就配置：[ server_args = --daemon ]。与上面 server 搭配，最终启动服务的方式[/usr/bin/rsync --daemon]</p></td></tr><tr><td><p>user<br>(服务所属UID)</p></td><td><p>配置值：[使用者账号]</p><p>如果 xinetd 是以 root 的身份启动来管理的，那么这个项目可以配置为其他用户。此时这个 daemon 将会以此配置值指定的身份来启动该服务的程序。举例来说，你启动 rsync 时会以这个配置值作为该程序的 UID。</p></td></tr><tr><td><p>group</p></td><td><p>跟 user 的意思相同.此项目填入组名即可。</p></td></tr><tr><td colspan="2" style="background:#0;" width="562"><p>一般配置项目：联机方式与联机封包协议</p></td></tr><tr><td><p>socket_type<br>(封包类型)</p></td><td><p>配置值：[stream|dgram|raw]，与封包有关</p><p>stream 为联机机制较为可靠的 TCP 封包，若为 UDP 封包则使用 dgram 机制。raw 代表 server 需要与 IP 直接对谈.举例来说 rsync 使用 TCP ，故配置为[socket_type = stream ]</p></td></tr><tr><td><p>protocol<br>(封包类型)</p></td><td><p>配置值：[tcp|udp]，通常使用 socket_type 取代此配置</p><p>使用的网络协议，需参考 /etc/protocols 内的通讯协议，一般使用 tcp 或 udp。由于与 socket_type 重复， 因此这个项目可以不指定。</p></td></tr><tr><td><p>wait<br>(联机机制)</p></td><td><p>配置值：[yes(single)|no(multi)]，默认 wait = no</p><p>这就是我们刚刚提到的 Multi-threaded 与 single-threaded .一般来说，我们希望大家的要求都可以同时被激活，所以可以配置[ wait = no ] 此外，一般 udp 配置为 yes 而 tcp 配置为 no。</p></td></tr><tr><td><p>instances<br>(最大联机数)</p></td><td><p>配置值：[数字或 UNLIMITED]</p><p>这个服务可接受的最大联机数量。如果你只想要开放 30 个人联机 rsync 时，可在配置文件内加入：[ instances = 30 ]</p></td></tr><tr><td><p>per_source<br>(单一用户来源)</p></td><td><p>配置值：[一个数字或 UNLIMITED]</p><p>如果想要控制每个来源 IP 仅能有一个最大的同时联机数，就指定这个项目吧.例如同一个 IP 最多只能连 10 条联机[ per_source = 10 ]</p></td></tr><tr><td><p>cps<br>(新联机限制)</p></td><td><p>配置值：[两个数字]</p><p>为了避免短时间内大量的联机要求导致系统出现忙碌的状态而有这个 cps 的配置值。第一个数字为一秒内能够接受的最多新联机要求， 第二个数字则为，若超过第一个数字那暂时关闭该服务的秒数。</p></td></tr><tr><td colspan="2" style="background:#0;" width="562"><p>一般配置项目：登录文件的记录</p></td></tr><tr><td><p>log_type<br>(登录档类型)</p></td><td><p>配置值：[登录项目 等级]</p><p>当数据记录时，以什么登录项目记载？且需要记载的等级为何(默认为 info 等级)。</p></td></tr><tr><td><p>log_on_success<br>log_on_failure<br>(登录状态)</p></td><td><p>配置值：[PID,HOST,USERID,EXIT,DURATION]</p><p>在[成功登陆]或[失败登陆]之后，需要记录的项目：PID 为纪录该 server 启动时候的 process ID ， HOST 为远程主机的 IP、USERID 为登陆者的账号、EXIT 为离开的时候记录的项目、DURATION 为该用户使用此服务多久？</p></td></tr><tr><td colspan="2" style="background:#0;" width="562"><p>进阶配置项目：环境、网络端口口与联机机制等</p></td></tr><tr><td><p>env<br>(额外变量配置)</p></td><td><p>配置值：[变量名称=变量内容]</p><p>这一个项目可以让你配置环境变量</p></td></tr><tr><td><p>port<br>(非正规埠号)</p></td><td><p>配置值：[一组数字(小于 65534)]</p><p>这里可以配置不同的服务与对应的 port ，但是请记住你的 port 与服务名称必须与 /etc/services 内记载的相同才行.不过，若服务名称是你自定义的，那么这个 port 就可以随你指定</p></td></tr><tr><td><p>redirect<br>(服务转址)</p></td><td><p>配置值：[IP port]</p><p>将 client 端对我们 server 的要求，转到另一部主机上去. 例如当有人要使用你的 ftp 时，你可以将他转到另一部机器上面去.那个 IP_Address 就代表另一部远程主机的 IP .</p></td></tr><tr><td><p>includedir<br>(呼叫外部配置)</p></td><td><p>配置值：[目录名称]</p><p>表示将某个目录底下的所有文件都给他塞进来</p></td></tr><tr><td colspan="2" style="background:#0;" width="562"><p>安全控管项目：</p></td></tr><tr><td><p>bind<br>(服务接口锁定)</p></td><td><p>配置值：[IP]</p><p>这个是配置[允许使用此一服务的适配卡]的意思.举个例子来说，你的 Linux 主机上面有两个 IP ，而你只想要让 IP1 可以使用此一服务，但 IP2 不能使用此服务，这里就可以将 IP1 写入即可.那么 IP2 就不可以使用此一 server</p></td></tr><tr><td><p>interface</p></td><td><p>配置值：[IP]</p><p>与 bind 相同</p></td></tr><tr><td><p>only_from<br>(防火墙机制)</p></td><td><p>配置值：[0.0.0.0, 192.168.1.0/24, hostname, domainname]</p><p>这东西用在安全机制上面，也就是管制[只有这里面规定的 IP 或者是主机名可以登陆.] </p></td></tr><tr><td><p>no_access<br>(防火墙机制)</p></td><td><p>配置值：[0.0.0.0, 192.168.1.0/24, hostname, domainname]</p><p>跟 only_from 差不多.就是用来管理可否进入你的 Linux 主机激活你的 server 服务的管理项目. no_access 表示[不可登陆]的 PC 啰.</p></td></tr><tr><td><p>access_times<br>(时间控管)</p></td><td><p>配置值：[00:00-12:00, HH:MM-HH:MM]</p><p>这个项目在配置[该服务 server 启动的时间]，使用的是 24 小时的配置.例如你的 ftp 要在 8 点到 16 点开放的话，就是： 08:00-16:00。</p></td></tr><tr><td><p>umask</p></td><td><p>配置值：[000, 777, 022]</p><p>可以配置用户创建目录或者是文件时候的属性.系统建议值是 022 。</p></td></tr></tbody></table>

### 通过统一控***务启动一个服务  

**1.       将服务设置为启动**  

```
[root@localhost xinetd.d]# cat /etc/xinetd.d/rsync|sed 's/yes/no/g' >/etc/xinetd.d/tmp
[root@localhost xinetd.d]# vim tmp
[root@localhost xinetd.d]# cat ./tmp >./rsync 
[root@localhost xinetd.d]# cat ./rsync # default: off

# description: The rsync server is a good addition to an ftp server, as it \
#       allows crc checksumming etc.
service rsync
{
        disable         = no
        socket_type     = stream
        wait            = no
        user            = root
        server          = /usr/bin/rsync
        server_args     = --daemon
        log_on_failure  += USERID
}
```

**2.       重新启动统一控***务进程，以重启我们刚才更改的这个服务**  

```
[root@localhost etc]# service xinetd restart
停止 xinetd：                                              [确定]
启动 xinetd：                                              [确定]
```

**3.       查看端口判断服务是否启用成功**  

```
[root@localhost xinetd.d]# cat /etc/services |grep 'rsync'
rsync           873/tcp                         # rsync
rsync           873/udp                         # rsync
root@localhost xinetd.d]# netstat -tnlp|grep 873
tcp        0      0 0.0.0.0:873                 0.0.0.0:*                   LISTEN      10301/xinetd  
```
  
**说明：所有服务端口在/etc/services 可以查看到**  

## 设置服务开机启动

前面说到部分自启动服务会开机启动，通过 xinetd 进程控制的统一控***务也可以通过更改服务配置文件中disable=no,也可以控制其启动，  
那么通过什么配置让我们可以选择哪些服务开机就启动，哪些服务开机时不启动  

**语法：**chkconfig--list  
    chkconfig [--level [0123456]] 服务名称 [on|off]  

**参数与选项：**  
--list：查看所有服务开机启动情况  
--level:启动级别（就是 init 后面那个数字，3为命令行模式，5为图形界面模式）  

**举例1：查看所有服务开机启动情况**  

```
[root@localhost xinetd.d]# chkconfig --list
NetworkManager  0:关闭  1:关闭  2:关闭  3:关闭  4:关闭  5:关闭  6:关闭
acpid           0:关闭  1:关闭  2:启用  3:启用  4:启用  5:启用  6:关闭
anacron         0:关闭  1:关闭  2:启用  3:启用  4:启用  5:启用  6:关闭

基于 xinetd 的服务：
        chargen-dgram:  关闭
        chargen-stream: 关闭
        daytime-dgram:  关闭
tftp:           启用
=>可以看出 xinetd 进程控制的服务通过更改 disable=no 也是可以设置开机启动的
```

**举例2：设置服务开机启动**  

```
[root@localhost xinetd.d]# chkconfig --level 345 NetworkManager on
[root@localhost xinetd.d]# chkconfig --list
NetworkManager  0:关闭  1:关闭  2:关闭  3:启用  4:启用  5:启用  6:关闭
```

总结：1.对于自启动的服务来说，通过 chkconfig[--level [0123456]]可设置是否开机启动       
     2.对于统一控制的服务，可以通过更改服务配置文件 disable=no 来设置开启启动    

## 如何制作自己的服务  


**语法：**chkconfig[--add|--del] 服务名称  
**选项与参数：**  
--add:添加一个服务到服务管理器  
--del:删除一个服务从服务管理器  

**步骤1：创建一个程序，提供某种功能**  
**说明：此程序执行文件必须在/etc/init.d/目录下**  

```
#!/bin/bash
# chkconfig: 35 80 70
# description:hello

echo "这是我的 script. 参数是 $1"
=> chkconfig: 35 80 70其中，35指的是启动级别，80指的是启动顺序，70指的是结束顺序(因为服务启动与结束是有依赖关系的因此需要设置启动结束顺序)
=> description 添加服务描述信息

[root@localhost init.d]# ll myscript 
-rwxrwxrwx 1 root root 97 03-20 16:08 myscript
```

**步骤2：添加服务到服务管理器**  

```
[root@localhost init.d]# chkconfig --add myscript 
[root@localhost init.d]# chkconfig --list myscript 
myscript        0:关闭  1:关闭  2:关闭  3:启用  4:关闭  5:启用  6:关闭
```

**开机显示效果**  

![](images/1.png)

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)