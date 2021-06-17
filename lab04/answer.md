# Lab04 Assignment 参考答案

> 班级：
> 学号：
> 姓名：

## 1. 简述文件描述符的作用并回答问题：文件描述符和文件是一对一对应的关系吗？

不是，多个文件描述符可以对应同一个文件。

## 2. 写出文件 I/O 函数`open()`、`read()`、`write()`、`close()`都分别有哪些参数，每个参数的含义是什么？

```c
int open(const char* filename, int oflag,);
filename为打开的文件名，oflag为文件打开标志（O_RDONLY,O_WRONLY,O_RDWR等）
Int close(int fd);
fd为关闭文件的文件描述符
ssize_t read(int fd,void* buf,size_t count);
ssize_t write(int fd, const void* buf,size_t count);
fd为文件描述符，buf为读/写的数据，count为读/写的字符数量
```

## 3. 创建一个文件，内容为你的姓名的全拼（如张三同学，文件中的内容即为`zhangsan`)。编写 c 语言程序实现以下功能：首先打开该文件并输出文件的内容，之后将文件的内容修改为`May the force be with you, ${姓名全拼}!`，比如`May the force be with you, zhangsan!`，输出修改后文件的内容，最后关闭文件。要求使用到`open()` `read()` `write()` `close()`函数。请详细叙述你的操作过程以及操作过程的截图，并给出你所编写的 C 程序的代码

```c

#include<unistd.h>
#include<stdio.h>
#include<fcntl.h>
#include<string.h>
int main(){
   char* s1="May the force be with you,";
   char* s2="!";
   char buf[20]={0};
   int fd;
   if((fd=open("1.txt",O_RDWR))==-1){
      perror("open error");
      exit(1);
   }

   ssize_t size=read(fd,buf,20);
   //printf("%s",buf);
   if(size==-1){
      perror("read error");
      exit(1);
   }
   lseek(fd, 0, SEEK_SET);
   write(fd,s1,strlen(s1));
   write(fd,buf,size);
   write(fd,s2,strlen(s2));
   close(fd);
   return 0;
}
```

## 4. 针对书中例 4-3 按照顺序执行下列命令

```shell
./writelock&
ls-l>book.dat
```

- 查看 book.dat 数据是否发生变化，解释原因。

ls -l 的内容写⼊了⽂件，因为 writelock 加的建议性锁， ls -l 的输出重定向进了book.dat

- 输入书中 P110 程序 write.c,按下列方式执行程序

```shell
./writelock &
./write
./write
./write
```

- book.dat 文件数据的变化情况是什么？给出原因，给出必要的截图。

./write的内容写⼊了⽂件，因为 writelock 加的建议性锁

## 5. 创建文件`~/srcfile`，使用 `ln` 命令创建 `srcfile` 的软链接文件 `~/softlink` ，给出使用的命令；使用 `ls -l` 查看 `~` ，观察 `softlink` 的文件大小，并解释为什么；使用 `ln` 命令创建 `srcfile`的硬链接文件 `~/hardlink` ，给出使用的命令；使用 `ls -l` 观察 `srcfile` 硬链接数的变化

```sh
ln -s srcfile softlink
# softlink 的文件大小为7字节，软链接存储源文件的路径
ln srcfile hardlink
# srcfile硬链接数加1
```

## 6. 编写一个 myls 程序，要求输入一个参数代表指定路径，打印路径下所有文件的名称

参考课本例 4-8

## 7. 创建一个文件，内容为你的姓名的全拼（如张三同学，文件中的内容即为`zhangsan`)。编写 c 语言程序实现以下功能：首先打开该文件并输出文件的内容，之后将文件的内容修改为`May the force be with you, ${姓名全拼}!`，比如`May the force be with you, zhangsan!`，输出修改后文件的内容，最后关闭文件。**要求使用到`fopen()` `fread()` `fwrite()` `fclose()`函数**。请详细叙述你的操作过程以及操作过程的截图，并给出你所编写的 C 程序的代码

```c
#include<stdio.h>
#include<string.h>
int main(){
   char* s1="May the force be with you,";
   char* s2="!";
   char buf[20]={0};
   FILE *file;
   file=fopen("1.txt","r+");
   fread(buf,20,1,file);
   //printf("%s",buf);
   fseek(file, 0, SEEK_SET);
   fwrite(s1,strlen(s1),1,file);
   fwrite(buf,strlen(buf),1,file);
   fwrite(s2,strlen(s2),1,file);
   fclose(file);
   return 0;
}
```
