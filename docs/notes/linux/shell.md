# shell 脚本
目录：

- [一、shell 简介](#一shell简介)
    - [1.shell 版本](#1shell版本)
    - [2.执行 shell](#2执行shell)
        - [方式一：直接执行]()
        - [方式二：指定解释器执行]()
        - [方式三：使用 source 执行]()
        - [方式四：使用 . 执行]()
    - [shell 命令执行过程]()
- [二、shell 语法结构 - 基础]()
    - [（一）、变量]()
        - [1.环境变量]()
        - [2.本地变量]()
        - [3.定义变量]()
        - [4.删除变量]()
        - [5.获取变量的值]()
    - [（二）、文件名代换（Globbing）]()
    - [（三）、命令代换]()
    - [（四）、算术代换]()
        - [1.加减乘除运算]()
        - [2.进制表示]()
    - [（五）、转义字符]()
    - [（六）、引号]()
        - [1.单引号]()
        - [2.双引号]()
- [三、shell 语法结构 - 高级]()
    - [（一）、条件测试]()
    - [（二）、分支语句]()
        - [1.if-else]()
        - [2.case-in]()
    - [（三）、循环语句]()
        - [1.for循环]()
        - [2.while循环]()
        - [3.break 和 continue]()
    - [（四）、位置参数和特殊变量]()
    - [（五）、输入输出]()
        - [1.输出 - echo]()
        - [2.输出 - 管道|]()
        - [3.输出 - tee]()
        - [4.输出 - 文件重定向]()
        - [5.输入 - read]()
    - [（六）、函数]()

## 一、shell简介

shell 是 unix 系统上用来控制系统运行的程序，有以下的特点：

- Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。
- Shell 既是一种命令语言，又是一种程序设计语言。
- Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。

shell 的作用是解释执行用户的命令，执行方式有以下两种：

- 交互式（Interactive）：用户输入一条命令，Shell就解释执行一条。
- 批处理（Batch）：用户事先写一个 Shell 脚本（Script），其中有很多条命令，让Shell 一次把这些命令执行完，而不必一条一条地敲命令。

shell 脚本和编程语言很相似，也有变量和流程控制语句，但 shell 脚本是解释执行的，不需要编译，shell 程序从脚本中一行一行读取并执行这些命令，相当于一个用户把脚本中的命令一行一行敲到 shell 提示符下执行。

### 1.shell 版本

由于历史原因，UNIX 系统上有很多种 Shell：

- sh（Bourne Shell）：由 Steve Bourne 开发，各种 UNIX 系统都配有sh。

- csh（C Shell）：由 Bill Joy 开发，随 BSD UNIX 发布，它的流程控制语句很像 C 语言，支持很多 Bourne Shell 所不支持的功能：作业控制，命令历史，命令行编辑。

- ksh（Korn Shell）：由 David Korn 开发，向后兼容 sh 的功能，并且添加了 csh 引入的新功能，是目前很多 UNIX 系统标准配置的 shell，在这些系统上 /bin/sh 往往是指向 /bin/ksh 的符号链接。

- tcsh（TENEX C Shell）：是 csh 的增强版本，引入了命令补全等功能，在 FreeBSD、Mac OS X 等系统上替代了 csh。

- bash（Bourne Again Shell）：由 GNU 开发的 shell，主要目标是与 POSIX 标准保持一致，同时兼顾对 sh 的兼容， bash 从 csh 和 ksh 借鉴了很多功能，是各种 Linux 发行版标准配置的 Shell，在 Linux 系统上 /bin/sh 往往是指向 /bin/bash 的符号链接。虽然如此，bash 和 sh 还是有很多不同的，一方面，bash 扩展了一些命令和参数，另一方面， bash 并不完全和 sh 兼容，有些行为并不一致，所以 bash 需要模拟 sh 的行为：当我们通过 sh 这个程序名启动 bash 时， bash 可以假装自己是 sh，不认扩展的命令，并且行为与 sh 保持一致。

- Zsh（Z shell）：Zsh 对 Bourne shell 做出了大量改进，同时加入了 Bash、ksh 及 tcsh 的某些功能。命令补全功能非常强大，可以补齐路径，补齐命令，补齐参数等。

可以通过如下的命令来查看当前系统中所有用户所对应的 shell 版本：

```shell
$ vi /etc/passwd
```

### 2.执行 shell

编写一个简单的 hello world：

```shell
#! /bin/sh
echo "hello world"
```

shell 脚本中用 # 表示注释，相当于 C 语言的 // 注释。

但如果 # 位于第一行开头，并且是 #!（称为Shebang）则例外，它表示该脚本使用后面指定的解释器 /bin/sh 解释执行。

接下来执行该脚本，有如下的几种执行方式：

#### 方式一：直接执行

可以给该脚本文件加上可执行权限然后执行：

```shell
$ chmod a+x test.sh
$ ./test.sh
```

#### 方式二：指定解释器执行

```shell
$ /bin/sh ./test.sh
```

以这种方式执行不需要test.sh文件具有可执行权限。

#### 方式三：使用 source 执行

```shell
$ source test.sh
```

#### 方式四：使用 . 执行

```shell
$ . test.sh
```

使用 source 或者 . 命令来执行脚本文件是直接在交互式的 shell 下逐行执行脚本中的命令。至于为什么，那么便得介绍下 shell 命令执行的过程了。

#### shell 命令执行过程

- 当用户在命令行输入命令后，一般情况下 shell 会 fork 出一个子进程，并且 exec 执行 shell 中的命令。

- 但是 shell 中的内建命令例外，执行内建命令相当于调用 shell 进程中的一个函数，并不创建新的进程。

内建命令虽然不创建新的进程，但也会有 Exit Status(虽然内建命令不创建新的进程，但执行结束后也会有一个状态码，也可以用特殊变量 $? 读出。) :

- 通常用 0 表示成功
- 非零表示失败


而 source 或者 . 命令是 shell 的内建命令，这种方式是不会创建子 shell 进程的，而是直接在交互式 shell下 逐行执行脚本中的命令。

注：查看 shell 中的内建命令的方式如下：

```shell
$ man bash-builtins
```

## 二、shell 语法结构 - 基础

### （一）、变量

shell 作为一种编程语言来说肯定是会有变量的定义的，但是却不同于一般的编程语言，shell对于变量的定义具有严格的格式限制。

- 所有的变量名必须为大写
- 变量名中只可以出现下划线
- 变量定义分为环境变量和本地变量
- 变量定义时等号左右没有空格（有空格会被当作命令和参数执行）

#### 1.环境变量

环境变量是可以由父进程传递给子进程的，因此 shell 进程中的环境变量可以从当前 shell 进程传给 fork 出来的子进程。

使用下面的命令可以显示当前 shell 进程中所有的环境变量：

```shell
$ printenv
```

#### 2.本地变量

本地变量只能存在于当前 shell 进程中。

使用下面的命令可以显示当前 shell 进程中所定义的所有变量（包括本地变量和环境变量）和函数：

```shell
$ set
```

环境变量是任何进程都有的概念，而本地变量是 shell 特有的概念。

#### 3.定义变量

在 shell 中，环境变量和本地变量的定义和用法基本一致。

在 shell 中定义或赋值一个本地变量：

```shell
$ VARNAME=value
```

该变量被定义好之后只是一个普通的本地变量，可以使用如下的命令将本地变量导出为环境变量：

```shell
$ export VARNAME
```

通常定义和导出环境变量可以一步完成：

```shell
$ export VARNAME=value
```

#### 4.删除变量

使用如下的命令来删除一个本地变量或者环境变量：

```shell
$ unset VARNAME
```

#### 5.获取变量的值

在 shell 中获取一个变量的时候不能直接用变量名，而是使用用 ${VARNAME}来表示它的值，也可以简写为 $VARNAME ：

```shell
echo $VARNAME
```

注意：

- 在定义变量时不用 $ ，取变量值时要用 $ 。
- 和 C 语言不同的是，shell 变量不需要明确定义类型。（事实上 shell 变量的值都是字符串，比如我们定义VAR=45，其实VAR的值是字符串45而非整数）
- shell 变量不需要先定义后使用，如果对一个没有定义的变量取值，则值为空字符串。

### （二）、文件名代换（Globbing）

在 shell 脚本中也是可以使用一些 **通配符（Wildcard）** 来匹配字符串的，shell 所支持的通配符如下：

- \*：匹配0个或多个任意字符
- ?：匹配一个任意字符
- [若干字符]：匹配方括号中任意一个字符的一次出现

其实这些通配符也就是正则表达式，可以实现对某一类字符串进行匹配。

```shell
$ ls /dev/ttyS*
$ ls ch0?.doc
$ ls ch0[0-2].doc
$ ls ch[012]   [0-9].doc
```

注意，Globbing 所匹配的文件名是由 shell 展开的，也就是说在参数还没传给程序之前已经展开了，比如上述 ls ch0[012].doc 命令，如果当前目录下有 ch00.doc 和 ch02.doc，则传给 ls 命令的参数实际上是这两个文件名，而不是一个匹配字符串。

### （三）、命令代换

如何在定义变量的时候将一个某个命令的执行结果赋值给该命令呢？

其实，由 **`** 反引号括起来的是一条命令，shell 先执行该命令，然后将输出结果立刻代换到当前命令行中。

例如定义一个变量存放 date 命令的输出：

```shell
$ DATE=`date`
$ echo $DATE
```

命令代换也可以用 **$()** 表示：

```shell
$ DATE=$(date)
```

### （四）、算术代换

#### 1.加减乘除运算

算术运算法 **$(())** 用于进行算术计算，$(()) 中的 shell 变量取值将转换成整数，同样含义的 **$[]** 等价例如：

```shell
$ VAR=45
$ echo $(($VAR+3))
```

$(()) 中只能用 +-*/ 和 () 运算符，并且只能做整数运算。

#### 2.进制表示

$[base#n],其中base表示进制,n 按照 base 进制解释，后面再有运算数，按十进制解释。

```shell
echo $[2#10+11]
echo $[8#10+11]
echo $[10#10+11]
```

### （五）、转义字符

和 C 语言类似，**\\** 在 shell 中被用作转义字符，用于去除紧跟其后的单个字符的特殊意义（回车除外），换句话说，紧跟其后的字符取字面值。例如：

```shell
$ echo $SHELL
# /bin/bash
$ echo \$SHELL
# $SHELL
$ echo \\
\
```

比如创建一个文件名为“$ $”的文件可以这样：

```shell
$ touch \$\ \$
```

还有一个字符虽然不具有特殊含义，但是要用它做文件名也很麻烦，就是 **-** 号。如果要创建一个文件名以-号开头的文件，这样是不行的：

```shell
$ touch -hello
# touch: invalid option -- h
# Try `touch --help' for more information.

# 即使加上\转义也还是报错：

$ touch \-hello
# touch: invalid option -- h
# Try `touch --help' for more information.
```

这是因为各种 UNIX 命令都把 - 号开头的命令行参数当作命令的选项，而不会当作文件名。如果非要处理以-号开头的文件名，可以有两种办法：

```shell
$ touch ./-hello
```

或者

```shell
$ touch -- -hello
```

**\\** 符号其实还有一种用法，在\后敲回车表示续行，Shell并不会立刻执行命令，而是把光标移到下一行，给出一个续行提示符>，等待用户继续输入，最后把所有的续行接到一起当作一个命令执行。例如：

```shell
itcast$ ls \
> -l
# （ls -l命令的输出）
```

### （六）、引号

#### 1.单引号

和 C 语言不一样，shell 脚本中的单引号和双引号一样都是字符串的界定符，而不是字符的界定符。

单引号用于保持引号内所有字符的字面值，即使引号内的\和回车也不例外，但是字符串中不能出现单引号。如果引号没有配对就输入回车，shell 会给出续行提示符，要求用户把引号配上对。例如：

```shell
$ echo '$SHELL'
# $SHELL
$ echo 'ABC\（回车）
> DE'
# （再按一次回车结束命令）
# ABC\
# DE
```

#### 2.双引号

被双引号用括住的内容，将被视为单一字串。它防止通配符扩展，但允许变量扩展。这点与单引号的处理方式不同

```shell
$ DATE=$(date)
$ echo "$DATE"
# $DATE
$ echo '$DATE'
# Sun Mar 31 12:37:40 UTC 2019
```

## 三、shell 语法结构 - 高级

### （一）、条件测试

在 shell 脚本中对某一个条件进行测试的命令是：**test 或者 [** ，该命令可以测试一个条件是否成立。

- 如果测试结果为真，则命令的Exit Status为 0
- 如果测试结果为假，则命令的Exit Status为 1（注意与C语言的逻辑表示正好相反）

例如测试两个数的大小关系：

```shell
wx@ubuntu:~/shell/01$ var=2
wx@ubuntu:~/shell/01$ test $var
wx@ubuntu:~/shell/01$ echo $?
0
wx@ubuntu:~/shell/01$ test $var -gt 5
wx@ubuntu:~/shell/01$ echo $?
1
wx@ubuntu:~/shell/01$ [ $var -gt 5 ]
wx@ubuntu:~/shell/01$ echo $?
1
wx@ubuntu:~/shell/01$ [ $var -gt 1 ]
wx@ubuntu:~/shell/01$ echo $?
0
```

虽然看起来很奇怪，但左方括号 /[ 确实是一个命令的名字，传给命令的各参数之间应该用空格隔开，比如，$VAR、-gt、3、] 是 /[ 命令的四个参数，它们之间必须用空格隔开。

命令 test 或 [ 的参数形式是相同的，只不过 test 命令不需要 ] 参数。

以 [ 命令为例，常见的测试命令如下所示：

```shell
[ -d DIR ]              如果DIR存在并且是一个目录则为真
[ -f FILE ]             如果FILE存在且是一个普通文件则为真
[ -z STRING ]           如果STRING的长度为零则为真
[ -n STRING ]           如果STRING的长度非零则为真
[ STRING1 = STRING2 ]   如果两个字符串相同则为真
[ STRING1 != STRING2 ]  如果字符串不相同则为真
[ ARG1 OP ARG2 ]        ARG1和ARG2应该是整数或者取值为整数的变量，OP是-eq（等于）-ne（不等于）-lt（小于）-le（小于等于）-gt（大于）-ge（大于等于）之中的一个
```

示例如下：

```shell
wx@ubuntu:~/shell/01$ mkdir test
wx@ubuntu:~/shell/01$ [ -d test ]
wx@ubuntu:~/shell/01$ echo $?
0
wx@ubuntu:~/shell/01$ [ -f test ]
wx@ubuntu:~/shell/01$ echo $?
1
wx@ubuntu:~/shell/01$ s1=hello
wx@ubuntu:~/shell/01$ s2=hello
wx@ubuntu:~/shell/01$ [ "$s1" = "$s2" ]
wx@ubuntu:~/shell/01$ echo $?
0
wx@ubuntu:~/shell/01$ s3=
wx@ubuntu:~/shell/01$ [ "$s1" = "$s3" ]
wx@ubuntu:~/shell/01$ echo $?
1
wx@ubuntu:~/shell/01$ [ "$s1" = $s3 ]
-bash: [: hello: unary operator expected
# 直接爆出语法错误，错误原因在于=右边需要一个字符串但是右边却是一个空，
# 也就是什么都没有，这样shell就会认为这是一个语法错误
# 正确的写法是在变量上加上双引号，这样展开也是一个空字符串
```

注意：

- 作为一种好的Shell编程习惯，应该总是把变量取值放在双引号之中。

shell 还和 c 语言一样也支持对测试条件之间进行 与、或、非 的逻辑运算：

```shell
# 非
[ ! EXPR ]          EXPR可以是上表中的任意一种测试条件，!表示逻辑反
# 与
[ EXPR1 -a EXPR2 ]  EXPR1和EXPR2可以是上表中的任意一种测试条件，-a表示逻辑与
# 或
[ EXPR1 -o EXPR2 ]  EXPR1和EXPR2可以是上表中的任意一种测试条件，-o表示逻辑或
```

例如：

```shell
wx@ubuntu:~/shell/01$ [ -d test -a "$var" = "abc" ]
wx@ubuntu:~/shell/01$ echo $?
1
wx@ubuntu:~/shell/01$ [ -d test -a "$var" = "test" ]
wx@ubuntu:~/shell/01$ echo $?
0
```

### （二）、分支语句

#### 1.if-else

和其他的该机语言类似，shell 中也使用**if、then、else、elif、fi**来进行分支执行控制，其中 fi 是 if 语句块的结束标志。其基本的格式如下：

```shell
if 条件1 ; then
    条件1满足时执行的代码（多行）
elif 条件2 ; then
    条件2满足时执行的代码（多行）
else
    不满足上述条件时执行的代码（多行）
fi
```

其中，**if 条件1 ; then** 是两条语句，所以中间得加上 ; 号，也建议使用这种方式书写。当然也可以分开写，这样就不需要加分号了。如下：

```shell
if 条件1
then 
    条件1满足时执行的代码（多行）
elif 条件2
then
    条件2满足时执行的代码（多行）
else
    不满足上述条件时执行的代码（多行）
fi
```

if 命令的参数组成一条子命令，如果该子命令的 Exit Status为0（表示真），则执行then后面的子命令，如果Exit Status非0（表示假），则执行 elif、else 或者 fi 后面的子命令。

if后面的子命令通常是测试命令，但也可以是其它命令。Shell 脚本没有 {} 括号，所以用 fi 表示 if 语句块的结束。

其中，**:** 是一个特殊的命令，称为空命令，该命令不做任何事，但Exit Status总是真。如下：

```shell
if : ; then
    echo "always true!"
fi
```

接下来用一个实例还演示下分支语句的用法：

```shell
#1 /bin/sh
  
# 根据用户输入的内容来判断当前是中午还是下午
echo "is it morning? please answer yes or no"
read YES_NO
if [ "$YES_NO" = "yes" ]; then
        echo "good morning!"
elif [ "$YES_NO" = "no" ]; then
        echo "good afternoon!"
else
        echo "sorry, $YES_NO not recongnized"
        exit 1
fi
exit 0
```

上例中的read命令的作用是等待用户输入一行字符串，将该字符串存到一个Shell变量中。

此外，Shell 还提供了 **&& 和 ||** 语法，和 C 语言类似，具有 Short-circuit 特性，很多 Shell 脚本喜欢写成这样：

```shell
test "$(whoami)" != 'root' && (echo you are using a non-privileged account; exit 1)
```

其中，&& 相当于 “if...then...” ，而 || 相当于 “if not...then...” 。

&& 和 || 用于连接两个命令，而上面讲的 -a 和 -o 仅用于在测试表达式中连接两个测试条件，要注意它们的区别，例如，

```shell
test "$VAR" -gt 1 -a "$VAR" -lt 3
```

和以下写法是等价的

```shell
test "$VAR" -gt 1 && test "$VAR" -lt 3
```

#### 2.case-in

case 命令类似与 C 语言中的 switch/case 语句，而 esac 则表示 case 语句块的结束。

C 语言中的 case 语句只能匹配整型或字符型常量、字符串等表达式，而 Shell 脚本的 case 可以匹配字符串和Wildcard，每个匹配分支可以有若干条命令，末尾必须以 **;;** 结束，执行时找到第一个匹配的分支并执行相应的命令，然后直接跳到 esac 之后，不需要像 C 语言一样用 break 跳出。其基本格式如下：

```shell
case 字符串 in
匹配字符串1)
    执行对应的命令（多条）
    ;;
匹配字符串2)
    执行对应的命令（多条）
    ;;
...
esac
```

在 case 的匹配字符串中支持通配符的使用。

例如，刚才上面的那个判断根据用户的输入来判断当前是否是早上或下午的例子可以使用 case in 语句块来实现：

```shell
#! /bin/sh
  
echo "is it morning? please answer yes or no!"
read YES_NO
case "$YES_NO" in
yes|y|Yes|Y|YES)
        echo "good morning!"
        ;;
[nN]*)
        echo "good afternoon!"
        ;;
*)
        echo "sorry,$YES_NO not recongnized. entry yes or no"
        exit 1
        ;;
esac
exit 0
```

### （三）、循环语句

#### 1.for循环

Shell 脚本的 for 循环结构和 C 语言很不一样，它类似于某些编程语言的foreach循环。其基本结构如下：

```shell
for 变量名 in 值1 值2 ... ; do
    执行语句（多句）
done
```

例如，遍历多个字符串，并将其打印出来：

```shell
#! /bin/sh
  
for FRUIT in apple banana pear; do
        echo "i like $FRUIT"
done
```

FRUIT 是一个循环变量，第一次循环 $FRUIT 的取值是 apple，第二次取值是 banana，第三次取值是 pear。

再比如，要将当前目录下的 chap0、chap1、chap2 等文件名改为 chap0~、chap1~、chap2~ 等（按惯例，末尾有~字符的文件名表示临时文件），这个命令可以这样写：

```shell
#! /bin/sh

for FILE_NAME in chap?; do
    mv $FILE_NAME $FILE_NAME~
done
```
#### 2.while循环

while 的用法和 C 语言类似。基本结构为：

```shell
while 条件判断式 ; do
    条件满足时执行的代码（多句）
done
```

比如一个验证密码的脚本：

```shell
#! /bin/sh
  
# 用户密码验证脚本
COUNTER=1
while [ "$COUNTER" -le 3 ]; do
        echo "entry password:"
        read PASS
        if [ "$PASS" = "mm123" ]; then
                echo "password is correct"
                exit 0
        else
                echo "password is wrong, please try again"
        fi
        COUNTER=$(($COUNTER+1))
done
echo "sorry"
exit 1
```

#### 3.break 和 continue

在 shell 循环中也支持 break 和 continue 命令。

- break[n] 可以指定跳出几层循环
- continue 跳过本次循环步，没跳出整个循环。

### （四）、位置参数和特殊变量

shell 当中有很多的特殊变量，其中都是以 $ 开头的。这些变量分别表示一些特殊的意义，以下是这些变量的整理：

```
$0  相当于 C 语言main函数的 argv[0]
$1、$2...    这些称为位置参数（Positional Parameter），相当于 C 语言 main 函数的 argv[1]、argv[2]...
$#  相当于 C 语言 main 函数的 argc - 1，注意这里的#后面不表示注释
$@  表示参数列表 "$1" "$2" ...，例如可以用在 for 循环中的 in 后面。
$*  表示参数列表 "$1" "$2" ...，同上
$?  上一条命令的 Exit Status
$$  当前进程号
```

以下使用一个例子来演示一下这些变量的作用：

```shell
! /bin/sh
  
echo "test---\$0"
echo "$0"
echo "test---\$1"
echo "$1"
echo "test---\$2"
echo "$2"
echo "test---\$3"
echo "$3"
echo "test---\$#"
echo "$#"
echo "test---\$@"
echo "$@"
echo "test---\$*"
echo "$*"
```

接下来进行执行：

```shell
wx@ubuntu:~/shell/01$ ./06.sh aa bb cc
test---$0
./06.sh
test---$1
aa
test---$2
bb
test---$3
cc
test---$#
3
test---$@
aa bb cc
test---$*
aa bb cc
```

另外的 $? 和 $$ 可以直接在命令行中直接执行

位置参数可以用 **shift** 命令 **左移**。

比如 shift 3 表示原来的 $4 现在变成 $1，原来的 $5 现在变成 $2 等等，原来的$1、$2、$3丢弃，$0不移动。

不带参数的 shift 命令相当于 shift 1。例如：

```shell
#! /bin/sh
  
echo "$*"
shift 1
echo "$*"
shift 2
echo "$*"
```

执行结果如下：

```shell
wx@ubuntu:~/shell/01$ ./06.sh aa bb cc dd ee
aa bb cc dd ee
bb cc dd ee
dd ee
```

### （五）、输入输出

#### 1.输出 - echo

echo 显示文本行或变量，或者把字符串输入到文件。命令结构：

```shell
echo [option] string
```
其中支持的选项：

- -e 解析转义字符
- -n 不回车换行。默认情况echo回显的内容后面跟一个回车换行。

例如：

```shell
wx@ubuntu:~/shell/01$ echo "hello\n\n"
hello\n\n
wx@ubuntu:~/shell/01$ echo -e "hello\n\n"
hello


wx@ubuntu:~/shell/01$ echo -n "hello"
hellowx@ubuntu:~/shell/01$ 
```

#### 2.输出 - 管道|

可以通过管道把一个命令的输出传递给另一个命令做输入。管道用竖线表示。

例如：

```shell
cat myfile | more
ls -l | grep "myfile"
# df -k 查看磁盘空间，找到第一列，去除“文件系统”，并输出
df -k | awk '{print $1}' | grep -v "文件系统"
```

#### 3.输出 - tee

tee 命令把结果输出到标准输出，另一个副本输出到相应文件。

例如：

```shell
# 将当前列表下的文件详细信息打印出来并保存一份到 a.txt 中
ll | tee a.txt
```

#### 4.输出 - 文件重定向

```shell
cmd > file             把标准输出重定向到新文件中
cmd >> file            追加
cmd > file 2>&1        标准出错也重定向到1所指向的file里
cmd >> file 2>&1
cmd < file1 > file2    输入输出都定向到文件里
cmd < &fd              把文件描述符fd作为标准输入
cmd > &fd              把文件描述符fd作为标准输出
cmd < &-               关闭标准输入
```

其中这里的 1 2 表示的是文件描述符：

```
文件描述符   对应的c语言库   描述
0          stdin         标准输入，键盘鼠标
1          stdout        标准输出，显示终端
2          stderr        标准错误输出，显示终端
```

#### 5.输入 - read

read 可以获取用户从标准输入中所输入的字符串，并将其保存到变量中去。其格式为：

```shell
read 变量
```

### （六）、函数

和 C 语言类似，Shell 中也有函数的概念，但是函数定义中没有返回值也没有参数列表。格式为：

```shell
# 函数定义
函数名(){
    执行代码
}

# 函数执行
函数名 变量1,变量2,...
```

例如：

```shell
#! /bin/sh
  
foo(){
        echo "function foo is called"
        echo "$*"
}

echo "---start---"
foo aa bb cc
echo "---stop---"
```

运行结果如下：

```shell
wx@ubuntu:~/shell/01$ ./08.sh
---start---
function foo is called
aa bb cc
---stop---
```

注意：函数体的左花括号 **{** 和后面的命令之间必须有空格或换行，如果将最后一条命令和右花括号 **}** 写在同一行，命令末尾必须有 **;** 号。

在定义 foo() 函数时并不执行函数体中的命令，就像定义变量一样，只是给 foo 这个名字一个定义，到后面调用 foo 函数的时候（注意Shell中的函数调用不写括号）才执行函数体中的命令。

Shell 脚本中的函数必须先定义后调用，一般把函数定义都写在脚本的前面，把函数调用和其它命令写在脚本的最后（类似 C 语言中的 main 函数，这才是整个脚本实际开始执行命令的地方）。

Shell 函数没有参数列表并不表示不能传参数，事实上，函数就像是迷你脚本，调用函数时可以传任意个参数，在函数内同样是用 $0、$1、$2 等变量来提取参数，函数中的位置参数相当于函数的局部变量，改变这些变量并不会影响函数外面的 $0、$1、$2 等变量。

函数中可以用 return 命令返回，如果 return 后面跟一个数字则表示函数的 Exit Status。

下面的这个脚本可以一次创建多个目录，各目录名通过命令行参数传入，脚本逐个测试各目录是否存在，如果目录不存在，首先打印信息然后试着创建该目录。

```shell
#! /bin/sh

is_directory()
{
  DIR_NAME=$1
  if [ ! -d $DIR_NAME ]; then
    return 1
  else
    return 0
  fi
}

for DIR in "$@"; do
  if is_directory "$DIR"
  then :
  else
    echo "$DIR doesn't exist. Creating it now..."
    mkdir $DIR > /dev/null 2>&1
    if [ $? -ne 0 ]; then
      echo "Cannot create directory $DIR"
      exit 1
    fi
  fi
done
```
注意is_directory()返回0表示真返回1表示假。