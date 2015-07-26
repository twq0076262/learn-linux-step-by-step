# Linux 学习记录--shell script

# shell script

shell script 是利用 shell 的功能所写的一个程序，这个程序使用纯文本文件，将一些 shell 的语法和命令写在里面，搭配正则表达式，管道命令与数据流重定向等功能，达到我们想要的目的

## shell script 执行  

**直接命令执行**   
shell script 文件必须具备 rx 的权限，假设 my.sh 在 /root 下    
**绝对路径**   
[root@bogon ~]# /root/my.sh   
**相对路径**   
[root@bogon ~]# . /my.sh   
**bash(sh)命令执行**   
shell script 文件必须具备 r 的权限    
root@bogon ~]# sh my.sh    

**source 或.命令执行**   
source 或. 命令执行与上面执行不同，上面执行 shell 都会分配一个子进程来进行，source 或. 命令就在本进程执行，也就是说一些非环境变量也能读取到   
root@bogon ~]# source my.sh   

## shell script 编写    

前面提到 shell script 中可以使用 shell 命令，那么 shell script 格式是怎样的？    

shellscript 文件 my.sh   

```
#!/bin/bash
#This is my first shell script
echo "hello world"
```

第1行：必须要以#!/bin/bash 开头代表这个文件内的语法使用 bash 的语法   
第2行：注释以#开头   
第3行：shell 命令    


## shell script 重要功能   
**test测试命令**   
用来检测系统上面某些文件或者相关的属性   

|测试的标志 |代表意义|
|:---------|:------|
|1. 关于某个文件名的[文件类型]判断，如 test -e  filename 表示存在否|
|-e   | 该[文件名]是否存在？(常用)|
|-f   | 该[文件名]是否存在且为文件(file)？(常用)|
|-d   | 该[文件名]是否存在且为目录(directory)？(常用)|
|-b   |该[文件名]是否存在且为一个 block device 装置？|
|-c   | 该[文件名]是否存在且为一个 character device 装置？|
|-S   | 该[文件名]是否存在且为一个 Socket 文件？|
|-p   | 该[文件名]是否存在且为一个 FIFO (pipe) 文件？|
|-L   |该[文件名]是否存在且为一个连结档？|

|2. 关于文件的权限侦测，如 test -r filename 表示可读否 (但 root 权限常有例外)|
|:---------------------------------|:-----------------------------------|
|-r |侦测该文件名是否存在且具有[可读]的权限？|
|-w |侦测该文件名是否存在且具有[可写]的权限？|
|-x |侦测该文件名是否存在且具有[可运行]的权限？|
|-u |侦测该文件名是否存在且具有[SUID]的属性？|
|-g |侦测该文件名是否存在且具有[SGID]的属性？|
|-k |侦测该文件名是否存在且具有[Sticky bit]的属性？|
|-s |侦测该文件名是否存在且为[非空白文件]？|

|3. 两个文件之间的比较，如： test file1 -nt file2|
|:----------------------|:----------------------|
|-nt |(newer than)判断 file1 是否比 file2 新|
|-ot |(older than)判断 file1 是否比 file2 旧|
|-ef |判断 file1 与 file2 是否为同一文件，可用在判断 hard link 的判定上。 主要意义在判定，两个文件是否均指向同一个  inode 哩！|

|4. 关于两个整数之间的判定，例如 test n1 -eq n2|
|:-------------|:--------------------------|
|-eq |两数值相等 (equal)|
|-ne |两数值不等 (not equal)|
|-gt |n1 大于 n2  (greater than)|
|-lt |n1 小于 n2 (less  than)|
|-ge |n1 大于等于 n2 (greater  than or equal)|
|-le |n1 小于等于 n2  (less than or equal)|

|5. 判定字串的数据|
|:----------------|:--------------------|
|test -z string    |判定字串是否为 0 ？若 string 为空字串，则为 true|
|test -n string    |判定字串是否非为 0 ？若 string 为空字串，则为 false。注： -n 亦可省略|
|test str1 = str2  |判定 str1 是否等于 str2 ，若相等，则回传 true|
|test str1 != str2 |判定 str1 是否不等于 str2 ，若相等，则回传 false|

|6. 多重条件判定，例如： test -r filename -a -x filename|
|:-------------------|:----------------------|
|-a |(and)两种情况同时成立！例如  test -r file -a -x file，则 file 同时具有 r 与 x 权限时，才回传  true。|
|-o| (or)两种情况任何一个成立！例如 test -r file -o -x file，则 file 具有 r 或 x 权限时，就可回传  true。|
|! |反相状态，如 test ! -x file ，当 file 不具有 x 时，回传 true|

**举例**   
**shellscript 文件内容**：   

```
#!bin/bash
read -p "please input file/dir name: " name
echo "your input is $name"
test ! -e $name && echo "file is not exist! "&&exit 1
test -d $name &&echo "file is directory"
test -f $name &&echo "file is regulate file"
test -r $name &&echo "you can read this file"
test -w $name &&echo "you can write this file"
test -x $name &&echo "you can exec this file"
exit 0
```

**执行结果**   

```
[root@localhost scripts]# sh test.sh
please input file/dir name: n
your input is n
file is not exist!
[root@localhost scripts]# sh test.sh
please input file/dir name: sh01.sh
your input is sh01.sh
file is regulate file
you can read this file
you can write this file
you can exec this file
[root@localhost scripts]# ll sh01.sh
-rwxr--r-- 1 root root 48 03-07 10:15 sh01.sh
```

测试命令   
[]测试命令和 test 用法一直，只是使用时要注意空格符使用   
 在中括号内的每个组件又要有空格符分割   
 在中括号内的变量常量都要使用“”括起来   

**举例**  
**shellscript 文件内容**：   

```
#!/bin/bash
read -p "please input y/n: " yn
[ -z "$yn" ] &&echo "your input is empty"&&exit 1
[ "$yn" == "y" -o "$yn" == "Y" ]&&echo "it is ok"&&exit 0
[ "$yn" == "n" -o "$yn" == "N" ]&&echo "it is not ok"&&exit 0
echo "you input is $yn .please use input (y/n)"
exit 2
```

**执行结果**   

```
[root@localhost scripts]# sh [].sh
please input y/n:
your input is empty
[root@localhost scripts]# sh [].sh
please input y/n: Y
it is ok
[root@localhost scripts]# sh [].sh
please input y/n: A
you input is A .please use input (y/n)
```

**执行参数**   
可以在执行 shell script 时添加参数   
sh var.sh first second third   
      $1   $2   $3    
其中 first 作为第一个参数存储在$1中，依次类推。   

**几个重要的参数**：   
$#：获取参数的数量   
$@：获取参数的整体内容    

**举例**   
**shellscript 文件内容**：   

```
#!/bin/bash
echo "total parameter number is : $#"
echo "whole parameter is : $@"
echo "1st  parameter is : $1"
echo "2st  parameter is : $2"
```

**执行结果**   

```
[root@localhost scripts]# sh var.sh first second third
total parameter number is : 3
whole parameter is : first second third
1st  parameter is : first
2st  parameter is : second
```

**if…..then**   
**语法：**     
**If [条件判断式 ] ;then**    
 **待执行的 shell 命令**  
**fi**  

**举例**  
**shellscript 文件内容**：  

```
#!/bin/bash
read -p "please input y/n: " yn
if [ -z "$yn" ];then
echo "your input is empty"
exit 1
fi
if [ "$yn" == "y" -o "$yn" == "Y" ];then
echo "it is ok"
exit 0
fi
if [ "$yn" == "n" -o "$yn" == "N" ];then
echo "it is not ok"
exit 0
fi
exit 2
```

**执行结果**  

```
[root@localhost scripts]# sh ifthen.sh
please input y/n:
your input is empty
[root@localhost scripts]# sh ifthen.sh
please input y/n: Y
it is ok
[root@localhost scripts]# sh ifthen.sh
please input y/n: N
it is not ok
if …else
```

**语法**：  
**If [条件判断式 ] ;then**  
 待执行的 shell 命令  
**else**  
 待执行的 shell 命令  
**fi**  
**if …elif..else**  
**语法**：  
**If [条件判断式 ] ;then**  
待执行的 shell 命令  
**elif[ 条件判断式 ] ;then**  
待执行的 shell 命令  
**else**  
待执行的 shell 命令  
**fi**  

**case …esac**  
**语法**：  
**case$变量 in**  
**“第一个变量内容”)**  
待执行的 shell 命令  
;;  
**“第 n 个变量内容”)**  
待执行的 shell 命令  
;;  
***) è 最后一个变量内容. *代表所有**  
待执行的 shell 命令  
;;  
**esac**  

**举例**  
**shellscript 文件内容**：  

```
#!/bin/bash
case $1 in
"hello")
echo " hello how are you!"
;;
"")
echo "you must input parameter!"
;;
*)
echo "parameter is hello,yours is $1"
;;
esac
```

**执行结果**  

```
[root@localhost scripts]# sh case.sh
you must input parameter!
[root@localhost scripts]# sh case.sh hello
hello how are you!
[root@localhost scripts]# sh case.sh he
parameter is hello,yours is he
```
**函数功能**  
**语法**：   
**functionfname()**  
**{**  
程序段  
**}**  

**举例**  
**shellscript 文件内容**：  

```
#!/bin/bash
function print()
{
if [ -z "$1" ];then
  echo "你没有输入参数"
else
  echo -n "你输入的是 $1"
fi
}
function returnval()
{
  return 3
}
case $1 in
"one")
print 1
;;
"return")
returnval
echo "return value is $?"
;;
"nopara")
print
;;
esac
```

**执行结果**  

```
[root@localhost scripts]# sh function.sh one
你输入的是 1
[root@localhost scripts]# sh function.sh nopara
你没有输入参数
[root@localhost scripts]# sh function.sh return
return value is 3
```

函数后面也可以添加参数$0记录函数名，$1记录参数1，依次类推  
函数也可以具有返回值，返回值内容通过$?查看  
**while do done**  
**语法**：  
**while[ 条件表达式 ] =>只要条件满足就执行**  
**do**  
程序段  
**done**  

**举例**  
**shellscript 文件内容**：  

```
declare -i sum=0;
declare -i num=0
while [ $num -le 100 ]
do
sum=sum+num
num=num+1
done
echo "总和是： $sum"
```

**执行结果**  

```
[root@localhost scripts]# sh while.sh
总和是： 5050 
```

**until do done**  

**语法**：  
**while[ 条件表达式 ] =>只要条件不满足就执行**  
**do**  
程序段  
**done**  
**for do done**  
**语法**：  
**for varin con1 con2….**  
**for varin conarr**  
**for (初始值;限制值;执行步长)**  

**举例**  
**shellscript 文件内容**：  

```
#!/bin/bash
userarr=$(cat /etc/passwd | cut -d ':' -f 1|head -n 5)
for user in $userarr
do
echo "user is : $user"
done
for char in A B C
do
echo "char is $char"
done
sum=0
for ((i=1; i<=$1; i++))
do
sum=$(($sum+$i))
done
echo "1+2..+$1=$sum"
```

**执行结果**  

```
[root@localhost scripts]# sh for.sh 100
user is : root
user is : bin
user is : daemon
user is : adm
user is : lp
char is A
char is B
char is C
1+2..+100=5050
```

## shell script 追踪与调试  
**语法**：sh [-nvx]  xxx.sh  
**选项与参数**：  
-n:不执行，仅检查语法错误  
-v:在执行前，输出 script 内容  
-x:将使用到 script 内容显示到屏幕，追踪主要靠这个  

**举例**：  

```
[root@localhost scripts]# sh -x function.sh nopara
+ case $1 in
+ print
+ '[' -z '' ']'
+ echo $'\344\275\240\346\262\241\346\234\211\350\276\223\345\205\245\345\217\202\346\225\260' 
你没有输入参数
[root@localhost scripts]# sh -x function.sh one
+ case $1 in
+ print 1
+ '[' -z 1 ']'
+ echo '你输入的是 1'
你输入的是 1
```

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)