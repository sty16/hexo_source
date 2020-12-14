---
layout: a
title: unix shell简单实现
toc: true
date: 2020-12-14 20:21:10
tags: Linux
categories: Linux
---



### 1.shell整体框架

[github项目地址](https://github.com/sty16/simple_shell)

shell程序整体框架，读取用户输入命令并执行

![图片1.png](https://i.loli.net/2020/12/14/fmitdD6aWzpHGh9.png)

进入main函数后，循环执行从标准输入读取命令，解析命令，将命令字符串以空格分隔，解析为命令与参数。判断是否为内建命令(history, jobs, fb, exit)。如果不是则父进程通过fork()创建子进程，子进程执行execv()完成进程代码段与数据段内容替换。

### 2.输入输出重定向

定义命令结构体，其包含argc(命令参数个数)、bg(后台命令标志)、name(命令名称)、argv(命令参数)、fds(重定向文件描述符)等五个成员变量。

```c++
struct command {
    int argc;
    int bg;
    string name;
    string argv[ARG_MAX];
    int fds[2];
};
```

解析指令时command > file检查是否有>或<操作符，有则检查后面是否有文件，若果有文件则以读写的方式打开文件，并将返回文件描述符记录到命令结构体中，在fork()后的子进程中，采用dup2()函数将子进程的输入、输出重定向fds所指向的文件描述符。

![图片7.png](https://i.loli.net/2020/12/14/5wjpN4sEdIFifTb.png)

### 3.实现管道
```c++
struct commands {
    string key;
    int cmd_counts;
    struct command* cmds[CMD_MAX];
    commands *pre, *next;
}
```

定义命令集(commands)结构体，成员cmd_counts为命令个数，cmds记录每一个解析后的命令指针。

+ 检查输入的字符中是否有”|”，如果有按照|将字符串分隔开，并解析每一个命令(支持多条命令)

+ 创建管道，前一个命令的stdout重定向到管道的写端，后一个命令的stdin重定向的管道的读端。

+ Fork()创建每一个子进程，执行指令

  ![图片2.png](https://i.loli.net/2020/12/14/htSEg2HT4DQG8on.png)

  ![图片8.png](https://i.loli.net/2020/12/14/bH7i2nsAtNSwZaO.png)

### 4.作业控制

```c++
struct job_t {
    pid_t pid;
    int jid;
    int state;
    string cmd;
};
```
定义job_t结构体，包含成员进程编号，作业编号，作业状态，命令字符串。定义作业运行状态：UNDEF:0、FG:1、BG:2、 ST:3

定义JOBCtrl类，来控制作业的执行。

![图片3.png](https://i.loli.net/2020/12/14/UwmWtxdeJrfNVgo.png)

父进程fork()创建子进程后，父进程将作业添加到作业列表，并定义SIG_CHLD信号处理函数，当子进程结束执行时，发送SIG_CH

LD信号给父进程。父进程捕捉信号，在信号处理函数中调用waitpid()函数获得子进程pid，将其从作业列表中删除。

### 5.后台执行程序

![图片4.png](https://i.loli.net/2020/12/14/T73QYpMuF2waIZH.png)

如果是前台执行程序，则父进程处于while循环的等待状态，直到子进程执行完毕向父进程发送SIG_CHLG信号，父进程捕捉到信号后将其从作业列表中删除后，结束while循环等待。如果是后台执行程序则打印作业相关信息并继续执行读取用户输入。

### 6.作业控制 jobs fg bg

+ jobs查看所有作业的运行状态， 将作业列表中的所有作业打印输出

+ fg % 1，从作业列表中找到后台运行的作业，将其状态变为FG，并执行等待，直到子进程结束发出SIG_CHLD信号，从作业列表中删除。

+ bg % 1，父进程注册SIGTSTP信号处理，CRTL+Z, 发送STGTSTP信号给子进程，暂停子进程执行。当执行bg命令时，父进程向暂停的子进程发送SIGCONT信号恢复子进程的执行。

  ![图片9.png](https://i.loli.net/2020/12/14/SstzXpCmdu31U2A.png)
### 7.历史命令

实现了CMDCache类

![图片5.png](https://i.loli.net/2020/12/14/yOtWgKl5DLiPq6G.png)

+ 哈希表+双向链表维护历史命令，哈希表记录输入字符串与解析后的命令结构体的映射，双向链表记录历史输入命令

+ LRU(least Recently used)算法，当输入字符串已经被解析过时，将命令从双向链表中删除，并重新命令列表头部插入。输入字符串未解析过，解析后插入列表头部。

+ history命令 将历史命令按输入顺序的倒序排列输出

+ up、down命令获取上一条输入命令、下一条命令输入

![图片11.png](https://i.loli.net/2020/12/14/fCrIyUwi1RMmaqb.png)

