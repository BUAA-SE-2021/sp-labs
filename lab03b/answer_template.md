# Lab03b Assignment

> 班级：
> 学号：
> 姓名：

## 1. shebang

- 假如在脚本的第一行放入`#!/bin/rm`或者在普通文本文件中第一行放置`#!/bin/more`，然后将文件设为可执行权限执行，看看会发生什么，并解释为什么。

- `#!/bin/rm`

描述发生了什么(截图)

![rm](img/rm.jpg)

为什么

- `#!/bin/more`

描述发生了什么(截图)

![more](img/more.jpg)

为什么

## 2. 运行、打印

- 编写一个 bash 脚本，执行该脚本文件将得到两行输出，第一行是你的学号，第二行是当前的日期（考虑使用`date`命令）。对该脚本文件的要求是
  - 文件名为`date-${你的学号}`，比如`date-15131049`
  - 用户可以在任意位置**只需要输入文件名**就可以执行该脚本文件
  - **不破坏除用户家目录之外的任何目录结构**，即不要在家目录之外的任何地方增删改任何文件
    **请详细叙述你的操作过程以及操作过程的截图，并给出你所编写的脚本文件的代码。**

## 3. 正则

- 完成[LeetCode: 193. Valid Phone Numbers](https://leetcode-cn.com/problems/valid-phone-numbers/)(体会`grep -E`, `grep -P`, `egrep`, `awk`等的差异)

```bash
# 粘贴你的代码
```

AC 截图：

![lc193](img/fig.jpg)

## 4. 文件读入

- 完成[LeetCode: 195. Tenth Line](https://leetcode.com/problems/valid-phone-numbers/)，给出你的代码和 AC 截图。(提示：[怎么读取每一行](http://blog.sina.com.cn/s/blog_605f5b4f0101b0sd.html))

```bash
# 粘贴你的代码
```

AC 截图：

![lc195](img/fig.jpg)

## 5. 语法

- 完成一个简单的交互设计，根据用户输入输出对应内容，具体交互内容随意，要求至少用上`select`，`case`和`read`。

```bash
# 粘贴你的代码
```

交互体验截图：

![inter](img/fig.jpg)

## 6. 综合实验

- 编写 Shell 脚本`addowner.sh`将某目录下面所有的文件名后面加上文件所有者的名字。比如`a.txt`和`file`的所有者都为 owner，文件名修改后分别为`a[owner].txt`和`file[owner]`。
  - 使用用法：`./addowner 目录名称`。（若无目录名称这一参数，则默认为当前目录）
  - 对于子目录，名称不变。
  - （提示：为了测试效果，请通过`useradd`创建若干用户，并可通过`chown`改变文件所有者。另外，网上的答案是错的。）

```bash
# 粘贴你的代码
```

运行结果截图(包含目录、文件(含`.`与不含`.`)，并体现出多个文件所有者)：

![owner](img/fig.jpg)

完成本综合实验的过程和体会：

xxx

## 7. 实验感想
