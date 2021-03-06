# Lab06 Assignment

> 班级：
> 学号：
> 姓名：

## 1. 请简述信号什么时候处于未决状态，并简述信号存在未决状态的作用

## 2. 若在某信号的信号处理函数中给自己发送同一个信号，请简单描述程序的行为

## 3. 编写程序实现如下功能

程序 A.c 通过 sigqueue()函数按用户输入向程序 B.c 发送信号和附加数据；B.c 程序接收到该信号后，输出伴随信号的附加数据。运行过程如下：

```sh
./B & # 此时，输出进程 B 的 PID 号。
./A processB_PID sigvalue # 第一个参数表示进程 B 的 PID，第二个参数为伴随信号的附加数据(int 值即可)。
```

```c
//code of A

```

```c
//code of B

```

截图：

## 4. 请实现这样一个程序

程序每间隔 1 秒输出你的学号，当按下 ctrl+c 后，程序询问是否退出程序（此时停止输出学号），输入 Y 或 5 秒未进行任何输入则退出程序，输入 N 程序恢复运行，继续输出学号（提示：alarm()函数设置超时时间，SIGALRM 信号处理函数作为超时处理）。

```c
//code

```

## 5. 请实现这样一个程序

在程序中创建一个子进程，通过信号实现父子进程交替输出，父进程输出学号，子进程输出姓名，要求父进程先输出。

```c
//code

```

截图：

## 6. 父子进程

父进程等待子进程退出通常仅需调用 wait()函数，但如果子进程未退出，父进程将会一直处于阻塞态,并通过循环不断获取子进程状态，该回收子进程的方式是对 CPU 资源的浪费。子进程终止时会自动向父进程发送 SIGCHLD 信号，请通过该特性实现这样一个程序：父进程创建 5 个子进程，每个子进程输出 PID 后以不同的状态值退出，父进程使用 SIGCHLD 信号实现异步回收子进程，每回收一个子进程就输出该子进程的 PID 和退出状态值，需要保证任何情况下所有子进程都能回收（提示：SIGCHLD 是不可靠信号，不支持排队，考虑两个子进程同时结束的情况）

```c
//code

```

截图：

## 7. 异步信号安全函数

异步信号安全函数(async-signal-safe function)是可以在信号处理函数中安全调用的函数，即一个函数在返回前被信号中断，并在信号处理函数中再次被调用，均可以得到正确结果。通常情况下，不可重入函数(non-reentrant function)都不是异步信号安全函数，都不应该在信号处理函数中调用。

1. 请判断下面的函数是否是异步信号安全函数，如果是请说明理由，如果不是请给出一种可能发生问题的情况。

   ```c
   int tmp;
   void swap1(int* x, int* y)
   {
      tmp = *x;
      *x = *y;
      *y = tmp;
   }
   ```

   ```c
   void swap2(int* x, int* y)
   {
      int tmp;
      tmp = *x;
      *x = *y;
      *y = tmp;
   }
   ```

2. 由于 printf()函数使用全局缓冲区，因此它不是异步信号安全函数。为了避免可能发生的问题，其中一个解决方法是在调用 printf()函数前阻塞所有信号，并在调用后恢复。请用上述思路补全代码，实现 printf()的异步信号安全版本，无需实现格式化输出（提示：sigprocmask()函数可用于阻塞多个信号）。

   ```c
   //code
   void print_safe()
   {
       //TODO:阻塞所有信号
       printf("safe print!\n")
       //TODO:恢复所有信号
   }
   ```

## 8. 实验感想
