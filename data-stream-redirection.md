# Linux 学习记录--数据流重定向  

# 数据流重定向  

**数据流可以分为2种：**  
**输入数据流：**以写文件为例，从键盘输入的字符就输入数据流  
**输出数据流：**以读文件为例，将文件内容显示到屏幕上，显示的内容就是输出字符流   
**数量流重定向**就是指改变数据流输入的方式或输出的介质。比如，输入数据流可以是一个文件的内容，输出数据流介质可以是文件而不单单的屏幕   
 
对于命令行来说输入数据流主要来自键盘，输出数据流只要介质是屏幕。   
**同时输出数据流又可分为：**  
  正确输出  
  错误输出  
 
**语法：**   
**输入数据流：**使用<(覆盖)或<<(累加)   
**正确输出数据流：**使用>(覆盖)或>>(累加)   
**错误输出数据流：**使用2>(覆盖)或2>>(累加)   
 
**说明：如果某些信息不想显示到屏幕上也不保存到文件或设备上,可以讲输出数据流指向/dev/null**
 
**举例1：正确输出数据流(覆盖)**   

```
[root@localhost ~]# ll > ll.file
[root@localhost ~]# vim ll.file
总计 225968
-rw------- 1 root root      1377 02-14 10:29 anaconda-ks.cfg
-rw-r--r-- 1 root root       207 03-05 11:00 bashrc-back
……..
```

**举例2：正确输出数据流(累加)**  

```
[root@localhost ~]# ll /root >> ll.file
总计 225968
-rw------- 1 root root      1377 02-14 10:29 anaconda-ks.cfg
-rw-r--r-- 1 root root       207 03-05 11:00 bashrc-back
……..
总计 225972
-rw------- 1 root root      1377 02-14 10:29 anaconda-ks.cfg
-rw-r--r-- 1 root root       207 03-05 11:00 bashrc-back
……..
```

**举例3：正确输出与错误输出数据流**    
  
```
[root@localhost ~]# ll /root /root/error 
ls: /root/error: 没有那个文件或目录 =>错误信息
/root:           =>正确信息
总计 225972
-rw------- 1 root root      1377 02-14 10:29 anaconda-ks.cfg
-rw-r--r-- 1 root root       207 03-05 11:00 bashrc-back
………………..
[root@localhost ~]# ll /root /root/error >right.list 2>error.list 
[root@localhost ~]# cat right.list
/root:
总计 225984
-rw------- 1 root root      1377 02-14 10:29 anaconda-ks.cfg
-rw-r--r-- 1 root root       207 03-05 11:00 bashrc-back
……………..
[root@localhost ~]# cat error.list
ls: /root/error: 没有那个文件或目录
```

**举例4：正确与错误输出数据流写在一个文件中**   

```
[root@localhost ~]# ll /root /root/error >all.list 2>&1 
[root@localhost ~]# cat all.list
ls: /root/error: 没有那个文件或目录   
/root:  
总计 225996  
-rw-r--r-- 1 root root        45 03-05 13:02 all.list
-rw------- 1 root root      1377 02-14 10:29 anaconda-ks.cfg
………………..
``` 
 
## 命令执行的判断依据(; && ||) 
 
**语法：**  
**cmd;cmd:**不考虑命令相关性连续额的命令执行   
**cmd1&& cmd2:**若 cmd1执行完毕且正确，则执行 cmd2  
            若 cmd1执行错误则不执行 cmd2   
**cmd1|| cmd2：**若 cmd1执行完毕且正确，则不执行 cmd2  
            若 cmd1执行完毕且为错误，则执行 cmd2   
 
本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)