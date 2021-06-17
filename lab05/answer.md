# Lab05 Assignment 参考答案

## 1. 参数传递

请写这样一个程序（不是函数）：传入三个参数，传入该程序的第一个参数用以判断该程序进行有理数算术加运算还是减运算（0 表示将要进行加运算，1 表示将要进行减运算），第二个第三个参数分别是加（减）运算的第一第二个元素。（提示：`main(int argc, char*argc[])`，可获取命令行参数）。

```c
#include<stdio.h>
#include<string.h>

int to_int(char s[]){
    int len = strlen(s);
    int num = 0;
    for(int i = 0; i < len; i++){
        num *= 10;
        num += s[i] - '0';
    }
    return num;
}
int main(int argc,char *argv[]){
    if(strcmp("0",argv[1])==0){
        printf("%d\n",to_int(argv[2])+to_int(argv[3]));
    }
    else{
        printf("%d\n",to_int(argv[2])-to_int(argv[3]));
    }
}
```

## 2. 进程调用

请写这样一个程序：使用创建子进程的方式调用上一个程序，进行加运算和减运算（请学习`fork`和`exec`函数的使用）。

```c
#include<unistd.h>
#include<string.h>
#include<stdio.h>
#include<stdlib.h>
#include<signal.h>
#include<errno.h>
#include<fcntl.h>
#include<string.h>
#include<signal.h>

char *s[4];
char *S[4];

int main(){

    s[0] = "./a";
    s[1] = "1";
    s[2] = "50";
    s[3] = "60";

    S[0] = "./a";
    S[1] = "0";
    S[2] = "50";
    S[3] = "60";

    if (fork() == 0)    execv("./a",s);
    else execv("./a",S);

    return 0;
}
```

## 3. 守护进程

守护进程有哪些特点，创建一个守护进程需要哪些步骤，每一步的意义是什么？（如果没有这一步可能有什么问题）

- 特点 :

  1.守护进程是脱离于终端并且在后台运行的进程，它不受终端控制，及时终端推出，它仍然在后端运行。

  2.守护进程在执行过程中产生的信息不在任何终端显示，也不会被任何终端所产生的信息所打断。

- 步骤 :

  1.创建子进程

  2.让子进程脱离控制终端

  3.改变当前目录为根目录

  4.修改文件权限掩码

  5.关闭文件描述符

  每一步的详情和意义参见《Linux 编程基础》118-119 页。

## 4. 僵尸进程

僵尸进程有什么危害？

- 僵尸进程没有任何代码、数据、堆栈，并不会占用多少资源，但它存在于系统的任务列表。

编写一个会产生僵尸进程的程序并运行，在终端查看当前进程。然后利用终端杀死该进程。因为系统所能使用的进程号是有限的，如果大量的产生僵尸进程，将因为没有可用的进程号而导致系统不能产生新的进程。

```c
#include <stdio.h>
#include <unistd.h>
#include <errno.h>
#include <stdlib.h>

int main(){
    pid_t pid;
    pid = fork();
    if (pid < 0){
        perror("fork error:");
        exit(1);
    }
    else if (pid == 0){
        printf("I am child process. I am exiting.\n");
        printf("I will become a zombie!\n");
        exit(0);
    }    else sleep(60);
    return 0;
}
```

## 5. C 实现重定向

编写一个 C 程序：使得调用 `printf()` 时，输出直接重定向至 `log.txt`。

```c
#include<stdio.h>
#include<string.h>

int main(){
    freopen("log.txt","w",stdout);
    printf("Hello World!\n");
    printf("Print into log.txt successfully!");
    return 0;
}
```

## 6. 重定向代码分析

有以下一段代码：

```c
int fd1, fd2, fd3, fd4;
fd1 = open("a.txt", O_RDONLY);
fd2 = open("b.txt", O_WRONLY);
fd3 = dup(fd1);
fd4 = dup2(fd2, 0);

#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#include<sys/types.h>
#include<sys/stat.h>
#include<fcntl.h>

int main(){
    int fd1, fd2, fd3, fd4;
    fd1 = open("a.txt", O_RDONLY);
    fd2 = open("b.txt", O_WRONLY);
    fd3 = dup(fd1);
    fd4 = dup2(fd2, 0);
    printf("%d %d %d %d",fd1, fd2, fd3, fd4);
    while (1);
    return 0;
}
```

请问，最后的 fd1, fd2, fd3, fd4 的值为多少？并解释原因。

```c
fd1 = 3
fd2 = 4
fd3 = 5
fd4 = 0
```

open 返回的是文件描述符。文件描述符是打开的文件数量减去一。因为对于每个进程，都会打开 3 个描述符，分别是标准输入（0），标准输出（1），标准错误（2）。所以 fd1 为 3，fd2 为 4。而 dup 函数的作用是复制一个和 fd1 指向同一个文件的文件描述符，并且新的文件符总是取最小的可用值，所以这时 fd3 为 5。而 dup2(fd2,0)的作用就是将 fd2 所指的文件将其拷贝到 0 之后，然后将 0 返回，所以 fd4 为 0，且 0 也是 b.txt 的文件描述符。

### 7. 管道

编写 C 语言程序实现如下功能：创建父子进程，父子进程之间通过管道进行通信，父进程向子进程发送字符串，子进程接收到该字符串后，将该字符串的最后 5 个字符发送给父进程。

```c
#include<stdio.h>
#include<unistd.h>
#include<string.h>
int main() {
    int pipefds[2];
    int returnstatus;
    int pid;
    returnstatus = pipe(pipefds);
    if (returnstatus == -1) {
        printf("Unable to create pipe\n");
        return 1;
    }
    pid = fork();
    // Child process
    if (pid == 0) {
        sleep(1);//等待父进程写入管道
        char childstr[20],childstr1[20];
        read(pipefds[0],childstr, sizeof(childstr));
        printf("子进程从管道读出：%s\n",childstr);
        int len=strlen(childstr);
        int i,j;
        for(i=len-5,j=0;i<len;i++,j++){
            childstr1[j]=childstr[i];
        }
        childstr1[j]='\0';
        printf("子进程写入管道：%s\n",childstr1);
        write(pipefds[1], childstr1, sizeof(childstr1));
    }
    else {
        char parentstr[20]="jbw1234567";
        char parentstr1[20];
        printf("父进程写入管道：%s\n",parentstr);
        write(pipefds[1], parentstr, sizeof(parentstr));
        sleep(3);//等待子进程读入并写入管道
        read(pipefds[0],parentstr1, sizeof(parentstr1));
        printf("父进程从管道读出：%s\n",parentstr1);
    }
}
```
