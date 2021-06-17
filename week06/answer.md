# Week06 Assignment 参考答案

## 1. 文件和目录的作用有什么不同？

LINUX 有四种基本文件系统类型：普通文件、目录文件、连接文件和特殊文件，可用 file 命令来识别。

- 普通文件
  - 如文本文件、C 语言元代码、SHELL 脚本、二进制的可执行文件等，可用 cat、less、more、vi、emacs 来察看内容，用 mv 来改名。
  - LINUX 的一些设备如磁盘、终端、打印机等都在文件系统中表示出来，则一类文件就是特殊文件，常放在/dev 目录内。例如，软驱 A 称为/dev/fd0。LINUX 无 C：的概念，而是用/dev/had 来自第一硬盘。
- 目录文件
  - 包括文件名、子目录名及其指针。它是 LINUX 储存文件名的唯一地方，可用 ls 列出目录文件。

## 2. 简述标准文件 I/O 和系统调用 I/O 的各自优缺点是什么？

- 系统调用 I/O 读写文件时，每次操作都会执行相关系统调用，这样处理的好处是直接读写实际文件，坏处是频繁地系统调用会增加系统开销
- 标准文件 I/O 可以看成是在文件 I/O 的基础上封装了缓冲机制，先读写缓冲区，必要时再访问实际文件出，从而减少系统调用的次数

## 3. 符号链接和硬链接的异同点是什么？

### 相同点

- 都建立了对同一个文件的链接
- 删除链接对源文件均无影响

### 不同点

- 含义不同：符号链接是一类特殊的文件，其包含有一条以绝对路径或相对路径的形式指向其它文件或者目录的引用；硬链接是一个文件的一个或多个文件名
- 删除文件性质不同：
  - 在对符号链接进行读写时，系统会自动把该操作转换为对源文件的操作，但删除链接文件时，系统仅仅删除链接文件，而不删除源文件本身；如果目标文件被移动、重命名或删除，任何指向它的符号链接仍然存在，但会指向一个不复存在的文件
  - 而移动或删除原始文件时，硬链接不会被破坏，因为它所引用的是文件的物理数据
- 硬链接的文件不需要用户有访问原始文件的权限，也不会显示原始文件的位置

## 4. 查看权限

假设目前系统的 umask 为 0022，执行`CREAT("a.txt", 765)`代码后，请写出文件`a.txt`所有者、组用户及其他用户对该文件的权限。

![fig](img\right.png)

所有者：不能读、能写、能执行。111

组用户：能读、不能写、不能执行。100

其他用户：能读、不能写、不能执行。101

## 5. 文件锁的使用

关于文件锁的使用，当某进程对某个文件上了读取锁或者写入锁后，请就下面几种情况分别编写程序进行分析，并解释原因。

(1,2) 创建子进程，请问子进程对该文件是否拥有同样的锁？该进程执行 exec 调用，请问该进程对文件的锁是否依然有效？

我先引用 man 页面的原话如下:

> Record locks are not inherited by a child created via fork(2), but are preserved across an execve(2).
> 也就是说，子进程不能拥有同样的锁，这是合理的，因为文件锁是相对于进程而言的。
> exec 调用，可以理解为还是父进程。

首先我要先介绍一下我对`fcntl`这个复杂的函数的封装。

我在`kaiyan.h`里面将 fcntl 函数封装成了四个函数:

```c
bool fcntlReadable(int fd); //能否加读锁
bool fcntlWritable(int fd); //能否加写锁
int fcntlSetReadLock(int fd); //成功0,失败-1
int fcntlSetWriteLock(int fd); //成功0,失败-1
```

下面是测试:

```c
# include "kaiyan.h"

void confirmClose(int fd){
    //由于关闭文件会导致所有锁丢失，这里用read来主动关闭文件
    while(1){
        char msg[] = "是否关闭文件并退出?(y/n)";
        write(0, msg, strlen(msg));
        char quit[] = "y";
        char buffer[] = "123456789123456789";
        read(0, buffer, strlen(quit));
        if(strncmp(buffer, quit, 1) == 0){
            closeThrow(fd);
            exit(0);
        }
    }
}
//测试锁的冲突性
void test1(int fd){
    if(forkThrow() == 0){ //子进程
        if(fcntlWritable(fd))printf("OK\n");
        else printf("Can not\n");
    }else{ //父进程
        confirmClose(fd);
    }
}
int main(){
    int fd = openThrow("file.txt");
    if(fcntlSetWriteLock(fd) == 0){
        test1(fd);
    }else{
        printf("一开始加锁就失败了");
        confirmClose(fd);
    }
    return 0;
}
```

分别手动改函数名，上面的测试的结果如下:

```c
/*
 * 2021-4-11, Linux
 * 父进程 子进程 测试结果
 * ------|------|--------
 * 有读锁 加读锁 OK
 * 有读锁 加写锁 Can not
 * 有写锁 加写锁 Can not
 * 有写锁 加读锁 Can not
 */
```

可见写锁的独占性和读锁的共享性。
~~exec 我就不测试了，太累了。。。~~

---

(3) 当该进程对文件建立锁的文件描述符关闭后，这些锁是否仍然有效？
引用 man 页面的原话如下:

> If a process closes any file descriptor referring to a file, then all of the process's locks on that file are released, regardless of the file descriptor(s) on which the locks were obtained. This is bad: it means that a process can lose its locks on a file such as /etc/passwd or /etc/mtab when for some reason a library function decides to open, read, and close the same file.

可见，如果关闭了这个文件，那么所有所有的进程对这个文件上的锁就会被释放。
也就是说，如果一个恶意的病毒想要破坏我们对某个文件上的锁，那个病毒只需要打开这个文件，然后再关闭这个文件就行了。。。

测试:

```c
//测试关闭文件是否能松开锁
void test2(int fd){
    if(forkThrow() == 0){ //子进程
        //等待父进程关闭文件大概3秒
        sleep(3);
        if(fcntlReadable(fd))printf("OK\n");
        else printf("Can not\n");
    }else{ //父进程
        closeThrow(fd);
    }
}
```

上面的测试给出:

```plain
/*
 * 2021-4-11, Linux, test2
 * 父进程          子进程   测试结果
 * --------------|--------|--------
 * 有读锁后close   加读锁   OK
 * 有读锁后close   加写锁   OK
 * 有写锁后close   加写锁   OK
 * 有写锁后close   加读锁   OK
 */
```

---

(3) 如果对这些文件描述符执行 dup 操作，锁是否对新的文件描述符仍然有效？

关于`Open file descriptions`，man 页面的原话是:

> The term open file description is...in kernel-developer a struct file...a child process created via fork(2) inherits duplicates of its parent's file descriptors, and those duplicates refer to the same open file descriptions...Each open() of a file creates a new open file description; thus, there may be multiple open file descriptions corresponding to a file inode...
> 也就是说，多个文件描述符对应同一个`struct file`，多个`struct file`对应一个 inode，一个 inode 对应一个真实存在的文件。

测试:

```c
//测试dup能否把fcntl的锁也dup出来
void test3(int fd){
    int fdNew = dup(fd);
    if(forkThrow() == 0){ //子进程
        if(fcntlReadable(fdNew))printf("OK\n");
        else printf("Can not\n");
    }else{ //父进程
        confirmClose(fd);
    }
}
```

测试结果: 子进程仍然无法对新的 fd 上锁，说明 dup 会把锁也 dup 了

```plain
/*
 * 2021-4-11, Linux, test3
 * 父进程   子进程        测试结果
 * -------|-------------|--------
 * 有读锁   给dup加读锁   OK
 * 有读锁   给dup加写锁   Can not
 * 有写锁   给dup加写锁   Can not
 * 有写锁   给dup加读锁   Can not
 */
```

---

(4) 假设该进程对某个文件加了读取锁/写入锁，然后又同时对该进程进行写入/读取操作，将会发生什么情况？

用`F_SETLK`等等设置的锁是建议性的"Advisory record locking"，意思是如果你遵循良好的编程习惯，你那么大概率能帮助你解决锁冲突的问题，但是你仍然可以进行写入和读取操作。

Linux 号称自己能实现内核级别的"强制锁(Mandatory locking)"，也就是说能从内核底层上自动识别危险的`read`和`write`操作，这涉及到`O_NONBLOCK`选项。

> If the O_NONBLOCK flag is not enabled, then the system call is blocked until the lock is removed or converted to a mode that is compatible with the access. If the O_NONBLOCK flag is enabled, then the system call fails with the error EAGAIN...
> 然而我在 man 页面却看到了满满的 BUG 警告:
> Warning: the Linux implementation of mandatory locking is unreliable. See BUGS below. Because of these bugs, and the fact that the feature is believed to be little used...
> 之后还说如果要想让强制锁发挥最大作用，需要在`mount`的时候加`-o`选项等等。。。看着就特别复杂。
> 不过还好"强制锁"是"non-POSIX"的，也就是说属于一个"超纲"的功能，也不能太怪 Linux 了，毕竟人家免费。。。

~~演示就不演示了。。。~~

---

题外话:

下面是我测试的脚本全部内容:

```c
# include "kaiyan.h"

void confirmClose(int fd){
    //由于关闭文件会导致所有锁丢失，这里用read来主动关闭文件
    while(1){
        char msg[] = "是否关闭文件并退出?(y/n)";
        write(0, msg, strlen(msg));
        char quit[] = "y";
        char buffer[] = "123456789123456789";
        read(0, buffer, strlen(quit));
        if(strncmp(buffer, quit, 1) == 0){
            closeThrow(fd);
            exit(0);
        }
    }
}

//测试锁的冲突性
void test1(int fd){
    if(forkThrow() == 0){ //子进程
        if(fcntlWritable(fd))printf("OK\n");
        else printf("Can not\n");
    }else{ //父进程
        confirmClose(fd);
    }
}

//测试关闭文件是否能松开锁
void test2(int fd){
    if(forkThrow() == 0){ //子进程
        //等待父进程关闭文件大概3秒
        sleep(3);
        if(fcntlReadable(fd))printf("OK\n");
        else printf("Can not\n");
    }else{ //父进程
        closeThrow(fd);
    }
}

//测试dup能否把fcntl的锁也dup出来
void test3(int fd){
    int fdNew = dup(fd);
    if(forkThrow() == 0){ //子进程
        if(fcntlReadable(fdNew))printf("OK\n");
        else printf("Can not\n");
    }else{ //父进程
        confirmClose(fd);
    }
}

int main(){
    int fd = openThrow("file.txt");
    if(fcntlSetReadLock(fd) == 0){
        test2(fd);
    }else{
        printf("一开始加锁就失败了");
        confirmClose(fd);
    }
    return 0;
}
```

下面是我的`kaiyan.h`的全部内容:

```c
//kaiyan.h
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<stdbool.h>
#include<unistd.h>
#include<sys/types.h>
#include<sys/stat.h>
#include<fcntl.h>
#include<errno.h>

void perrorExit(const char* s){
    printf("errno = %d:", errno);
    perror(s);
    exit(-1);
}

int openThrow(const char* file){
    int fd = open(file, O_RDWR | O_CREAT, S_IRWXU | S_IRWXG | S_IRWXO);
    if(fd < 0) perrorExit("openThrow");
    return fd;
}

void closeThrow(int fd){
    if(close(fd) < 0)perrorExit("closeThrow");
}

int forkThrow(){
    int pid = fork();
    if(pid < 0) perrorExit("forkThrow");
    return pid;
}

//>>>>>>>>>>> fcntl相关 >>>>>>>>>>>
//这几个函数是高度封装的，可以理解为public
//所有的函数都不会向stdout输出
bool fcntlReadable(int fd); //能否加读锁
bool fcntlWritable(int fd); //能否加写锁
int fcntlSetReadLock(int fd); //成功0,失败-1
int fcntlSetWriteLock(int fd); //成功0,失败-1

//剩下的函数都是辅助函数，可以理解为private
int fcntlThrow(int fd, int cmd, struct flock* ptr){
    int eax = fcntl(fd, cmd, ptr);
    if(eax < 0){
        perror("fcntlThrow");
        exit(0);
    }
    return eax;
}

struct flock flockInit(short type){
    struct flock lock;
    lock.l_type = type; //这是最关键的参数
    //下面的参数一般没有必要自己设置
    lock.l_whence = SEEK_SET; //一般我们只想从文件开头开始锁
    lock.l_start = 0; //一般我们只想从文件开头开始锁
    lock.l_len = 0; //一般我们只想锁住整个文件
    lock.l_pid = getpid(); //一般我们只想查看自己能否上锁
    return lock;
}

short fcntlGetLockType(int fd, short type){
    struct flock lock = flockInit(type);
    fcntlThrow(fd, F_GETLK, &lock);
    return lock.l_type;
}

bool fcntlReadable(int fd){ //能否加读锁
    return fcntlGetLockType(fd, F_RDLCK) == F_UNLCK;
}

bool fcntlWritable(int fd){ //能否加写锁
    return fcntlGetLockType(fd, F_WRLCK) == F_UNLCK;
}

void fcntlSetLock(int fd, short type){
    //不检查能否加锁
    struct flock lock = flockInit(type);
    fcntlThrow(fd, F_SETLK, &lock);
}

int fcntlSetReadLock(int fd){
    if(fcntlReadable(fd)){
        fcntlSetLock(fd, F_RDLCK);
        return 0;
    }
    return -1;
}

int fcntlSetWriteLock(int fd){
    if(fcntlWritable(fd)){
        fcntlSetLock(fd, F_WRLCK);
        return 0;
    }
    return -1;
}

//<<<<<<<<<<< fcntl相关 <<<<<<<<<<<
```
