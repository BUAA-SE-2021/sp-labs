# Lab04 Assignment

> 班级：
> 学号：
> 姓名：

1. 简述文件描述符的作用并回答问题：文件描述符和文件是一对一对应的关系吗？

   ```
   
   
   ```

   

2. 写出文件 I/O 函数`open()`、`read()`、`write()`、`close()`都分别有哪些参数，每个参数的含义是什么？

   ```
   
   
   
   ```

   

3. 创建一个文件，内容为你的姓名的全拼（如张三同学，文件中的内容即为`zhangsan`)。编写 c 语言程序实现以下功能：首先打开该文件并输出文件的内容，之后将文件的内容修改为`May the force be with you, ${姓名全拼}!`，比如`May the force be with you, zhangsan!`，输出修改后文件的内容，最后关闭文件。要求使用到`open()` `read()` `write()` `close()`函数。请详细叙述你的操作过程以及操作过程的截图，并给出你所编写的 C 程序的代码。

   ```
   
   
   
   ```

   

4. 针对书中例 4-3 按照顺序执行下列命令：

   ```shell
   ./writelock&
   ls-l>book.dat
   ```

   查看 book.dat 数据是否发生变化，解释原因。

   输入书中 P110 程序 write.c,按下列方式执行程序

   ```shell
   ./writelock &
   ./write
   ./write
   ./write
   ```

   book.dat 文件数据的变化情况是什么？给出原因，给出必要的截图。

   ```
   
   
   
   ```

   

5. 创建文件`~/srcfile`，使用 `ln` 命令创建 `srcfile` 的软链接文件 `~/softlink` ，给出使用的命令；使用 `ls -l` 查看 `~` ，观察 `softlink` 的文件大小，并解释为什么；使用 `ln` 命令创建 `srcfile`的硬链接文件 `~/hardlink` ，给出使用的命令；使用 `ls -l` 观察 `srcfile` 硬链接数的变化。

   ```
   
   
   
   ```

   

6. 创建一个文件，内容为你的姓名的全拼（如张三同学，文件中的内容即为`zhangsan`)。编写 c 语言程序实现以下功能：首先打开该文件并输出文件的内容，之后将文件的内容修改为`May the force be with you, ${姓名全拼}!`，比如`May the force be with you, zhangsan!`，输出修改后文件的内容，最后关闭文件。**要求使用到`fopen()` `fread()` `fwrite()` `fclose()`函数**。请详细叙述你的操作过程以及操作过程的截图，并给出你所编写的 C 程序的代码。

   ```
   
   
   
   ```

   

##  实验感想
