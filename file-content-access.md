# Linux 学习记录--文件内容查阅

# 文件内容查阅

**cat:由第一行开始显示文件内容**  
**tac:由最后一行开始显示文件内容**  
**nl:显示的时候，顺便输出行号**  
**more:一页一页的显示文件内容**  
**less:与 more 类似，但是它可以往前翻页**  
**head:只看头几行**  
**tail:只看结尾几行**  
**touch:文件创建与文件时间修改**  

## cat(concatenate) 
 
**语法**：cat [-AbEnTv]  
**选项与参数**：  
-A：相当于-vET 的整合参数  
-b:列出行号，仅针对非空白行做行号显示  
-n:输出行号，空白与非空白都会列出  
-E:将结尾的断行字符￥显示出来  
-v:列出一些看不出的特殊字符  
-T:将 Tab 按键以∧I 显示出来  
 
**举例**：

```
  
[root@localhost tmp]# cat /etc/issue
CentOS release 5.10 (Final)
Kernel \r on an \m

[root@localhost tmp]# cat -n /etc/issue
     1  CentOS release 5.10 (Final)
     2  Kernel \r on an \m
     3
[root@localhost tmp]# cat -A /etc/issue
CentOS release 5.10 (Final)$
Kernel \r on an \m$
$  
```

## 添加行号与打印(nl)   
 
**语法** ：[root@www ~]# nl [-bnw] 文件  
**选项与参数**：  
-b：指定行号指定的方式，主要有两种：  
     -b a ：表示不论是否为空行，也同样列出行号(类似 cat -n)；  
     -b t ：如果有空行，空的那一行不要列出行号(默认值)；  
-n：列出行号表示的方法，主要有三种：  
     -n ln ：行号在萤幕的最左方显示；   
     -n rn ：行号在自己栏位的最右方显示，且不加 0 ；  
     -n rz ：行号在自己栏位的最右方显示，且加 0 ；  
-w：行号栏位的占用的位数。  
 
**举例**

```
  
 [root@www ~]# nl /etc/issue
     1  CentOS release 5.3 (Final)
     2  Kernel \r on an \m

这个文件其实有三行，第三行为空白(没有任何字节)，  
因为他是空白行，所以 nl 不会加上行号喔  

[root@www ~]# nl -b a /etc/issue
     1  CentOS release 5.3 (Final)
     2  Kernel \r on an \m
     3
[root@www ~]# nl -b a -n rz /etc/issue
000001  CentOS release 5.3 (Final)
000002  Kernel \r on an \m
000003
自动在自己栏位的地方补上 0 了～默认栏位是六位数，如果想要改成 3 位数？  

[root@www ~]# nl -b a -n rz -w 3 /etc/issue
001     CentOS release 5.3 (Final)
002     Kernel \r on an \m
003
```
 
**语法**：more|less文件  
 
**More**:  
空白键 (space)：代表向下翻一页；  
Enter        ：代表向下翻『一行』；  
/字串        ：代表在这个显示的内容当中，向下搜寻『字串』这个关键字；  
:f           ：立刻显示出档名以及目前显示的行数；  
q            ：代表立刻离开 more ，不再显示该文件内容。  
b 或 [ctrl]-b ：代表往回翻页，不过这动作只对文件有用，对管线无用。  
 
**Less**:  
空白键    ：向下翻动一页；  
[pagedown]：向下翻动一页；  
[pageup]  ：向上翻动一页；  
/字串     ：向下搜寻『字串』的功能；  
?字串     ：向上搜寻『字串』的功能；  
n        ：重复前一个搜寻 (与 / 或 ? 有关！)  
N        ：反向的重复前一个搜寻 (与 / 或 ? 有关！)  
q        ：离开 less 这个程序；  

**举例**：  

```

[root@localhost tmp]# more /etc/man.config 
#
# Generated automatically from man.conf.in by the
……..
# and to determine the correspondence between extensions and decompressors.
#
# MANBIN                /usr/local/bin/man
#
--More--(31%) 
```
 
## 取出前面几行(head)  
 
**语法**：head [-nnumber] 文件  
**选项与参数**:  
-n:后面接数字，代表行数  
number 默认值是10 当 number 是负数，代表列出前面所有行数但是不包括后面 number 行  
 
## 取出后面几行(tail)  
**语法**：tail [-nnumber] 文件  
**选项与参数**:  
-n:后面接数字，代表行数  
number 默认值是10 当 number 是正数（+ number），代表该文件从 number 以后才会列出来  
 
## 修改文件时间|创建新文件(touch)  
 
**时间属性  
Mtime(modificationtime):当文件内容数据更改时就会更新这个时间，内容数据指的是文件的内容，不包括文件的权限和属性  
Ctime(Statetime):当文件的状态（权限和属性）更改时会更新这个时间  
Atime(accesstime):当文件内容被取用就会修改这个时间  
举例**： 

```
 
[root@localhost ~]# ls -l --time-style=long-iso  /etc/man.config 默认是修改mtime
-rw-r--r-- 1 root root 4617 2012-05-30 20:34 /etc/man.config
[root@localhost ~]# ls -l --time=ctime --time-style=long-iso  /etc/man.config
-rw-r--r-- 1 root root 4617 2014-02-14 10:06 /etc/man.config
[root@localhost ~]# ls -l --time=atime --time-style=long-iso  /etc/man.config
-rw-r--r-- 1 root root 4617 2014-02-21 10:19 /etc/man.config
``` 
 
**语法**：touch[-acdmt] 文件  
**选项与参数**：  
-a:仅修改访问时间 atime  
-c:仅修改文件的时间，若该文件不存在则不创建新文件  
-d:后面可接欲修改的日期，也可以使用—date=”时间或日期”  
-m:仅修改 mtime  
-t:后面可以接欲修改的时间   
 
**主要功能**：  
创建一个空文件  
修改文件日期(mtime,atime)  
**举例**：

```
  
[root@localhost tmp]# cp -a /etc/man.config ./newman.config
[root@localhost tmp]# ls -l --time-style=long-iso  newman.config 指定时间格式
-rw-r--r-- 1 root root 4617 2012-05-30 20:34 newman.config
[root@localhost tmp]# touch -m -t 0709150203  newman.config //只修改mtime
[root@localhost tmp]# ls -l --time-style=long-iso  newman.config
-rw-r--r-- 1 root root 4617 2007-09-15 02:03 newman.config
 [root@localhost tmp]# ls -l --time=atime --time-style=long-iso  newman.config //只修改atime
-rw-r--r-- 1 root root 4617 2014-02-21 10:33 newman.config
[root@localhost tmp]# touch -a -t 0809150203  newman.config 
 [root@localhost tmp]# ls -l --time=atime --time-style=long-iso  newman.config
-rw-r--r-- 1 root root 4617 2008-09-15 02:03 newman.config
[root@localhost tmp]# 
[root@localhost tmp]# touch -d "2 days ago"  newman.config //默认修改atime 与 mtime
[root@localhost tmp]# ls -l --time=atime --time-style=long-iso  newman.config
-rw-r--r-- 1 root root 4617 2014-02-19 10:36 newman.config
[root@localhost tmp]# ls -l  --time-style=long-iso  newman.config
-rw-r--r-- 1 root root 4617 2014-02-19 10:36 newman.config
```

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)