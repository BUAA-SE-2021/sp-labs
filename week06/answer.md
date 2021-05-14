# Week06 Assignment 参考答案

## 1. 文件和目录的作用有什么不同？

LINUX 有四种基本文件系统类型：普通文件、目录文件、连接文件和特殊文件，可用 file 命令来识别。

- 普通文件
  - 如文本文件、C 语言元代码、SHELL 脚本、二进制的可执行文件等，可用 cat、less、more、vi、emacs 来察看内容，用 mv 来改名。
  - LINUX 的一些设备如磁盘、终端、打印机等都在文件系统中表示出来，则一类文件就是特殊文件，常放在/dev 目录内。例如，软驱 A 称为/dev/fd0。LINUX 无 C：的概念，而是用/dev/had 来自第一硬盘。
- 目录文件
  - 包括文件名、子目录名及其指针。它是 LINUX 储存文件名的唯一地方，可用 ls 列出目录文件。

## 2. 简述标准文件 I/O 和系统调用 I/O 的各自优缺点是什么？

`stdio`的东西在不同的操作系统上实现不同，在 Linux 上是对`unistd`的高度封装，而在 Windows 上就是调用`win32`的 API，
但总之`stdio`是跨平台通用的，而且底层还有缓冲区 buffer 优化。`unistd`的只能在 Linux 上用，里面的 IO 是基于 system-call 来实现的，
而且调用起来没有`stdio`那样那么安全。但是由于种种原因在网络编程里面还是用的`unistd`的多一些:
在读写设备时通常是不希望有缓冲的，例如向代表网络设备的文件写数据就是希望数据通过网络设备发送出去，而不希望只写到缓冲区里就算完事儿了，当网络设备接收到数据时应用程序也希望第一时间被通知到，所以网络编程通常直接调用 Unbuffered I/O 函数。
open、read、write、close 等系统函数称为无缓冲 I/O(Unbuffered I/O)函数，因为它们位于 C 标准库的 I/O 缓冲区的底层。
用 Unbuffered I/O 函数每次读写都要进内核，调一个系统调用比调一个用户空间的函数要 慢很多，所以在用户空间开辟 I/O 缓冲区还是必要的，用 C 标准 I/O 库函数就比较方便，省 去了自己管理 I/O 缓冲区的麻烦。
用 C 标准 I/O 库函数要时刻注意 I/O 缓冲区和实际文件有可能不一致，在必要时需调 用 fflush(3)。

## 3. 符号链接和硬链接的异同点是什么？

硬链接：由于 linux 下的文件是通过索引节点（Inode）来识别文件，硬链接可以认为是一个指针，指向文件索引节点的指针，系统并不为它重新分配 inode。每添加一个一个硬链接，文件的链接数就加 1。
尽管硬链接节省空间，也是 Linux 系统整合文件系统的传统方式，但是存在一下不足之处：（1）不可以在不同文件系统的文件间建立链接（2）只有超级用户才可以为目录创建硬链接。
软链接克服了硬链接的不足，没有任何文件系统的限制，任何用户可以创建指向目录的符号链接。因而现在更为广泛使用，它具有更大的灵活性，甚至可以跨越不同机器、不同网络对文件进行链接。如果给 ln 命令加上- s 选项，则建立软链接。如果[链接名]已经存在但不是目录，将不做链接。[链接名]可以是任何一个文件名（可包含路径），也可以是一个目录，并且允许它与“目标”不在同一个文件系统中。如果[链接名]是一个已经存在的目录，系统将在该目录下建立一个或多个与“目标”同名的文件，此新建的文件实际上是指向原“目标”的符号链接文件。 
软链接与硬链接，区别不仅仅是在概念上，在实现上也是不同的。区别：硬链接原文件＆链接文件公用一个 inode 号，说明他们是同一个文件，而软链接原文件＆链接文件拥有不同的 inode 号，表明他们是两个不同的文件；在文件属性上软链接明确写出了是链接文件，而硬链接没有写出来，因为在本质上硬链接文件和原文件是完全平等关系；链接数目是不一样的，软链接的链接数目不会增加；文件大小是不一样的，硬链接文件显示的大小是跟原文件是一样的，这用强调，因为是等同的嘛，而这里软链接显示的大小与原文件就不同了。总之，建立软链接就是建立了一个新文件。当访问链接文件时，系统就会发现他是个链接文件，它读取链接文件找到真正要访问的文件。

## 4. 查看权限

假设目前系统的 umask 为 0022，执行`CREATE("a.txt", 765)`代码后，请写出文件`a.txt`所有者、组用户及其他用户对该文件的权限。
我电脑上没有 create 函数，我用 open 函数来代替 create。

```c
 ls
open.c
 cat open.c
# include <fcntl.h>
# include <unistd.h>
# include <sys/types.h>
# include <sys/stat.h>

/*
 S_IRWXU  00700 user (file owner) has read, write, and execute permission
 S_IRUSR  00400 user has read permission
 S_IWUSR  00200 user has write permission
 S_IXUSR  00100 user has execute permission

 S_IRWXG  00070 group has read, write, and execute permission
 S_IRGRP  00040 group has read permission
 S_IWGRP  00020 group has write permission
 S_IXGRP  00010 group has execute permission

 S_IRWXO  00007 others have read, write, and execute permission
 S_IROTH  00004 others have read permission
 S_IWOTH  00002 others have write permission
 S_IXOTH  00001 others have execute permission
 */


int main(){
    //int fd = open(const char* path, int flags, [int mode]);
    int fd = open("file.txt", O_RDWR | O_CREAT | O_TRUNC, S_IRWXU | S_IRGRP | S_IWGRP | S_IROTH | S_IXOTH); //mode=765
        //一定要用man给出的宏不要自己写什么777，不要自己写什么数字
        //如果umask是022,也就是000,020,020
        //那么765&~022=(421,420,401)-(000,020,020)=(421,400,401)=745
    close(fd);
    return 0;
}
 umask
000
 gcc open.c
 ./a.out
 la
总用量 24K
-rwxrwxrwx 1 zky zky  17K 4月   8 16:19 a.out
-rwxrw-r-x 1 zky zky    0 4月   8 16:19 file.txt
-rw-r--r-- 1 zky zky 1.2K 4月   8 16:17 open.c
 echo 确实是765
确实是765
 echo 然后现在我改umask
然后现在我改umask
 umask 022
 umask
022
 rf a.out
 rf file.txt
 gcc open.c
 ./a.out
 la
总用量 24K
-rwxr-xr-x 1 zky zky  17K 4月   8 16:20 a.out
-rwxr--r-x 1 zky zky    0 4月   8 16:20 file.txt
-rw-r--r-- 1 zky zky 1.2K 4月   8 16:17 open.c
 echo 发现是745
发现是745

```

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

```
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

```
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
