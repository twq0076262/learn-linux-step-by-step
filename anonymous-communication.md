# Linux 学习记录--匿名管道通信

![](images/22.bmp)

# 匿名管道通讯
**管道是 Linux 支持的最初 Unix IPC 形式之一，具有以下特点：**  
1.管道是半双工的，数据只能向一个方向流动；需要双方通信时，需要建立起两个管道；  
2.只能用于父子进程或者兄弟进程之间（具有亲缘关系的进程）；  

## 什么是管道
管道对于管道两端的进程而言，就是一个文件，但它不是普通的文件，它不属于某种文件系统，而是自立门户，单独构成一种文件系统，并且只存在与内存中。  

## 数据的读出和写入
一个进程向管道中写的内容被管道另一端的进程读出。写入的内容每次都添加在管道缓冲区的末尾，并且每次都是从缓冲区的头部读出数据。  
## 管道的创建

```
#include int pipe(int fd[2])
```

管道两端可分别用描述字 fd[0]以及 fd[1]来描述，需要注意的是，管道的两端是固定了任务的。即一端只能用于读，由描述字 fd[0]表示，称其为管道读端；另一端则只能用于写，由描述字 fd[1]来表示，  

## 管道的规则
1.      当管道内容长度为0时，读端将处于阻塞状态，等待写端向管道写入内容  
2.      当写端数据长度小于缓冲区长度时，数据将以原子性写入缓冲区。  

对读进程来说：  
3.      当写端被关闭时，所有数据被读出后，read 返回0。  
4.      当写端未被关闭时，所有数据被读出后，读端阻塞。  

对写进程来说：  
5.      当读端关闭时，如写端数据长度大于管道最大长度时，写完管道长度时，产生信号 SIGPIPE 后退出程序。（以存入管道的数据读进程可以读取到）  
6.      当读端未被关闭时，如写端数据长度大于管道最大长度时，写完管道长度时，写端将处于阻塞状态  
### 规则分析1

```
#include<unistd.h>
#include<stdio.h>
#include<string.h>
#include<sys/types.h>
#include<stdlib.h>
#include<sys/wait.h>
int main() {
    int fd[2];
    pid_t cid;
    if (pipe(fd) == -1) {
        perror("管道创建失败！");
        exit(1);
    }
    cid = fork();
    switch (cid) {
    case -1:
        perror("子进程创建失败");
        exit(2);
        break;
    case 0:
        close(fd[1]);
        char message[1000];
        int num = read(fd[0], message, 1000);
        printf("子进程读入的数据是：%s,长度是=%d", message, num);
        close(fd[0]);
        break;
    default:
        close(fd[0]);
        char *writeMsg = "父进程写入的数据！";
        sleep(10);//1
        write(fd[1], writeMsg, strlen(writeMsg));
        close(fd[1]);
        break;
    }
    return 0;
}
```

[root@ Release 18$] ps -C processcomm -opid,ppid,stat,cmd  
PID  PPID STAT CMD  
5973 2488 S   /root/workspace/processcomm/Release/processcomm  
5976 5973 S   /root/workspace/processcomm/Release/processcomm  
=>读端由于阻塞中，其所在进程（子进程）处于 sleep 状态  

**控制台输出**  
父进程工作 PID=5973,PPID=2488  
子进程工作 PID=5976,PPID=5973  
子进程读入的数据是：父进程写入的数据！,长度是=27  

### 规则分析2  

```
switch (cid) {
    case -1:
        perror("子进程创建失败");
        exit(2);
        break;
    case 0:
        printf("子进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        break;
    default:
        printf("父进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        close(fd[0]);
        const long int writesize=4000;
        char writeMsg[writesize];
        int i;
        for(i=0;i<writesize;i++)
        {
            writeMsg[i]='a';
        }
        int writenum=write(fd[1], writeMsg, strlen(writeMsg));
        printf("父进程写入的数据长度是=%d\n", writenum);
        close(fd[1]);
        wait(NULL);
        break;
    }
```

**控制台输出**  
父进程工作 PID=7072,PPID=2488  
父进程写入的数据长度是=4001  
子进程工作 PID=7077,PPID=7072  

### 规则分析3  

```
switch (cid) {
    case -1:
        perror("子进程创建失败");
        exit(2);
        break;
    case 0:
        printf("子进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        close(fd[1]);
        char message[40001];
        int num = read(fd[0], message, 4001);
        printf("子进程读入的数据长度是=%d\n", num);
        num = read(fd[0], message, 4000);
        printf("子进程再次读入的数据长度是=%d", num);
        close(fd[0]);
        break;
    default:
        printf("父进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        close(fd[0]);
        const long int writesize = 4000;
        char writeMsg[writesize];
        int i;
        for (i = 0; i < writesize; i++) {
            writeMsg[i] = 'a';
        }
        int writenum = write(fd[1], writeMsg, strlen(writeMsg));
        printf("父进程写入的数据长度是=%d\n", writenum);
        close(fd[1]);
    //  wait(NULL);
        break;
    }
```

[root@ Release30$] ps -C processcomm -o pid,ppid,stat,cmd  
 PID PPID STAT CMD  
=>读写进程都已退出  

**控制台输出**  
父进程工作 PID=8004,PPID=2488  
父进程写入的数据长度是=4001  
子进程工作 PID=8009,PPID=1  
子进程读入的数据长度是=4001  
子进程再次读入的数据长度是=0  
### 规则分析4  

```
switch (cid) {
    case -1:
        perror("子进程创建失败");
        exit(2);
        break;
    case 0:
        printf("子进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        char message[40001];
        int num = read(fd[0], message, 4001);
        printf("子进程读入的数据长度是=%d", num);
        num = read(fd[0], message, 4000);
        printf("子进程再次读入的数据长度是=%d", num);
        break;
    default:
        printf("父进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        close(fd[0]);
        const long int writesize = 4000;
        char writeMsg[writesize];
        int i;
        for (i = 0; i < writesize; i++) {
            writeMsg[i] = 'a';
        }
        int writenum = write(fd[1], writeMsg, strlen(writeMsg));
        printf("父进程写入的数据长度是=%d\n", writenum);
        close(fd[1]);
        break;
    }
```

[root@ Release29$] ps -C processcomm -o pid,ppid,stat,cmd  
 PID PPID STAT CMD  
7916    1 S   /root/workspace/processcomm/Release/processcomm  
=>读进程阻塞  

**控制台输出：**  
父进程工作 PID=7914,PPID=2488  
父进程写入的数据长度是=4001  
子进程工作 PID=7916,PPID=1  

### 规则分析5  

```
switch (cid) {
    case -1:
        perror("子进程创建失败");
        exit(2);
        break;
    case 0:
        printf("子进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
            close(fd[1]);
        char message[65535];
        int num = read(fd[0], message, 65535);
        printf("子进程读入的数据长度是=%d", num);
    close(fd[0]);
        break;
    default:
        printf("父进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        close(fd[0]);
        const long int writesize = 80000;
        char writeMsg[writesize];
        int i;
        for (i = 0; i < writesize; i++) {
            writeMsg[i] = 'a';
        }
        int writenum = write(fd[1], writeMsg, strlen(writeMsg));
        printf("父进程写入的数据长度是=%d\n", writenum);
        close(fd[1]);
        wait(NULL);
        break;
    }
```

[root@ Release25$] ps -C processcomm -o pid,ppid,stat,cmd  
 PID PPID STAT CMD  
=>所有进程都以退出  

**控制台输出**  
父进程工作 PID=7776,PPID=2488  
子进程工作 PID=7778,PPID=7776  
子进程读入的数据长度是=65535  

### 规则分析6  

switch (cid) {
    case -1:
        perror("子进程创建失败");
        exit(2);
        break;
    case 0:
        printf("子进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        break;
    default:
        printf("父进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        const long int writesize=80000;
        char writeMsg[writesize];
        int i;
        for(i=0;i<writesize;i++)
        {
            writeMsg[i]='a';
        }
        int writenum=write(fd[1], writeMsg, strlen(writeMsg));
        printf("父进程写入的数据长度是=%d\n", writenum);
        wait(NULL);
        break;
    }
父进程工作 PID=7309,PPID=2488  
子进程工作 PID=7314,PPID=7309  

[root@ Release24$] ps -C processcomm -o pid,ppid,stat,cmd  
 PID PPID STAT CMD  
7309 2488 S   /root/workspace/processcomm/Release/processcomm  
7314 7309 Z    [processcomm]<defunct>  

## 管道代码举例  

### 1.   当发送信息小于管道最大长度  

```
#include<unistd.h>
#include<stdio.h>
#include<string.h>
#include<sys/types.h>
#include<stdlib.h>
#include<sys/wait.h>
int main() {
    int fd[2];
    pid_t cid;
    if (pipe(fd) == -1) {
        perror("管道创建失败！");
        exit(1);
    }
    cid = fork();
    switch (cid) {
    case -1:
        perror("子进程创建失败");
        exit(2);
        break;
    case 0:
        printf("子进程工作PID=%d,PPID=%d\n", getpid(), getppid());
        close(fd[1]);
        char message[1000];
        int num;
        do {
            num = read(fd[0], message, 1000);
            printf("子进程读入的数据长度是=%d\n", num);
        } while (num != 0);
        close(fd[0]);
        break;
    default:
        printf("父进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        close(fd[0]);
        const long int writesize = 37;
        char writeMsg[writesize];
        int i;
        for (i = 0; i < writesize-1; i++) {
            writeMsg[i] = 'a';
        }
        writeMsg[writesize-1]='\0';
        int writenum = write(fd[1], writeMsg, strlen(writeMsg)+1);
        printf("父进程写入的数据长度是=%d\n", writenum);
        close(fd[1]);
        break;
    }
    return 0;
}
```

### 2.   当发送信息大于管道最大长度  
此例子主要应该规则6，当发送信息大于管道长度时且写进程在未全部将新数据写入管道中，写进程处于阻塞状态，直到所有数据写入管道  

```
int main() {
    int fd[2];
    pid_t cid;
    if (pipe(fd) == -1) {
        perror("管道创建失败！");
        exit(1);
    }
    cid = fork();
    switch (cid) {
    case -1:
        perror("子进程创建失败");
        exit(2);
        break;
    case 0:
        printf("子进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        close(fd[1]);
        char message[1000];
        int num;
        do {
            num = read(fd[0], message, 1000);
            printf("子进程读入的数据长度是=%d\n", num);
        }while(num!=0);
        close(fd[0]);
        break;
    default:
        printf("父进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        const long int writesize = 80000;
        char writeMsg[writesize];
        int i;
        for (i = 0; i < writesize-1; i++) {
            writeMsg[i] = 'a';
        }
        writeMsg[writesize-1]='\0';
        int writenum = write(fd[1], writeMsg, strlen(writeMsg)+1);
        printf("父进程写入的数据长度是=%d\n", writenum);
        close(fd[0]);
        close(fd[1]);
        break;
    }
    return 0;
}
```

### 3.   写进程多次写入  
此例应用规则6，防止多次写入，写入数据长度管道最大长度  

```
int main() {
    int fd[2];
    pid_t cid;
    if (pipe(fd) == -1) {
        perror("管道创建失败！");
        exit(1);
    }
    cid = fork();
    switch (cid) {
    case -1:
        perror("子进程创建失败");
        exit(2);
        break;
    case 0:
        printf("子进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        close(fd[1]);
        char message[1000];
        int num;
        do {
            num = read(fd[0], message, 1000);
            if (num > 0) {
                printf("子进程读入的数据长度是=%d %s\n", num, message);
            }
        } while (num != 0);
        close(fd[0]);
        break;
    default:
        printf("父进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        const long int writesize = 10;
        char writeMsg[writesize];
        int i;
        for (i = 0; i < writesize - 1; i++) {
            writeMsg[i] = 'a';
        }
        writeMsg[writesize - 1] = '\0';
        int writenum = write(fd[1], writeMsg, strlen(writeMsg));
        printf("父进程写入的数据长度是=%d\n", writenum);
        char *newmsg = "helloworld";
        writenum = write(fd[1], newmsg, strlen(newmsg) + 1);
        printf("父进程再次写入的数据长度是=%d\n", writenum);
        close(fd[0]);
        close(fd[1]);
        break;
    }
    return 0;
}
```

### 4.   兄弟间的管道通讯

```
int main() {
    int fd[2];
    pid_t cid, did;
    if (pipe(fd) == -1) {
        perror("管道创建失败！");
        exit(1);
    }
    cid = fork();
    switch (cid) {
    case -1:
        perror("兄进程创建失败");
        exit(2);
        break;
    case 0:
        printf("兄进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        close(fd[1]);
        char message[1000];
        int num;
        do {
            num = read(fd[0], message, 1000);
            if (num > 0) {
                printf("兄进程读入的数据长度是=%d,%s\n", num, message);
            }
        } while (num != 0);
        close(fd[0]);
        break;
    default:
        did = fork();
        if (did == 0) {
            printf("弟进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
            const long int writesize = 10;
            char writeMsgs[writesize];
            int i;
            for (i = 0; i < writesize - 1; i++) {
                writeMsgs[i] = 'a';
            }
            writeMsgs[writesize - 1] = '\0';
            int writenum = write(fd[1], writeMsgs, strlen(writeMsgs) + 1);
            printf("弟进程写入的数据长度是=%d\n", writenum);
            close(fd[0]);
            close(fd[1]);
        } else if (did == -1) {
            perror("弟进程创建失败！");
            exit(3);
        }
        break;
    }
    return 0;
}
```

### 5.   父子双通道管道通讯

```
int main() {
    int fd[2], backfd[2];
    pid_t cid;
    if (pipe(fd) == -1) {
        perror("管道创建失败！");
        exit(1);
    }
    if (pipe(backfd) == -1) {
        perror("管道创建失败！");
        exit(2);
    }
    cid = fork();
    switch (cid) {
    case -1:
        perror("子进程创建失败");
        exit(2);
        break;
    case 0:
        printf("子进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        close(fd[1]);
        char message[10000];
        int num;
        do {
            num = read(fd[0], message, 10000);
            printf("子进程读入的数据长度是=%d\n", num);
        } while (num != 0);
        close(fd[0]);
        close(backfd[0]);
        char *msg1 = "消息返回成功啊！";
        write(backfd[1], msg1, strlen(msg1) + 1);
        close(backfd[1]);
        break;
    default:
        printf("父进程工作 PID=%d,PPID=%d\n", getpid(), getppid());
        const long int writesize = 80000;
        char writeMsg[writesize];
        int i;
        for (i = 0; i < writesize - 1; i++) {
            writeMsg[i] = 'a';
        }
        writeMsg[writesize - 1] = '\0';
        int writenum = write(fd[1], writeMsg, strlen(writeMsg) + 1);
        printf("父进程写入的数据长度是=%d\n", writenum);
        close(fd[0]);
        close(fd[1]);
        close(backfd[1]);
        char msg2[1000];
        int num1 = read(backfd[0], msg2, 1000);
        printf("返回消息是：%s", msg2);
        close(backfd[0]);
        break;
    }
    return 0;
}
```

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)