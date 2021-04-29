# Week09 Assignment

> 班级：
> 学号：
> 姓名：

## 1. 请给出下列代码输出行数，并解释原因

```c
#include<stdio.h>
#include<unistd.h>
int main(){
    printf("my pid is %d\n", getpid());
    fork();
    fork();
    fork();
    printf("my pid is %d\n", getpid());
}
```

## 2. 画出上述进程的创建过程

## 3. 编写一个程序，实现下图示的进程之间的关系

进程 A -> 进程 B -> 进程 C

其中箭头的方向表明它们之间的生成关系，即 A 是祖先进程，C 是孙子进程。

## 4. 编写一个程序，创建两个子进程，父进程在屏幕上输出 10 个字符 'A'，两个子进程分别输出 10 个 'B' 和 'C'

> 要求父进程在两个子进程输出完字符后再输出自己的字符。
