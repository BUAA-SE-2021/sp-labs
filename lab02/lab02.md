# Lab02: GCC&GBD&Makefile

[TOC]

## 1. 实验目的

- 通过使用gcc完成c程序转变为可执行文件的过程，进一步理解程序的编译执行方式。

## 2. 实验内容

- gcc 基本使用
  - gcc将一个源程序转换为可执行程序的主要过程。
  - gcc进行源程序预处理。
  - gcc完成编译、汇编及链接过程。
  - gcc创建静态库和动态库。

## 3. 实验指南

### 3.1. gcc的常用命令

1. 无选项编译链接
用法：gcc test.c
作用：将test.c预处理、汇编、编译并链接形成可执行文件。这里未指定输出文件，默认输出为a.out。


2. 选项 -o
用法：gcc test.c -o test
作用：将test.c预处理、汇编、编译并链接形成可执行文件test。-o选项用来指定输出文件的文件名。


3. 选项 -E
用法：gcc -E test.c -o test.i
作用：将test.c预处理输出test.i文件。


4. 选项 -S
用法：gcc -S test.i 
作用：将预处理输出文件test.i汇编成test.s文件。

5. 选项 -c
用法：gcc -c test.s
作用：将汇编输出文件test.s编译输出test.o文件。


6. 无选项链接
用法：gcc test.o -o test
作用：将编译输出文件test.o链接成最终可执行文件test。


7. 选项-O
用法：gcc -O1 test.c -o test

作用：使用编译优化级别1编译程序。级别为1~3，级别越大优化效果越好，但编译时间越长。


## 4. 实验习题

- 