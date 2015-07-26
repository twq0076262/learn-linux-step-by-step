# Linux 学习记录--有名管道通信

![](images/23.bmp)

# 有名管道通讯

## 什么是有名管道

匿名管道应用的一个重大限制是它没有名字，因此，只能用于具有亲缘关系的进程间通信，在有名管道（named pipe 或 FIFO）提出后，该限制得到了克服。FIFO 不同于管道之处在于它提供一个路径名与之关联，以 FIFO 的文件形式存在于文件系统中。这样，即使与 FIFO 的创建进程不存在亲缘关系的进程，只要可以访问该路径，就能够彼此通过 FIFO 相互通信  
## 有名管道创建
int mkfifo(const char * pathname, mode_t mode)  
和普通文件创建一样 pathname 为文件名称，mode为权限  

## 有名管道通信规则

#管道关闭规则

int close (int __fd);  
1．当最后一个读进程管道关闭时，写进程无论是阻塞还是非阻塞，都会将管道写满（如果能写满）并退出  
2．当最后一个写进程管道关闭时，向管道写入一个结束标识，当读进程从管道读到这个结束标识时，如果是阻塞读进程将结束阻塞返回读入数据个数为0.（对于未阻塞读进程如果管道内没有数据则返回-1，如果读到结束标识则返回读入数据个数为0）
  
# 规则分析1  

**写进程**

```
#include<unistd.h>
#include<stdlib.h>
#include<stdio.h>
#include<string.h>
#include<fcntl.h>
#include<limits.h>
#include<sys/types.h>
#include<sys/stat.h>
#include<errno.h>
#define FIFO_NAME "/tmp/my_fifo"
#define BUF_SIZE 80000
intmain(int argc, char *argv[]) {
    unlink(FIFO_NAME);
    int pipe_fd;
    int res;
    char buf[BUF_SIZE];
    memset(buf, 3, BUF_SIZE);
    if (access(FIFO_NAME, F_OK) == -1) {
        res = mkfifo(FIFO_NAME, 0766);
        if (res != 0) {
            fprintf(stderr, "不能创建管道文件 %s\n", FIFO_NAME);
            exit(1);
        }
    }
    printf("进程 PID %d 打开管道 O_WRONLY\n", getpid());
    pipe_fd = open(FIFO_NAME, O_WRONLY);
    if (pipe_fd != -1) {
        res = write(pipe_fd, buf, sizeof(buf));
        printf("写入数据的大小是%d \n", res);
        close(pipe_fd);
        sleep(1000);
    } else
        exit(1);
    exit(1);
}
```

**读进程**  

```
#include<unistd.h>
#include<stdlib.h>
#include<stdio.h>
#include<string.h>
#include<fcntl.h>
#include<limits.h>
#include<sys/types.h>
#include<sys/stat.h>
#define FIFO_NAME "/tmp/my_fifo"
#define BUF_SIZE 20
intmain(int argc, char *argv[]) {
    char buf[BUF_SIZE];
    memset(buf, 0, BUF_SIZE);
    int pipe_fd;
    int res;
    int bytes_read = 0;
    printf("进程 PID %d 打开管道 O_RDONLY\n", getpid());
    pipe_fd = open(FIFO_NAME, O_RDONLY);
    if (pipe_fd != -1) {
        bytes_read = read(pipe_fd, buf, sizeof(buf));
        printf("读入数据的大小是%d \n", bytes_read);
        sleep(10);
        close(pipe_fd);
    } else
        exit(1);
    exit(1);
}
```

**控制台信息**  
**读进程：**  
进程 PID 10930打开管道 O_RDONLY  
读入数据的大小是20  
**写进程：**  
进程 PID 10918打开管道 O_WRONLY  
（10S后输出…..）  
写入数据的大小是65536  
**分析：**当读进程执行到 close(pipe_fd);时，写进程一次性将数据写满缓冲区（65536）并退出。

# 规则分析2  

**写进程**  

```
#define FIFO_NAME "/tmp/my_fifo"
#define BUF_SIZE 80000
int main(int argc, char *argv[]) {
    unlink(FIFO_NAME);
    int pipe_fd;
    int res;
    char buf[BUF_SIZE];
    memset(buf, 3, BUF_SIZE);
    if (access(FIFO_NAME, F_OK) == -1) {
        res = mkfifo(FIFO_NAME, 0766);
        if (res != 0) {
            fprintf(stderr, "不能创建管道文件 %s\n", FIFO_NAME);
            exit(1);
        }
    }
    printf("进程 PID %d 打开管道 O_WRONLY\n", getpid());
    pipe_fd = open(FIFO_NAME, O_WRONLY);
    if (pipe_fd != -1) {
        res = write(pipe_fd, buf, sizeof(buf));
        printf("写入数据的大小是%d \n", res);
        sleep(10);
        close(pipe_fd);
    } else
        exit(1);
    exit(1);
}
```

**读进程**

```
#define FIFO_NAME "/tmp/my_fifo"
#define BUF_SIZE 4000
int main(int argc, char *argv[]) {
    char buf[BUF_SIZE];
    memset(buf, 0, BUF_SIZE);
    int pipe_fd;
    int bytes_read = 0;
    printf("进程 PID %d 打开管道 O_RDONLY\n", getpid());
    pipe_fd = open(FIFO_NAME, O_RDONLY);
    if (pipe_fd != -1) {
        do {
            bytes_read = read(pipe_fd, buf, sizeof(buf));
            printf("读入数据的大小是%d \n", bytes_read);
        } while (bytes_read != 0);
        close(pipe_fd);
    } else
        exit(1);
    exit(1);
}
```

### 控制台输出：  
**读进程**  
进程 PID 12240打开管道 O_RDONLY  
读入数据的大小是4000.  
……  
(10S后)  
读入数据的大小是0  
**写进程**  
进程 PID 12227打开管道 O_WRONLY  
写入数据的大小是80000  
**分析：**  
如果读进程为阻塞的，当写进程关闭管道时，读进程收到写进程发来的结束符，读进程结束阻塞（此时bytes_read =0）  
如果读进程为非阻塞的，首先将所有数据读取出来，然后在读进程未收到写进程发来的结束符时，由于管道没有数据读进程不会阻塞且返回-1，因为此例 WHILE 退出条件是 bytes_read =0，因此在未读到结束符之前返回值一直是-1，直到读取到结束符才返回0  

# 管道写端规则  
## 对于设置了阻塞标志的写操作：  
1.当要写入的数据量不大于 PIPE_BUF 时，linux 将保证写入的原子性。如果此时管道空闲缓冲区不足以容纳要写入的字节数，则进入睡眠，直到当缓冲区中能够容纳要写入的字节数时，才开始进行一次性写操作。  
2.当要写入的数据量大于 PIPE_BUF 时，linux 将不再保证写入的原子性。FIFO 缓冲区一有空闲区域，写进程就会试图向管道写入数据，写操作在写完所有请求写的数据后返回。    
## 对于没有设置阻塞标志的写操作：  
3.当要写入的数据量大于 PIPE_BUF 时，linux 将不再保证写入的原子性。在写满所有 FIFO 空闲缓冲区后，写操作返回。  
4.当要写入的数据量不大于 PIPE_BUF 时，linux 将保证写入的原子性。如果当前 FIFO 空闲缓冲区能够容纳请求写入的字节数，写完后成功返回；如果当前 FIFO 空闲缓冲区不能够容纳请求写入的字节数，则返回 EAGAIN 错误，提醒以后再写  
# 管道读端规则  

## 对于设置了阻塞标志的写操作：  
1.如果有进程写打开 FIFO，且当前 FIFO 内没有数据，将一直阻塞。  
## 对于没有设置阻塞标志的写操作：  
2.如果有进程写打开 FIFO，且当前 FIFO 内没有数据。则返回-1，当前 errno 值为 EAGAIN，提醒以后再试。  
# 管道读写规则代码举例  

**写进程**  

```
#include<unistd.h>
#include<stdlib.h>
#include<stdio.h>
#include<string.h>
#include<fcntl.h>
#include<limits.h>
#include<sys/types.h>
#include<sys/stat.h>
#include <errno.h>
#define FIFO_NAME "/tmp/my_fifo"
#define BUF_SIZE 88888
int main(int argc,char *argv[])
{
    int pipe_fd;
    int res;
    char buf[BUF_SIZE];
    memset(buf,3,BUF_SIZE);
    if(access(FIFO_NAME,F_OK)==-1)
    {
        res=mkfifo(FIFO_NAME,0766);
        if(res!=0)
        {
            fprintf(stderr,"不能创建管道文件 %s\n",FIFO_NAME);
            exit(1);
        }
    }
    printf("进程 PID %d 打开管道 O_WRONLY\n",getpid());
    pipe_fd=open(FIFO_NAME,O_WRONLY|O_TRUNC);//1
   // pipe_fd=open(FIFO_NAME,O_WRONLY|O_TRUNC|O_NONBLOCK);//2
    if(pipe_fd!=-1)
    {
        res=write(pipe_fd,buf,sizeof(buf));
        printf("写入数据的长度是%d \n",res);
        close(pipe_fd);
    }
    else
        exit(1);
    exit(1);
}
```

**读进程**  

```
#include<unistd.h>
#include<stdlib.h>
#include<stdio.h>
#include<string.h>
#include<fcntl.h>
#include<limits.h>
#include<sys/types.h>
#include<sys/stat.h>
#define FIFO_NAME "/tmp/my_fifo"
#define BUF_SIZE 4000
int main(int argc, char *argv[]) {
    int res;
    if(access(FIFO_NAME,F_OK)==-1)
    {
        res=mkfifo(FIFO_NAME,0766);
        if(res!=0)
        {
            fprintf(stderr,"不能创建管道文件 %s\n",FIFO_NAME);
            exit(1);
        }
    }
    char buf[BUF_SIZE];
    memset(buf, 0, BUF_SIZE);
    int pipe_fd;
 int num=0;
    int bytes_read = 0;
    printf("进程 PID %d 打开管道 O_RDONLY\n", getpid());
    pipe_fd = open(FIFO_NAME, O_RDONLY);//3
    //pipe_fd = open(FIFO_NAME, O_RDONLY|O_NONBLOCK);//4
    if (pipe_fd != -1) {
        do {
            num++;
            bytes_read = read(pipe_fd, buf, sizeof(buf));
            printf("第%d次读入数据，数据的长度是%d \n",num, bytes_read);
        } while (bytes_read != 0);
        close(pipe_fd);
    } else
        exit(1);
    exit(1);
}
```

上面两段代码分别是管道的读端进程与写端进程。其中有4个注释行。分别代表  
1.       pipe_fd=open(FIFO_NAME,O_WRONLY|O_TRUNC);//1阻塞写端  
2.       pipe_fd= open (FIFO_NAME,O_WRONLY|O_TRUNC|O_NONBLOCK);//2非阻塞写端  
3.       pipe_fd =open(FIFO_NAME, O_RDONLY);//3阻塞读端  
4.       pipe_fd =open(FIFO_NAME, O_RDONLY|O_NONBLOCK);//4非阻塞读端  

可以分以下3种情况分析：  
**说明：写端与读端不能同时都不阻塞**   

# 写端阻塞，读端不阻塞  

**控制台输出如下：**   

**读端进程：**  
进程 PID 5919打开管道 O_RDONLY  
第1次读入数据，数据的长度是-1   
…………..  
第5次读入数据，数据的长度是4000  
…………..  
第26次读入数据，数据的长度是4000  
第27次读入数据，数据的长度是888  
第28次读入数据，数据的长度是0  

**写端进程：**  
进程 PID 5906打开管道 O_WRONLY  
写入数据的长度是88888  
分析：读端满足读端规则2，前面由于写进程还未开始写入数据到管道因此返回-1  
写端满足写端规则2  
# 写端不阻塞，读端阻塞  

**执行流程：**先执行读端程序，在执行写端  
**控制台输出如下：**  
 
**读端进程：**  
进程 PID 6046打开管道 O_RDONLY  
第1次读入数据，数据的长度是4000  
…………  
第16次读入数据，数据的长度是4000  
第17次读入数据，数据的长度是1536  
第18次读入数据，数据的长度是0  
 
**写端进程：**  
进程 PID 6056打开管道 O_WRONLY  
写入数据的长度是65536  
分析：读端满足读端规则1，读进程在为读取到管道数据时一直处于等待阻塞状态  
写端满足写端规则3，写端写满管道后推出，因此写入数据长度是65535，而不是88888  
# 写端阻塞，读端阻塞  

**控制台输出如下：**  

**读端进程：**  
进程 PID 8386打开管道 O_RDONLY  
第1次读入数据，数据的长度是4000  
…………  
第22次读入数据，数据的长度是4000  
第23次读入数据，数据的长度是888  
第24次读入数据，数据的长度是0  

**写端进程：**  
进程 PID 8373打开管道 O_WRONLY  
写入数据的长度是88888  
**分析：**读端满足读端规则1，读进程在为读取到管道数据时一直处于等待阻塞状态  
写端满足写端规则2  

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)