# Linux 学习记录--文件|目录的默认权限与隐藏权限

# 文件|目录的默认权限与隐藏权限

当我们创建一个文件或者目录时即使我们未对其非配权限，其也会存在默认权限
 
```

[root@localhost tmp]# mkdir newdir
[root@localhost tmp]# ls -dl newdir
drwxr-xr-x 2 root root 4096 02-21 11:10 newdir
[root@localhost tmp]# touch newfile
[root@localhost tmp]# ll newfile 
-rw-r--r-- 1 root root 0 02-21 11:11 newfile
[root@localhost tmp]# 
[root@localhost tmp]# 
```
  
对于文件来说默认的权限是 rw-r--r—   
对于目录来说默认的权限是 rwxr-xr-x    
 
**语法**：   
**查看默认权限**：umask [-S]    
**选项与参数**：-S 以符号形式显示   
 
**设置默认权限**：umask 权限数    
 
**说明**：对于目录来说最大权限是777(rwxrwxrwx)   
      对于文件来说最大权限是666(rw-rw-rw-)    
 
当权限数为022时代表：目录权限(777-022)=755(rwxr-xr-x)            
当权限数为022时代表：目录权限(666-022)=644(rw-r--r—)         
  
**举例：查看默认权限** 

```
                               
[ro[root@localhost tmp]# umask
0022
[root@localhost tmp]# umask -S
u=rwx,g=rx,o=rx
```

**举例：设置默认权限**

```

[root@localhost tmp]# umask 011
[root@localhost tmp]# mkdir newdir1
[root@localhost tmp]# ls -dl newdir1
drwxrw-rw- 2 root root 4096 02-21 13:09 newdir1
[root@localhost tmp]# touch newfile1
[root@localhost tmp]# ll newfile1 
-rw-rw-rw- 1 root root 0 02-21 13:10 newfile1 
```

## 文件隐藏属性(chattr|lsattr)               
 
### 设置文件属性              
 
**语法**：chattr [+-=][ASacdistu]文件或目录名称                   
**选项与参数**：                         
+：增加某一个特殊参数                    
-：删除某一个特殊参数  
=：仅有后面接的参数   
 
A:当配置了 A 这个属性时，若你有存取此文件(或目录)时，他的存取时间 atime 将不会被修改，可避免 I/O 较慢的机器过度的存取磁碟。这对速度较慢的计算机有帮助  
S：一般文件是非同步写入磁碟的(原理请参考第五章 sync 的说明)，如果加上 S 这个属性时，当你进行任何文件的修改，该更动会[同步]写入磁碟中。  
a：当配置 a 之后，这个文件将只能添加数据，而不能删除也不能修改数据，只有 root 才能配置这个属性。   
c：这个属性配置之后，将会自动的将此文件『压缩』，在读取的时候将会自动解压缩， 但是在储存的时候，将会先进行压缩后再储存(看来对於大文件似乎蛮有用的！)  
d：当 dump 程序被运行的时候，配置 d 属性将可使该文件(或目录)不会被 dump 备份  
i：可以让一个文件[不能被删除、改名、配置连结也无法  写入或新增数据！]对与系统安全性有相当大的助益！只有 root 能配置此属性  
s：当文件配置了 s 属性时，如果这个文件被删除，他将会被完全的移除出这个硬盘空间，所以如果误删了，完全无法救回来了喔！  
u：与 s 相反的，当使用 u 来配置文件时，如果该文件被删除了，则数据内容其实还存在磁碟中，可以使用来救援该文件  
   
**举例**：  

```

[root@localhost tmp]# nano
[root@localhost tmp]# ll testa 
-rw-r--r-- 1 root root 5 02-21 13:24 testa
[root@localhost tmp]# chattr +a testa 
[root@localhost tmp]# nano testa  //此处修改不允许保存
[root@localhost tmp]# chattr =i testa 
[root@localhost tmp]# rm testa 
rm：是否删除有写保护的 一般文件 “testa”? y  
rm: 无法删除 “testa”: 不允许的操作  
```

### 查看文件属性  
 
**语法**：lsattr [-adR] 文件或目录  
**选项与参数**：  
-a：将隐藏文件列出来  
-d:如果接的是目录，仅列出目录本身属性而非目录内的文件名  
-R：连同子目录的数据也一并列出来  
 
### 查看文件类型  
**语法**：file 文件 

```
 
[root@localhost tmp]# file ~/.bashrc   
/root/.bashrc: ASCII text
```  

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)