# Linux 学习记录--正则表达式与其应用  

# 正则表达式与其应用  

##数据处理工具：awk ,sed  

正则表达式基本上是一种“表示法”，只要工具程序支持这种表示法，那么该工具程序就可以用来作为正则表达式的字符串处理只用。例如 vi,grep,awk,sed 等工具  

##正则表达式特殊符号   

语系对应正在表达式也会存在影响。比如  
LANG=C 时：0 1 2 3 4 … A B C D ..Z a b c d ..z  
LANG=ZH_CN 时：0 1 2 3 4 …a A b B c C d D …….  
因此[a-z]当 C 语系时代表的意义是获取小写字母。在 ZH_CN 语系时代表的意义就是获取字母(大写与小写)    
为了避免数字和字母的选取错误，正则表达式采用特殊符号来代表  
[:alnum:]：代表英文大小写字符及数字。A-Z a-z 0-9  
[:alpha:]：代表英文大小写字符 A-Z a-z  
[:blank:]：代表空格与 TAB 键    
[:cntrl:]：代表键盘上的控制按键 CR,LF,TAB,DEL 等    
[:digit:]：代表数据 0-9    
[:graph:]：代表除了空格与 TAB 键的其他所有按键    
[:lower:]:代表小写字符    
[:upper:]：代表大写字符    
[:print:]：代表任何可以被打印出来了的字符    
[:punct:]：代表标点符号字符    
[:space:]：代表会产生的空白的字符如 TAB 空格 CR    
[:xdigit:]：代表十六进制的数字类型 0-9 A-F a-f    
**举例**    
[root@localhost ~]# cat xargsfile |grep -n '[[:upper:]]'    
2:FRA    
4:AWEE    

## 基础正则表达式字符    

|字符|意义与范例|
|:--|:--------|
|^word| 意义：查找以 word 为行首的数据
举例：查找以#开始的那一行
gerp ‘^#’ file.txt|
|Word$ |意义：查找以 word 为行尾的数据
举例：查找以#为结尾的那一行
grep‘#$’ file.txt|
|. |意义：代表一定有一个任意字符
举例查找字符串 eae,ebe e e，ee 之间一定有一个字符，空格也算字符
G
rep ‘e.e’ file.txt |
|* |意义：重复0个到无穷个前一个字符
举例：查找含有 es ess esss 等的字符串
grep ‘ess*’ file.txt |
|[] |意义：从字符集合中找出想要选取的字符
举例：查找含有 gl 或 gd 的那一行
grep ‘g[ld]’ file.txt|
|[n1-n2] |意义：从字符集合里找出想要选取的字符范围
举例：查找含有任意数字的哪一行
grep ‘[0-9]’ file.txt|
|[^] |意义：从字符集合中找处不要的字符或范围
举例：查找不含大写字母的那一行
grep ‘[^A-Z]’ file.txt|
|\{n,m\}|意义：连续 n 个到 m 个的前一个字符，如\{n\}则是连续n个前一个字符，如\{n,\}则是连续 n 个以上前一个字符
举例1：查找 g 与 g 之间包含2个到3个 o 的字符串如：goog gooog
grep ‘\{2,\3}’ file.txt
举例2：查找 g 与 g 之间包含2个 o 的字符串如：goog
grep ‘\{2\}’ file.txt
举例3：查找 g 与 g 之间包含3个及以上 o 的字符串如：gooog,gooood,goo….od
grep ‘\{3,\}’ file.txt|

## 扩展正则表达式

|字符| 意义与范例|
|:---|:---------|
|+ |意义：重复一个或一个以上的字符
举例：查找god,good,good等字符串
egrep ‘go+d’ file.txt|
|? |意义：0个过1个前一个字符
举例：查找gd god
egrep ‘go?d’ file.txt|
|`| `|意义：用或的方式找出数个字符串
举例：找出my ,own
egrep ‘my|own’ file.txt|
|() |意义：找出“组“的字符串
举例：找出good 或glad
egrep ‘g(oo|la)d’ file.txt|
|()+ |意义：多个重复组的判别
举例：找出Axy123123123C
egrep ‘Axy(123)+C’ file.txt|

**说明：如 grep 需要使用扩展正则表达式，可使用 grep –e 或 egrep**  
### sed 

sed 本身是一个也是一个管道命令。可以分析输入数据流，还可以将数据进行替换，删除，选去等操作  
**sed 与 tr 的区别**  
tr 操作的单元是字符，它针对字符进行删除和替换  
sed 操作单元的是行，它针对行进行删除和替换 
**sed 与 vim 的区别**    
sed 是管道命令它修改只是输入数据流，并不会修改文件本身。虽然 sed 也可以直接修改文件，但是不需要打开文件，对于大文件来说很有帮助   
vim 是文本编辑器，它修改的是文件本身  
**语法**：sed [-nefr] ‘动作’  
**选项与参数**：  
-n:silent 模式，只将 sed 处理过的内容显示 i 出来  
-e:设置多个 sed 动作  
-f filename: 文件内记录 sed 脚本 scipt  
-r:sed 支持的扩展正则表达式语法（默认是基础正则表达式）  
-i:直接修改读取文件内容，而不是屏幕输出   
**动作：n1,n2function**  
n1,n2不一定存在  
**function**:  
a：新增，a 后面接字符串，这些字符在当前的下一行显示  
c：替换，c 后面接字符串，这些字符替换 n1-n2之间的行  
d：删除，删除 n1-n2之间的行  
i：添加，i 后面接字符串，这些字符在当前的上一行显示  
p: 打印，打印 n1~n2行之间的数据  
s: 替换以关键字形式替换，并不是替换整行. sed ‘s/旧字符串/新字符串/g’’  
**举例**：  
 
```  
[root@localhost ~]# cat sedfile |sed -n 'p' =>查询所有内容  
line 1
line 2
line 3
line 4
line 5
line 6
line 7
line 8
line 9
line 10
[root@localhost ~]# cat sedfile |sed -n '1,4p'=>查询1~4内容
line 1
line 2
line 3
line 4
[root@localhost ~]# cat sedfile |sed '2a new line'|sed -n '1,5p'
=>在第2行下添加新的一行
line 1
line 2
new line
line 3
line 4
[root@localhost ~]# cat sedfile |sed '3d'|sed -n '1,4p'
=>删除第3行
line 1
line 2
line 4
line 5
[root@localhost ~]# cat sedfile |sed '2i insert line'|sed -n '1,5p'
=>在第2行上添加新的一行
line 1
insert line
line 2
line 3
line 4
[root@localhost ~]# cat sedfile |sed '2,3c replace line'|sed -n '1,3p'
=>替换2~3行
line 1
replace line
line 4
[root@localhost ~]# cat sedfile |sed '1,3s/ne/NEL/g'|sed -n '1,6p'
=>用 NEL 替换2~3行的 ne
liNEL 1
liNEL 2
liNEL 3
line 4
line 5
line 6
```

举例2：使用 sed 直接修改文件  

```
[root@localhost ~]# sed -i '$a this is line' sedfile ;cat sedfile|tail -n 2
line 10
this is line
```

### awk  

(awk 功能很强大，这里只是功能介绍性说明)  
Awk 是一个数据处理工具，相比 sed 作用于一整行的处理，awk 则将一行分为数个“字段”处理  
**awk 的处理流程**  
1.读入第一行，并将第一行的数据填入$0,$1,$2……等变量中   
2.依据条件类型的限制，判断是否需要后面的动作  
3.做完所有的动作与条件类型  
4.若还有后续的行的数据，则重复1-3步骤  
**语法**：awk ‘条件类型1 {动作1}条件类型2 {动作2}……’ filename  
**说明**：  
1.awk 默认用空格或 tab 来分割一行数据，并将数据填充到$1,$2..中  
如：root  pts1  192这一行， $1=root  $2=pts1.  
2.         awk 后方语句中非变量需使用双引号来定义，变量可以直接使用  
**awk 内置变量**  

|**运算符**|**描述**|
|:--------|:-------|
|= += -= *= /= %= ^= **= |赋值|
|?:| C 条件表达式|
|`||`| 逻辑或|
|&& |逻辑与|
|~ ~!| 匹配正则表达式和不匹配正则表达式|
|< <= > >= != == |关系运算符|
|空格 |连接|
|+ - |加，减|
|* / & |乘，除与求余|
|+ - ! |一元加，减和逻辑非|
|^ *** |求幂|
|++ -- |增加或减少，作为前缀或后缀|
|$ |字段引用|
|in| 数组成员|

**举例1：查看$1 NR NF**  

```
[root@bogon ~]# last -n 5 | awk '{print $1 "\t lines: " NR "\t cols: "NF }'
root     lines: 1        cols: 10
root     lines: 2        cols: 9
root     lines: 3        cols: 9
reboot   lines: 4        cols: 9
root     lines: 5        cols: 10
```

**举例2：带有条件的，仅输出$1==root 的数据**  

```
[root@bogon ~]# last -n 5 | awk '$1=="root" {print $1 "\t lines: " NR "\t cols: "NF }'
root     lines: 1        cols: 10
root     lines: 2        cols: 9
root     lines: 3        cols: 9
root     lines: 5        cols: 10
```

**awk 关键字**  

## BEGIN
 
BEGIN 关键字作用是预设，在读如第一行前面就执行 BEGIN 后面的动作  
比如每一行默认分割方式是空格或是 TAB，所以我们可以设置 FS 来改变分割符，但是此时第一行数据已经读取分析完毕，列信息已经存在$1，$2..中，改变只能从第2行开始。  
**举例**：  

```
[root@bogon ~]# cat /etc/passwd|head -n 5 |awk 'BEGIN {FS=":"} NR=="1" {print"UID\tGID"} NR>="1" {print $1 "\t" $3}'
UID     GID
root    0
bin     1
daemon  2
adm     3
lp      4
```

**举例2 计算数据(num1+num2)**  

```
数据文件
month:num1:num2
1:100:150
2:200:250
3:300:350
4:400:450
5:500:550
6:600:650

[root@bogon ~]# cat cal.file |awk 'BEGIN {FS=":"} NR=="1" {print$1"\t"$2"\t"$3"\ttotal"} NR>"1" {print$1"\t"$2"\t"$3"\t"$2+$3}'
```

```
month   num1    num2    total
1       100     150     250
2       200     250     450
3       300     350     650
4       400     450     850
5       500     550     1050
6       600     650     1250
```

## END

END 操作将在扫描完全部的输入之后执行  

**举例**：  

```
[root@bogon ~]# cat cal.file |awk 'BEGIN {FS=":"} NR=="1" {print$1"\t"$2"\t"$3"\ttotal"} NR>"1" {print$1"\t"$2"\t"$3"\t"$2+$3} END {print "sum"}'
month   num1    num2    total
1       100     150     250
2       200     250     450
3       300     350     650
4       400     450     850
5       500     550     1050
6       600     650     1250
sum
```

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)