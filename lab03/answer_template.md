# Lab03 Assignment

> 班级：
> 学号：
> 姓名：

## Q1 管道

请写出命令`who | wc -l`的结果并分析其执行过程。

运行结果：

![pipe](img/fig.jpg)

分析：

我的分析是xxx

## Q2 shebang

假如在脚本的第一行放入`#!/bin/rm`或者在普通文本文件中第一行放置`#!/bin/more`，然后将文件设为可执行权限执行，看看会发生什么，并解释为什么。

- `#!/bin/rm`

描述发生了什么(截图)

![pipe](img/rm.jpg)

为什么

- `#!/bin/more`

描述发生了什么(截图)

![pipe](img/more.jpg)

为什么

## Q3 重定向

解释以下命令`sh test && cat a.txt || cat b.txt >f1 <f2 2>&1`'若命令执行到最后一个 `cat b.txt`,`f1`中的内容为`b.txt`的内容还是`f2`的内容

是xxx的内容。

原因：

xxx

## Q4 数组，循环

- 编写一个 Shell 脚本计算斐波那契数列，第 17 个值，禁止打表输出。

```bash
# 粘贴你的代码
```

运行结果截图：

![fib](img/fig.jpg)

## Q5 正则

完成[LeetCode: 193. Valid Phone Numbers](https://leetcode-cn.com/problems/valid-phone-numbers/)(体会`grep -E`, `grep -P`, `egrep`, `awk`等的差异)

```bash
# 粘贴你的代码
```

AC截图：

![lc193](img/fig.jpg)

## Q6 文件读入

- 完成[LeetCode: 195. Tenth Line](https://leetcode.com/problems/valid-phone-numbers/)，给出你的代码和 AC 截图。(提示：[怎么读取每一行](http://blog.sina.com.cn/s/blog_605f5b4f0101b0sd.html))

```bash
# 粘贴你的代码
```

AC截图：

![lc193](img/fig.jpg)

## Q7 语法

- 完成一个简单的交互设计，根据用户输入输出对应内容，具体交互内容随意，要求至少用上`select`，`case`和`read`。

```bash
# 粘贴你的代码
```

交互体验截图：

![inter](img/fig.jpg)

## Q8 综合实验

编写Shell脚本`addowner.sh`将某目录下面所有的文件名后面加上文件所有者的名字。比如`a.txt`和`file`的所有者都为owner，文件名修改后分别为`a[owner].txt`和`file[owner]`。

- 使用用法：`./addowner 目录名称`。（保证有目录名称这一参数）
- 对于子目录，名称不变。
- （提示：为了测试效果，请通过`useradd`创建若干用户，并可通过`chown`改变文件的所有者。另外，网上的答案是错的。）

```bash
# 粘贴你的代码
```

运行结果截图(包含目录、文件(含`.`与不含`.`)，并体现出多个文件所有者)：

![owner](img/fig.jpg)

完成本综合实验的过程和体会：

xxx

## 实验感想
