# Linux 学习记录--管道命令

# 管道命令
 
## 选取命令：cut，grep
## 排序命令:sort,wc,uniq
## 双重数据量：tee
## 字符转换命令：tr,expand,col
## 切割命令：split
## 参数代换：xargs
 
管道命令与连续命令不同，连续命令中的各个命令不存在相关性只是顺序执行。  
对于管道命令来说 cmd1|cmd2.   
cmd2需要 cmd1产生的输出流作为 cmd2的输入流,命令之间存在很强的依赖关系，并且管道命令只能处理正确的输出数据流   
## 选取命令
 
### cut
 
**从某一行将一段信息切出来**   
**语法**：cut –d ‘分割字符’  -f field   
             cut –c 字符范围   
**选项与参数**：
-d:后接分割字符与-f 连用    
-f:获取经-d 分割后的第几个字段    
-c:以字符的单位取出固定字符区间，适用于排列正确的信息   
选取范围 a-b 如果是从第 a 个字符到最后可写成 a-  
**说明：cut 可以进行单行与多行分割，对于多行每一行都看做单独的一行分割与获取 field**  
**举例1：单行分割**  

```
[root@localhost ~]# echo $PATH
/usr/kerberos/sbin:/usr/kerberos/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/usr/X11R6/bin:/root/bin
[root@localhost ~]# echo $PATH |cut -d ':' -f 1
/usr/kerberos/sbin
[root@localhost ~]# echo $PATH |cut -d '
/usr/kerberos/sbin:/usr/local/sbin
```

**举例2：多行分割**  

```
[root@localhost ~]# last -5
root     pts/1        :0.0             Wed Mar  5 09:41   still logged in   
root     :0                            Wed Mar  5 09:40   still logged in   
root     :0                            Wed Mar  5 09:40 - 09:40  (00:00)    
reboot   system boot  2.6.18-371.el5   Wed Mar  5 09:20          (05:08)    
root     pts/1        :0.0             Tue Mar  4 15:27 - crash  (17:53)    
[root@localhost ~]# last -5|cut -d ' ' -f 1
root
root
root
reboot
root
```

**举例3：范围选取**  

```
[root@localhost ~]# export
declare -x COLORTERM="gnome-
declare -x DBUS_SESSION_BUS_
declare -x DESKTOP_SESSION="
declare -x DESKTOP_STARTUP_I
declare -x DISPLAY=":0.0"
[root@localhost ~]# export|cut -c 12-
COLORTERM="gnome-terminal"
DBUS_SESSION_BUS_ADDRESS="unix:abstract=/tmp/dbus-OeMZpvhP93,guid=30f2d841bcc5b92980611600531680a3"
DESKTOP_SESSION="default"
DESKTOP_STARTUP_ID=""
DISPLAY=":0.0"
``` 
### grep   
 
分析一行信息，若当中存在我们需要的信息，则将该行输出，grep 后还可接正则表达式或通配符进行查询。    
**语法**：grep [-acinv] [-A] [-B] [--color=auto] ‘查找字符串’ filename    
**选项与参数**：    
-a:将 binary 文件以 text 文件方式查找数据    
-c:计算‘查找字符串’次数    
-i:忽略大小写    
-n:输出行号    
-v:反向选择    
-A:后面可跟数字，代表除了本行外，后续的 n 行也都列出来     
-B: 后面可跟数字，代表除了本行外，前面的 n 行也都列出来      
--color=auto: 关键字部分添加颜色      
**举例**：    

```
[root@localhost ~]# last -3|grep 'root'
root     pts/1        :0.0             Wed Mar  5 09:41   still logged in   
root     :0                            Wed Mar  5 09:40   still logged in   
root     :0                            Wed Mar  5 09:40 - 09:40  (00:00)    
[root@localhost ~]# last -5|grep -vn 'root'
4:reboot   system boot  2.6.18-371.el5   Wed Mar  5 09:20          (05:33)    
6:
7:wtmp begins Fri Feb 14 10:32:51 2014
[root@localhost ~]# last |grep -c 'root'
84
[root@localhost ~]# last -5|grep -n 'roo*' = >通配符查找
1:root     pts/1        :0.0             Wed Mar  5 09:41   still logged in 
2:root     :0                            Wed Mar  5 09:40   still logged in 
3:root     :0                            Wed Mar  5 09:40 - 09:40  (00:00)  
5:root     pts/1        :0.0             Tue Mar  4 15:27 - crash  (17:53)  
```
 
## 排序命令    
 
### sort     
 
sort 可以按照不同的数据类型来排序，例如按数字或文字排序，排序结果也受语系编码的影响，例如有的语系字符是这么排序的 AaBbCc….建议语系使用 LANG=C   
**语法**：sort [-fbMnrtuk]文件或输入流   
**选项与参数**：   
-f:忽略大小写    
-b:忽略最前面的空格    
-M:以月份(英文)来排序   
-r:反向排序    
-t:分隔符与-k 连用    
-u:就是 uniq    
-k:以那个 field 的进行排序     
**举例1**：     

```
[root@localhost ~]# cat /etc/passwd |sort -
avahi:x:70:70:Avahi daemon:/:/sbin/nologin
bin:x:1:1:bin:/bin:/sbin/nologin
cimsrvr:x:100:500:tog-pegasus OpenPegasus WBEM/CIM services:/var/lib/Pegasus:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
```

**举例2：用‘：’分割第三段进行排序**     

```
[root@localhost ~]# cat /etc/passwd |sort -t ':' -k 3
root:x:0:0:root:/root:/bin/bash
cimsrvr:x:100:500:tog-pegasus OpenPegasus WBEM/CIM services:/var/lib/Pegasus:/sbin/nologin
luci:x:101:101::/var/lib/luci:/sbin/nologin
uucp:x:10:14:uucp:/var/spool/uucp:/sbin/nologin
```

### uniq    
 
将重复的数据仅列出一列     
**语法**：uniq [-ic]     
**选项与参数**：   
-i:忽略大小写     
-c:进行计数     
**举例**：     

```
[root@localhost ~]# last|cut -d ' ' -f 1 |sort|uniq -c|sort -n
      1 wtmp
      3 tkf
     26 reboot
     84 root
 => last|cut -d ' ' -f 1 |sort 截取登录名并排序
=> uniq -c 删除重复列,并计数
=>sort –n 按照计数排序
```

### wc  
 
wc 可以帮助我们统计文件字符信息     
**语法**：wc [lwm]    
**选项与参数**：     
-i:仅列出行     
-w:仅列初子       
-m:字符数     
**举例**：     

```
[root@localhost ~]# wc /etc/man.config 
141  722 4617 /etc/man.config
[root@localhost ~]# cat /etc/man.config |wc
    141     722    4617
=>分别代表行数，字数，字符数
```

## 双重数据流(tee)      
 
前面提到输出数据流介质可以是设备或文件，但数据流只能被一个介质全部接收，那么如果希望数据可以被2个介质接收就需要使用双重数据流，简单的说，如果你既想将输出数据流保存到文件也想同时控制台也会显示，那你就需要使用这个了     
**语法：**tee [-a] file     
**选项与参数：**-a:以累加的方式进行添加        
**举例**    
 
```
[root@localhost ~]# export|tee export.list|cut -c 12-
COLORTERM="gnome-terminal"
DBUS_SESSION_BUS_ADDRESS="unix:abstract=/tmp/dbus-OeMZpvhP93,guid=30f2d841bcc5b92980611600531680a3"
DESKTOP_SESSION="default"
[root@localhost ~]# vim export.list
declare -x COLORTERM="gnome-terminal"
declare -x DBUS_SESSION_BUS_ADDRESS="unix:abstract=/tmp/dbus-OeMZpvhP93,guid=30f2d841bcc5b92980611600531680a3"
declare -x DESKTOP_SESSION="default"
```

## 字符转换命令    
 
### tr    
 
tr 可以用来删除和替换一些文字信息      
**说明，tr 只是改变输出内容，并不会真正去修改文件的内容**    
**语法：**tr –d  ‘字符’     
             tr –s  ‘原字符’‘替换字符’     
**选项与参数：**    
-d:删除    
-s:替换     
**举例：**      

```
trfile 文件内容  
abcdefgh
abcdefgh
abcdefgh
[root@localhost ~]# cat trfile |tr -s 'b' 'B'  =>替换
aBcdefgh
aBcdefgh
aBcdefgh
[root@localhost ~]# cat trfile |tr -d 'b'  =>删除
acdefgh
acdefgh
acdefgh

=>操作结束后 trfile 文件内容不会有任何改变
```

### col    
 
col 主要将一些特殊字符进行转换    
 
**语法：**col [-xb]    
**选项与参数：**     
-x:将 tab 键转成相应的空格    
-b:在文字内有反斜杠，仅保留反斜杠后面接的那个字符    
**举例1:**去掉反斜杠(^H)     

```
[root@bogon ~]# man col > /root/col.man
[root@bogon ~]# cat -A /root/col.man|more 
N^HNA^HAM^HME^HE$
     c^Hco^Hol^Hl - filter reverse line feeds from input$

[root@bogon ~]# man col |col -b > /root/col.b.man
[root@bogon ~]# cat -A /root/col.b.man|more 
     col - filter reverse line feeds from input$
```

**举例2:**将 TAB 转换为空格 (^I)        

```
[root@bogon ~]# cat -A /etc/man.config|more # MANPATH^I/opt/*/man$
# MANPATH^I/usr/lib/*/man$
# MANPATH^I/usr/share/*/man$
# MANPATH^I/usr/kerberos/man$

[root@bogon ~]# cat /etc/man.config |col -x > /root/man.tab.config
[root@bogon ~]# cat -A /root/man.tab.config|more
# MANBIN                pathname$
# MANPATH               manpath_element [corresponding_catdir]$
# MANPATH_MAP           path_element    manpath_element$
```

### expand    
 
将[tab]按键转为空格键    
**语法：**expand [–t] file    
**选项与参数：**     
-t:[tab] 按键替换多少个空格字符     
**举例**      

```
[root@localhost ~]# grep '^MANPATH' /etc/man.config |head -n 3|cat -A
MANPATH^I/usr/man$
MANPATH^I/usr/share/man$
MANPATH^I/usr/local/man$
[root@localhost ~]# grep '^MANPATH' /etc/man.config |head -n 3|expand -6|cat -A
MANPATH     /usr/man$
MANPATH     /usr/share/man$
MANPATH     /usr/local/man$
```

## 切割命令(split)    
 
**语法：**split [-bl] file PREFIX    
**选项与参数：**     
-b:后面可接欲切割的文件大小    
-1:以行数进行切割    
PREFIX：切割后文件的前导符     
**举例1：切割文件**     

```
[root@localhost ~]# ll -h /etc/termcap 
-rw-r--r-- 1 root root 789K 2007-01-07 /etc/termcap
[root@localhost ~]# split -b 300k /etc/termcap newter
[root@localhost ~]# ll -h newter*
-rw-r--r-- 1 root root 300K 03-06 09:56 newteraa
-rw-r--r-- 1 root root 300K 03-06 09:56 newterab
-rw-r--r-- 1 root root 189K 03-06 09:56 newterac
```

**举例2 ：合成文件**      

```
[root@localhost ~]# cat newter* >> termap-back
[root@localhost ~]# ll -h termap-back 
-rw-r--r-- 1 root root 789K 03-06 10:02 termap-back
```

## 参数代换(xargs)     
 
**参数代换的作用:**        
1.作为某些指令的参数。比如 which, finger ,find ,whereis 等     
2.作为某些不支持管道命令的输入数据流     
**语法：**xargs [-epn] command   
**选项与参数：**    
-e:就是 EOF 的意思，后面可接一个字符串，当分析到这个字符串时，就会停止继续工作     
-p:在执行每个参数时，都会询问用户    
-n:后面接次数，执行 command 的次数     
**举例1：指令参数**     

```
[root@localhost ~]# cat ./xargsfile 
cd 
ll
grep
[root@localhost ~]# cut -d ' ' -f 1 ./xargsfile|xargs whereis
cd: /usr/share/man/man1p/cd.1p.gz /usr/share/man/man1/cd.1.gz
ll:
grep: /bin/grep /usr/share/man/man1p/grep.1p.gz /usr/share/man/man1/grep.1.gz
```

**举例2：作为输入数据流**      

```
[root@localhost ~]# find /sbin/ -perm +7000|ls -l
总计 227644
-rw------- 1 root root      1377 02-14 10:29 anaconda-ks.cfg
drwxr-xr-x 2 root root      4096 02-21 13:30 Desktop
…….
=>ls 不支持管道命令，一次查询的结果是~/下的内容
[root@localhost ~]# find /sbin/ -perm +7000|xargs ls -l
-rwsr-xr-x 1 root root 73108 10-02 05:10 /sbin/mount.nfs
-rwsr-xr-x 1 root root 73112 10-02 05:10 /sbin/mount.nfs4
…..
=>将 find 查询到的内容作为输入数据流供 ls 使用      
```

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)