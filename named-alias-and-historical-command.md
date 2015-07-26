# Linux 学习记录--命名别名与历史命令

# 命名别名与历史命令

## 命名别名

**语法：**alias 别名=’命令’  
          unalias别名   
alias 如后面什么也不跟。代表查询所有别名命名信息  

**举例1：查看所有别名**  

```
[root@localhost ~]# alias
alias cp='cp -i'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=tty'
alias ll='ls -l --color=tty'
alias ls='ls --color=tty'
alias mv='mv -i'
alias rm='rm -i'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
```

**举例2：设置别名**  

```
[root@localhost ~]# alias dir='cd'
[root@localhost ~]# dir /tmp
[root@localhost tmp]# [root@localhost tmp]#
```

**举例3：取消别名**  

```
[root@localhost tmp]# unalias dir
```

## 历史命令
**语法：**history n  
           history [-c]
           history [raw]histfiles

**选项与参数：**  
**n:**数字，列出最近 n 条命令   
**-c:**将目前 shell 中所有历史命令全部清除  
**-a:**将目前新增的历史命令添加到 histfiles，若没有加 histfiles 则默认添加到   ~/.bash_history   
**-r:**将 histfiles 读取到 这个 shell 的记忆  
**-w:**将目前的 history 记忆写入 histfiles 中   

**说明：**$HISTSIZE 记录了 shell 以及文件中最大存储历史记录数量   
    系统注销时会将 bashshell 历史记录记录到文件中~/.bash_history   

**举例：**  

```
[root@localhost tmp]# history 3 =>查看历史最近3条记录
  876  echo $HISTSIZE
  877  history
  878  history 3

[root@localhost tmp]# history –c =>清空 shell 中的历史记录
[root@localhost tmp]# history 4 =>以前的被清空 因此这里只能查询到这1条
1	history 4
[root@localhost tmp]# history –w =>shell 中的历史记录写入文件
[root@localhost tmp]# vim ~/.bash_history
history 5
vim ~/.bash_history
history –w
```

## 使用历史命令执行命令  
**语法：**  
**!number:**执行第几条命令  
**!command:**由最近的命令向前搜索由 command 开头的命令   
**!!:**执行上一个命令   

**说明：使用以上命令可以做好保密性，别人看到你的命令历史记录却不能知道你的操作**  

**举例：**  

```
[root@localhost /]# history 5 =>先查询历史命令
   16  cd /
   17  history -a
   18  vim ~/.bash_history 
   19  alias
   20  history 5
[root@localhost /]# !19 =>执行第19条命令
alias
alias cp='cp -i'
alias grep='grep --color=auto'
……
[root@localhost /]# !! =>执行上一个命令
alias
alias cp='cp -i'
alias grep='grep --color=auto'
[root@localhost /]# !al =>执行以 al 开头命令
alias
alias cp='cp -i'
```

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)