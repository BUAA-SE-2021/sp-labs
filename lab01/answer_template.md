# Lab01 Assignment

> 班级：
> 学号：
> 姓名：

## 实验准备

- 请安装一个合适的 Linux 系统，你安装的 Linux 发行版及版本号是什么？内核版本号是什么？

  截图：

  答案：

- 查看你的根目录下有哪几个子目录，每个子目录主要用来做什么用的？

  截图：

  答案：

- 查看自己的 ip 地址，并 ping 一下 baidu.com 看网络是否连通？

  命令：

  截图：

- 用软件安装命令下载 `build-essential`(注：在 Ubuntu 等 Debian 系的系统中下为`build-essentian`；在 CentOS / Arch 系的系统中有类似的 package，请自行安装，为后续实验作准备)。

  命令：

  截图：

## 1. Linux 命令操作

- Linux 命令行操作，请用你学到的 Linux 命令，实现以下操作，给出每一步你的命令行截图。
  - 用 wget 从`https://github.com/BUAA-SE-2021/patpat/releases/download/v0.1.3/patpat-linux-amd64`下载你们的 Linux 版 OOP 课 Java 自助评测机 patpat
  - `https://github.com/BUAA-SE-2021/sp-labs/lab01/lab01.zip`处下载实验压缩包
  - 解压`lab01.zip`。
  - 进入`lab01`目录,进入子目录`etc`，打印当前路径并在当前路径下创建名为`a1`的目录，并在`a1`目录中创建名为`b1`的目录。
  - 进入`b1`目录中，创建两个文件`a.txt`，`b.txt`。
  - 退回子目录`etc`，删除目录`a1`。
  - 将`etc`目录下所有以`tmp`开头的文件移动到`lab01`目录下的`Download`目录下的`tmp`目录中。
  - 查看`tmp`目录下`a1005.cpp`的内容。
  - 查看`tmp`目录下`a1009.cpp`的前十行和后十行。
  - 将`tmp`目录下的所有文件打成一个`tar`包，并命名为并命名为`tmp.tar.gz`。
  - 返回`lab01`目录，列出当前目录下的文件大小。
  - 用命令找出空目录并将空目录删除。

命令：

```shell
# 不完整的部分自行补充，填写必要注释
code # 下载
code # 下载实验资料
code # 解压
code # 进入目录
code # 打印当前路径
code # 创建目录`a1`
code # 创建子目录`b1`
code # 返回子目录`etc`
code # 删除`a1`
code # 移动
code # 查看
code # 查看前十行和后十行
code # 打包
code # 返回`lab01`目录
code # 列出当前目录下的文件大小
code # 用命令找出空目录
code # 删除空目录
```

必要的实验截图(如查看前十行、查看文件大小的展示效果)

![fig](img/xxx.jpg)

## 2. vi 模式

- vi 编辑器有哪几种模式？简述这几种模式间如何互相切换？

模式：

- 1
- 2
- 3
- 4
- 5

如何切换：

## 3. vi 命令

> 写出以下⼀系列操作使⽤的**命令**（底⾏模式的命令加上 : 或 / ）：

### 3.1. ⽤ `vi` 在当前⽤户家⽬录下新建⽂件 `testfile` 并打开

```shell
# 粘贴你的代码
```

### 3.2. 设置显示⾏号

```shell
# 粘贴你的代码
```

### 3.3. 进⼊ `insert mode` ，输⼊ `3` ⾏⽂本

```plain
This is the first line.
This is the second line.
This is the third line.
```

![fig](img/xxx.jpg)

### 3.4. 返回到 `command mode` ，将光标移动到第 `2` ⾏，复制这⼀⾏

```shell
# 粘贴你的代码
```

### 3.5. 移动光标到⽂档最后⼀⾏，将复制内容粘贴到这⼀⾏后⾯

```shell
# 粘贴你的代码
```

![fig](img/xxx.jpg)

### 3.6. 移动光标到第⼀⾏，删除第⼀⾏

```shell
# 粘贴你的代码
```

![fig](img/xxx.jpg)

### 3.7. 从⽂档开头开始查找 `second` ，然后查找下⼀个

```shell
# 粘贴你的代码
```

### 3.8. 将全部 `second` 替换为 `fourth` ，替换过程不需要询问

```shell
# 粘贴你的代码
```

![fig](img/xxx.jpg)

### 3.9. 取消显示⾏号

```shell
# 粘贴你的代码
```

![fig](img/xxx.jpg)

### 3.10. 保存修改并退出 `vi`

```shell
# 粘贴你的代码
```

### 3.11. 再次⽤ `vi` 打开 `testfile` ，另存为 `testfile2`

```shell
# 粘贴你的代码
```

## 4. POSIX

- 什么是 POSIX 标准？哪些操作系统支持 POSIX 标准？

## 5. LSB

- 什么是 LSB 标准？分析它和 POSIX 标准的异同。

## 6. 实验感想
