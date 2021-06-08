# Lab03b Assignment 参考答案

## 0. （选做，不算分，不限语言）批量删除 IDEA 工程文件

参考如下的目录结构：

```shell
./sp_java/
├── .idea
│   ├── .gitignore
│   ├── misc.xml
│   ├── modules.xml
│   └── workspace.xml
├── sp_java.iml
└── src
    ├── Test.class
    └── Test.java
```

在当前目录下可能存在**多个**类似于如上 `sp_java` 工程的文件夹。

要求批量删除**这些**文件夹中的 IDEA 工程文件，转换成如下的干净的目录结构。

```shell
./sp_java
└── src
    └── Test.java
```

参考答案：

```sh
#!/bin/sh
find .  -name "*.class" -exec rm -rf {} \;
find .  -name "*.iml" -exec rm -rf {} \;
find . -type d -name ".idea" -exec rm -rf {} \;
```

## 1. shebang

- 假如在脚本的第一行放入`#!/bin/rm`或者在普通文本文件中第一行放置`#!/bin/more`，然后将文件设为可执行权限执行，看看会发生什么，并解释为什么。

- `#!/bin/rm`

执行后文件会被删除。因为该语句指明了执行的命令为 `rm` 删除。

![20210417002714](https://cdn.jsdelivr.net/gh/ZewanHuang/Img@master/Images/20210417002714.png)

- `#!/bin/more`

执行后会标准输出文件内容。因为该语句指明了执行的命令为 `more` 标准显示。

![20210417002956](https://cdn.jsdelivr.net/gh/ZewanHuang/Img@master/Images/20210417002956.png)

为什么

## 2. 运行、打印

- 编写一个 bash 脚本，执行该脚本文件将得到两行输出，第一行是你的学号，第二行是当前的日期（考虑使用`date`命令）。对该脚本文件的要求是
  - 文件名为`date-${你的学号}`，比如`date-15131049`
  - 用户可以在任意位置**只需要输入文件名**就可以执行该脚本文件
  - **不破坏除用户家目录之外的任何目录结构**，即不要在家目录之外的任何地方增删改任何文件
    **请详细叙述你的操作过程以及操作过程的截图，并给出你所编写的脚本文件的代码。**

> 我在用户根目录下的 `~/.bash_profile` 中加入脚本文件所在目录路径，然后执行 `source ~/.bash_profile`，之后便可以在任何位置执行 `date-19373257.sh`。

```sh
#!/bin/bash
echo 19373257
echo `date`
```

![20210417011840](https://cdn.jsdelivr.net/gh/ZewanHuang/Img@master/Images/20210417011840.png)

![20210417011608](https://cdn.jsdelivr.net/gh/ZewanHuang/Img@master/Images/20210417011608.png)

## 3. 正则

- 完成[LeetCode: 193. Valid Phone Numbers](https://leetcode.com/problems/valid-phone-numbers/) | [leetcode-cn](https://leetcode-cn.com/problems/valid-phone-numbers/) (体会`grep -E`, `grep -P`, `egrep`, `awk`等的差异)

```bash
grep -P '^([0-9]{3}-|\([0-9]{3}\) )[0-9]{3}-[0-9]{4}$' file.txt
grep -E '^([0-9]{3}-|\([0-9]{3}\) )[0-9]{3}-[0-9]{4}$' file.txt
```

## 4. 文件读入

- 完成[LeetCode: 195. Tenth Line](https://leetcode.com/problems/tenth-line/) | [leetcode-cn](https://leetcode-cn.com/problems/tenth-line/)，给出你的代码和 AC 截图。(提示：[怎么读取每一行](http://blog.sina.com.cn/s/blog_605f5b4f0101b0sd.html))

```bash
#!/bin/bash
idx=0
while read -r line; do
idx=$(($idx+1))
if [ $idx -eq 10 ]; then
echo ${line}
fi
done <file.txt
```

## 5. 语法

- 完成一个简单的交互设计，根据用户输入输出对应内容，具体交互内容随意，要求至少用上`select`，`case`和`read`。

```bash
#!/bin/bash
echo "Please select what you want to do?"
select cmd in "Sleep" "Study" "Know me" "Eat"
do
    case $cmd in
        "Sleep")
            echo "Go, you such a lazy pig"
            break
            ;;
        "Study")
            echo "Oh, you are so juan, I don't like you"
            break
            ;;
        "Know me")
            echo "The command as you see"
            while read -r line
            do
                    echo $line
            done <date-19373257.sh
            break
            ;;
        "Eat")
            echo "ok, let's go eating"
            break
            ;;
        *)
            echo "wrong cmd"
    esac
done
```

## 6. 综合实验

- 编写 Shell 脚本`addowner.sh`将某目录下面所有的文件名后面加上文件所有者的名字。比如`a.txt`和`file`的所有者都为 owner，文件名修改后分别为`a[owner].txt`和`file[owner]`。
  - 使用用法：`./addowner 目录名称`。（若无目录名称这一参数，则默认为当前目录）
  - 对于子目录，名称不变。
  - （提示：为了测试效果，请通过`useradd`创建若干用户，并可通过`chown`改变文件所有者。另外，网上的答案是错的。）

```bash
#!/bin/bash
i=0
if [ $# -ge 1 ]; then
    cd $1 || exit
fi
ls -l | awk '{print $3, $9}' | while IFS= read -r line
do
    if [ $i -gt 0 ]; then
        eval $(echo $line | awk -F " " '{print "user="$1";file="$2}')
        if [[ -f ${file} ]]; then
            if [[ "$(expr index ${file} . )" != 0 ]]; then
                mv ${file} ${file%%.*}[$user].${file##*.}
            else
                mv ${file} ${file}[$user]
            fi
        fi
    fi
    ((i++))
done
```

```bash
#!/bin/bash

cd ./test
for file in $(ls)
do
 str1=`expr match  "$file"  '.*\..*'`
 str2=`expr match  "$file"  '.*'`
 if [ $str1 -eq $str2 ];then
  name=${file%%.*}
  suffix=${file##*.}
  mv $file $name[$(whoami)].$suffix
 else
  mv $file $file[$(whoami)]
 fi
done
```

黄泽桓参考答案：

**子目录不变**

```bash
#!/bin/bash

function changeName()
{
    file=$1
    array=(`ls -l ${file}`)
    who=${array[2]}
    if [[ $file =~ "." ]]; then
        new=${file%.*}[$who].${file##*.}
    else
        new=$file[$who]
    fi
    mv $1 $new
}
function travFolder()
{
    if [ $# -eq 0 ]; then
        dir="./"
    else
        dir=$1
    fi
    cd $dir
    for i in $(ls)
    do
        if [ -f $i ]; then
            changeName $i
        fi
    done
}
travFolder $1
```

**子目录变化（递归版）**

```bash
#!/bin/bash
function changeName()
{
    file=$1
    array=(`ls -l ${file}`)
    who=${array[2]}
    if [[ $file =~ "." ]]; then
        new=${file%.*}[$who].${file##*.}
    else
        new=$file[$who]
    fi
    mv $1 $new
}
function travFolder()
{
    if [ $# -eq 0 ]; then
        dir="./"
    else
        dir=$1
    fi

    cd $dir

    for i in $(ls)
    do
        if [ -d $i ];then
            travFolder $i
        else
            changeName $i
        fi
    done
}

travFolder $1
```
