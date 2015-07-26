# Linux 学习记录--日志系统

# 日志系统  

日志系统对于一个系统来说是非常重要的，从日志文件我们可以获取到系统的运行状况，协助我们排查问题。  
对于 CentOs 来说，日志系统主要包含2个服务与1个程序     
syslogd:记录系统与网络服务的信息  
klogd:记录内核产生的各项信息  
logrotate:日志文件的轮替功能  
说明：不同的 UNIX LIKE 对应的服务可能不一样     


## syslogd 服务      

### syslogd 服务配置文件分析   

```
[root@localhost ~]# cat /etc/syslog.conf 
#kern.*                                                 /dev/console
*.info;mail.none;news.none;authpriv.none;cron.none              /var/log/messages
authpriv.*                                              /var/log/secure
mail.*                                                  -/var/log/maillog
# Log cron stuff
cron.*                                                  /var/log/cron
*.emerg                                                 *
uucp,news.crit                                          /var/log/spooler
local7.*                                                /var/log/boot.log
news.=crit                                        /var/log/news/news.crit
news.=err                                         /var/log/news/news.err
news.notice                                       /var/log/news/news.notice
```

### 配置文件格式如下  

**【服务类型】 【信息等级设置】【信息存储方式】**   
**以 cron.*  /var/log/cron 为例**   
cron 是服务类型    
. 是信息等级设置   
/var/log/cron 信息存储方式    

**服务类别**    

<table cellpadding="0" cellspacing="0" width="95%"><tbody><tr><td style="background:#0;"><p>服务类别</p></td><td style="background:#0;"><p>说明</p></td></tr><tr><td><p>auth (authpriv)</p></td><td><p>主要与认证有关的机制</p></td></tr><tr><td><p>cron</p></td><td><p>就是例行性工作 cron/at 等产生信息记录的地方；</p></td></tr><tr><td><p>daemon</p></td><td><p>与各个  daemon(服务进程) 有关的信息；</p></td></tr><tr><td><p>kern</p></td><td><p>内核核 (kernel) 产生信息的地方</p></td></tr><tr><td><p>lpr</p></td><td><p>与打印相关的信息</p></td></tr><tr><td><p>mail</p></td><td><p>与邮件相关的信息</p></td></tr><tr><td><p>news</p></td><td><p>与新闻组相关的信息</p></td></tr><tr><td><p>syslog</p></td><td><p> syslogd 程序本身产生的信息</p></td></tr><tr><td><p>user, uucp, local0 ~ local7</p></td><td><p>与 Unix like 机器本身有关的一些信息。</p></td></tr></tbody></table>

**日志信息等级**   

<table cellpadding="0" cellspacing="0" width="95%"><tbody><tr><td style="background:#0;"><p>等级</p></td><td style="background:#0;"><p>等级名称</p></td><td style="background:#0;"><p>说明</p></td></tr><tr><td><p>1</p></td><td><p>info</p></td><td><p>仅是一些基本的信息说明而已；</p></td></tr><tr><td><p>2</p></td><td><p>notice</p></td><td><p>需要注意的信息；</p></td></tr><tr><td><p>3</p></td><td><p>warning<br>(warn)</p></td><td><p>警示的信息， info, notice, warn 这三个信息都是在告知一些基本信息而已，应该还不至于造成一些系统运行困扰；</p></td></tr><tr><td><p>4</p></td><td><p>err <br>(error)</p></td><td><p>一些重大的错误信息 </p></td></tr><tr><td><p>5</p></td><td><p>crit</p></td><td><p>比 error 还要严重的错误</p></td></tr><tr><td><p>6</p></td><td><p>alert</p></td><td><p>警告警告，已经很有问题的等级，比 crit 还要严重</p></td></tr><tr><td><p>7</p></td><td><p>emerg <br>(panic)</p></td><td><p>疼痛等级，意指系统已经几乎要死机的状态</p></td></tr></tbody></table>

 . ：代表比后面还要高的等级 (含该等级) 都被记录下来的意思。如 mail .info  
 .=：代表所需要的等级就是后面接的等级而已  
 .!：代表不等于，除了该等级外的其他等级都记录。  
 .*:代表说所有等级的信息都记录  

## 日志文件的轮替(logrotate)  
日志文件随着时间会变得越来越多，此时就需要进行日志文件轮替。以控制日志文件规模.logrotate 就是做这个用的  

### 日志轮替程序配置文件包括  
/etc/logrotate.conf ：记录整体轮替配置信息  
/etc/logrotate.d/*  : 记录各个服务类型的轮替配置信息  

其实可以在/etc/logrotate.conf 中定义配置各种服务类型的日志轮替配置信息，为了方便管理将每个服务类型的日志轮替配置信息形成独立文件存储在/etc/logrotate.d/*  

```
[root@localhost logrotate.d]# ll
-rw-r--r-- 1 root root  144 2012-02-23 acpid
-rw-r--r-- 1 root root  288 2007-11-12 conman
…….
-rw-r--r-- 1 root root  100 10-02 06:17 wpa_supplicant
-rw-r--r-- 1 root root  100 2012-07-26 yum
```

### logrotate 的配置文件  

```
[root@localhost ~]# vim /etc/logrotate.conf

=>下面为默认值。如不单独配置将才有下面的默认值
weekly    <==默认每周进行一次 rotate 的工作
rotate 4  <==默认保留4个登录文件 
create    <==以新创建文件继续存储日志文件
#compress <==被更动的登录文件是否需要压缩？如果登录文件太大则可考虑此参数启动

include /etc/logrotate.d

/var/log/wtmp {       <==仅针对 /var/log/wtmp 所配置的参数
    monthly           <==每个月一次，取代每周！
    minsize 1M        <==文件容量一定要超过 1M 后才进行
    create 0664 root utmp <==指定新建文件的权限与所属帐号/群组
    rotate 1          <==仅保留一个 
}
```

### 日志轮替规则
当第一次执行轮替，原本日志文件 msg 被重命名为 msg1,同时创建新的 msg 以记录新的日志信息，当的当第二次执行轮替，日志文件 msg1 被重命名为 msg2, msg 重命名为 msg1,同时创建新的 msg 以记录新的日志信息,依次类推，日志文件最多保存的数量为 rotate 这个属性所指定的数值  


### 日志轮替文件语法  
正如前面看到的。日志轮替方式是写在配置文件中的，这里只做简单的说明  

```
[root@localhost logrotate.d]# cat named

/var/log/named.log {
    missingok
create 0644 named named
sharedscripts
    postrotate 
        /sbin/service named reload  2> /dev/null > /dev/null || true
    endscript
}
```

如上前面文件。Logrotate 轮替信息可以分为2部分  
**1.       内部参数**  
**2.       引用外部执行**  

引用外部命令来进行额外的命令下达，这个配置需与 sharedscripts .... endscript 配置合用才行。至于可用的环境为：  
prerotate：在启动 logrotate 之前进行的命令  
postrotate：在做完 logrotate 之后启动的命令  


**内部参数 **  
compress 通过 gzip 压缩转储以后的日志  
nocompress 不需要压缩时，用这个参数  
copytruncate 用于还在打开中的日志文件，把当前日志备份并截断  
nocopytruncate 备份日志文件但是不截断   
create mode owner group 转储文件，使用指定的文件模式创建新的日志文件   
nocreate 不建立新的日志文件   
delaycompress 和 compress 一起使用时，转储的日志文件到下一次转储时才压缩  
nodelaycompress 覆盖 delaycompress 选项，转储同时压缩。  
errors address 专储时的错误信息发送到指定的 Email 地址  
ifempty 即使是空文件也转储，这个是 logrotate 的缺省选项。  
notifempty 如果是空文件的话，不转储  
mail address 把转储的日志文件发送到指定的 E-mail 地址  
nomail 转储时不发送日志文件  
olddir directory 转储后的日志文件放入指定的目录，必须和当前日志文件在同一个文件系统  
noolddir 转储后的日志文件和当前日志文件放在同一个目录下  
prerotate/endscript 在转储以前需要执行的命令可以放入这个对，这两个关键字必须单独成行  
postrotate/endscript 在转储以后需要执行的命令可以放入这个对，这两个关键字必须单独成行  
daily 指定转储周期为每天  
weekly 指定转储周期为每周  
monthly 指定转储周期为每月  
rotate count 指定日志文件删除之前转储的次数，0 指没有备份，5 指保留5 个备份  
tabootext [+] list 让 logrotate 不转储指定扩展名的文件，缺省的扩展名是：.rpm-orig, .rpmsave, v, 和 ~  
size size 当日志文件到达指定的大小时才转储，Size 可以指定 bytes (缺省)以及 K (sizek)或者M (sizem).  

### 执行日志轮替  

**语法：**logrotate [-vf] logfile  
**选项与参数：**  
-v:显示运行过程  
-f:不论是否符合配置文件的数据，强制每个日志文件都进行轮替操作  

**举例**  

```
[root@localhost /]# logrotate -vf /etc/logrotate.conf
………………..
[root@localhost /]# ll /var/log/messages*
-rw------- 1 root root      50 03-28 14:00 /var/log/messages
-rw------- 1 root root      50 03-28 14:00 /var/log/messages.1
-rw------- 1 root root      50 03-28 13:59 /var/log/messages.2
-rw------- 1 root root  333879 03-28 10:44 /var/log/messages.3
-rw------- 1 root root 1289326 03-25 08:50 /var/log/messages.4
```

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)