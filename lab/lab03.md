# Lab03: Shell 编程

[TOC]

## 实验目的

掌握重定向、管道等命令行使用技巧，进行 Shell 基础编程。

## 实验指南

### 重定向

Linux 中默认输入输出分为 3 个文件：

- 标准输入`stdin`。标准输入文件的编号是`0`**(牢记 Linux 万物皆文件，可以用文件表示设备)**，默认的设备是键盘，命令执行时从键盘读取数据。
- 标准输出`stdout`。标号为`1`，默认的设备是显示器，命令的输出会被打印到屏幕上。
- 标准错误`stderr`。标号为`2`，默认的设备是显示器，命令执行产生的错误信息会被发送到标准错误文件。

重定向的意思就是改变这三个文件实际指向，比如我们希望从某个文件中获取输入，那么就需要将标准输入指向这个文件。重定向后命令依然从标准输入获得输入，此时标准输入指向这个文件，故命令能够从这个文件获取输入。

- 输入重定向
  格式为`命令 < 文件名`，比如 `sort < sp.txt` , 把`sp.txt`文件中的内容作为`sort`的输入。
- 输出重定向
  格式为`命令 > 文件名`，比如 `cat /etc/passswd > ps.log`，`cat`会输出`/etc/passwd`中内容，但此时并不会输出到屏幕上，而是输出到`ps.log`中。
- 错误重定向
  格式为`命令 2> 文件名`，比如 `gcc -c test.c -o test.out 2 > error.log`，如果`gcc`编译时出现错误，则会把错误信息输出到`error.log`中。会覆盖原文件中内容，`>>` 则会将输出追加到原文件末尾。
- 其他
  - 在重定向错误时使用了错误文件的编号`2`，其实在输入输出的时候也可以显式写`0`或`1`，通常是省略。
  - `&`运算符，表示等同于：`2>&1`，表示将标准错误从重定向到标准输出指向的文件。如 `1>/dev/null` ，然后执行`2>&1`，此时都指向空设备。

### 管道

管道作用是将多个命令串连起来，使一个命令的输出作为另一个命令的输入。

格式为`命令1 | 命令2 | 命令3 ....| 命令n`。

比如`ls /etc | grep init `将会输出`/etc`目录下，文件名包含`init`的文件/目录。如果不使用管道，命令就得拆成: `ls /etc > tmp grep init < tmp rm tmp ls /etc | grep init >> test cat test`。

其实管道是一种进程之间通信的手段，在之后的`Linux`系统编程的实验中我们还会经常遇到。

### Shell 编程

#### 什么是 Shell？

通俗一点的解释就是**命令解析器**，用以接收用户输入的命令，然后调用相应的应用程序。

**要注意我们敲命令的地方(也就是那个黑色框框)不叫 Shell！！！那个东西叫 terminal，我们在 terminal 中输入命令，然后 Shell 接收指令**。Shell 可看成是一个进程，terminal 是这个进程的输入输出，与用户交互的部分。

详细可看[知乎：终端、Shell、tty 和控制台（console）有什么区别？](https://www.zhihu.com/question/21711307)

#### 实验环境

这次实验统一使用`bash`，在 Shell 中输入 `echo $SHELL`，输出应当是`/bin/bash`。如果不是，可使用`chsh`命令，之后输入`/bin/bash`将默认 Shell 更改为`bash`。

#### 如何运行 Shell？

以`helloworld.sh`为例

```bash
#!/bin/bash
echo "Hello, World!"
```

- `#!/bin/bash`被称为 shebang。具体其来源与作用见[释伴：Linux 上的 Shebang 符号(#!)](https://blog.csdn.net/u012294618/article/details/78427864)
- 运行该程序需要具有可执行权限，因此要进行`chmod +x helloworld.sh`的操作。
- `./helloworld.sh`即可看到输出。

#### 命令连接符

命令的执行是串行的，一条命令结束才能输入下一条命令，可以在命令之间加上`;`分割命令，从而可以一行输入所有命令。Shell 会挨个执行。

- `&&`连接符
  - 命令`1 && 命令2 && 命令3` ，Shell 在判断出这个表达式的真假后就会停止执行。如果命令 1 为`false`，可以判断表达式 一定为假，执行停止。如果为`true`，那么还需要执行命令 2，一直执行到能判断真假为止或者执行完被`&&`连接的命令。
- `||`连接符
  - 同 `&&` , 执行到能判断真假或者所有被连接命令被执行完为止。`&&`和`||`的计算方式同 c 语言中的`&& ||`。以上运算原则又被称作**短路原则**

#### Shell 变量

- 在 Shell 中使用变量无需定义，在使用的时候创建。并且变量不分类型，Shell 统一认为是字符串，需要的时候通过一些命令进行转换。
- 变量赋值: **变量名=值** ，等号左右不能够有空格。若字符串中包含空格，则需要用单/双引号括起来。
- 可以使用`readonly`将变量改为只读类型。
- 通过 `$` 引用变量值，`echo $SHELL`。
- 输入变量，`read`变量名。

| 引用格式              | 返回值                                      |
| --------------------- | ------------------------------------------- |
| `$var`                | 返回变量值                                  |
| `${#var}`             | 返回变量值的长度                            |
| `${var:start}`        | 返回从 start 下标到字符串末尾的子串         |
| `${var:start:length}` | 返回从 start 下标开始，长度为 length 的子串 |

实际上还有一些空值判断、字符串替换和正则匹配拆分字符串等，为了精简篇幅，这里不再列举，可自行查阅资料。

- 环境变量
  - 环境变量是用来定义系统运行环境的一些参数，比如每个用户不同的家目录`HOME`、邮件存放位置`MAIL`等。
  - 可以使用`env`命令来查看`Linux`系统中所有的环境变量。
  - `export 变量名`，将一个已经存在的本地变量修改为环境变量
  - `export 变量名 = 值`，定义一个环境变量。
- 位置变量
  - 在执行 Shell 脚本的时候，可以传入参数，如当前有个脚本叫`test`，执行`sh test arg1 arg2 arg3` ，那么在`test`中，`$0` 代表脚本文件名，​`$1`为第一个参数:`arg1`，以此类推。
  - 使用`shift`可以将参数左移，此时`$1`为`arg2`，`$2`为`arg3`, `$3`为空`$#`为参数数量。

| 特殊参数 | 含义                                    |
| -------- | --------------------------------------- |
| `$#`     | 传递到脚本的参数数量                    |
| `$?`     | 前一个命令执行情况，`0`成功，其他值失败 |
| `$$`     | 运行当前脚本的进程`id`                  |
| `$!`     | 运行脚本最后一个命令                    |
| `$*`     | 传递给脚本或者函数的所有参数            |

#### 其余 Shell 语法

Shell 编程还可以使用一些比如`if`语句等其余语句，这些语句和咱们学过的`C`中的对应语句功能基本一致，只是写法可能有点不一样，这里就不列举具体的功能了，仅给出一些例子以供参考。

```shell
#!/bin/bash
clear
echo;echo
echo "为 $0 添加执行权限"
chmod +x $0
echo

# 重定向
echo 输出重定向 > __re.txt
cat __re.txt
echo

gcc tan90.file 2>__error.log
cat __error.log
echo

# 管道
ls -l /etc | grep pa
echo

# 定义变量
var=string

# 只读变量不可修改
readonly test
# test=6 去掉注释报错

# 引用变量
echo "var的值为: "$var
echo "var长度为: "${#var}
echo "var2~4为:  "${var:2:3}
echo

# 传入参数
echo "文件名为$0"
echo '$1: '$1
echo '$2: '$2
echo '$1: '$3
echo "传入参数有： "$*
echo "传入参数数量："$#
echo

shift;echo "移位后参数为："
echo '$0: '$0
echo '$1: '$1
echo

# 运算
i=1
((i+=5))
echo i=1,执行 '((i+=5))'
echo 'i=: '$i
echo

# if
echo -e "输入一个文件名，测试是否存在: \c" # -e能解释转义字符，详情man echo
read filename
if [[ -f $filename && -s $filename ]]; then
    echo "$filename 文件存在且不为空"
else
    echo "$filename 文较不存在或为空"
fi
echo
#======================
if [ -d /boot ]; then
    echo "存在/boot目录"
else
    echo "不存在/boot目录"
fi
echo
#==================
if ls -l /boot/efi; then
    echo "访问/boot/efi 成功"
else
    echo "访问/boot/efi 失败"
fi
echo
#======
echo
data=3
data2=2
if [ $data -lt $data2 ]; then
    echo $data \< $data2
elif [ $data -gt $data2 ]; then
    echo $data \> $data2
else
    echo $data = $data2
fi

echo

# select
echo "输入编号选择："
select subject in Math ComputerScience Chinese Moyu
do
    echo "我最喜欢$subject"
    break
done
echo

# case
case $subject in
    'Moyu' ) echo 'Moyu美滋滋';; #2个分号
    '*' )  echo "好好学习天天向上"
esac
echo

# for
for var in  1 2 3 4 5 6
do
    echo -e $var '\c '
done
echo;echo

# while
count=1
sum=0
while [ $count -lt 101 ]
do
    ((sum+=count))
    # count=`expr $count + 1` 这种写法不推荐
    ((count=expr+1))
done
echo sum is $sum
echo

# until 略

function helloworld # 或者 helloworld()
{
    echo 参数个数为 $#
    echo 传入参数为 $*
}
helloworld 1 2 3


# 一个综合一些的例子
f[1]=1
f[2]=1
function fib () {
    for ((i = $1; i < $2; i++)); do
    if (( i > 6 && i > 8)); then
        break
    fi
    ((f[i] = f[i-1] + f[i-2]))
    # f[i]=$((${f[i-1]}+${f[i-2]}))
    # f[i]=`expr ${f[i-1]} + ${f[i-2]}`
    # 这三种方式都可以进行赋值，但expr的方法不推荐(lint会报错)
    echo ${f[i]}
done
}
fib 3 10 # $0: fib, $1: 3, $2: 10

rm __error.log
rm __re.txt
exit 0  # 脚本成功退出
```

## 实验习题

1. 解释以下命令`bash test && cat a.txt || cat b.txt >f1 <f2 2>&1`'若命令执行到最后一个 `cat b.txt`,`f1`中的内容为`b.txt`的内容还是`f2`的内容
2. 编写一个 Shell 脚本计算斐波那契数列，第 17 个值，禁止打表输出。
3. 完成[LeetCode: 193. Valid Phone Numbers](https://leetcode-cn.com/problems/valid-phone-numbers/)，给出你的代码和 AC 截图。(体会`grep -E`, `grep -P`, `egrep`, `awk`等的差异)
4. 完成[LeetCode: 195. Tenth Line](https://leetcode.com/problems/valid-phone-numbers/)，给出你的代码和 AC 截图。(提示：[怎么读取每一行](http://blog.sina.com.cn/s/blog_605f5b4f0101b0sd.html))
5. 完成一个简单的交互设计，根据用户输入输出对应内容，具体交互内容随意，要求至少用上`select`，`case`和`read`。
