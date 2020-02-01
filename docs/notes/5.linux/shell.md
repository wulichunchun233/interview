# shell 脚本

目录：

- [一、shell 简介](#一shell简介)
    - [1.shell 版本](#1shell-版本)
    - [2.执行 shell](#2执行-shell)
        - [方式一：直接执行](#方式一直接执行)
        - [方式二：指定解释器执行](#方式二指定解释器执行)
        - [方式三：使用 source 执行](#方式三使用-source-执行)
        - [方式四：使用 . 执行](#方式四使用--执行)
        - [shell 命令执行过程](#shell-命令执行过程)
- [二、shell 语法结构 - 基础](二shell-语法结构---基础)
    - [（一）、变量](#一变量)
        - [1.环境变量](#1环境变量)
        - [2.本地变量](#2本地变量)
        - [3.定义变量](#3定义变量)
        - [4.删除变量](#4删除变量)
        - [5.获取变量的值](#5获取变量的值)
    - [（二）、文件名代换（Globbing）](#二文件名代换globbing)
    - [（三）、命令代换](#三命令代换)
    - [（四）、算术代换](#四算术代换)
        - [1.加减乘除运算](#1加减乘除运算)
        - [2.进制表示](#2进制表示)
    - [（五）、转义字符](#五转义字符)
    - [（六）、引号](#六引号)
        - [1.单引号](#1单引号)
        - [2.双引号](#2双引号)
- [三、shell 语法结构 - 高级](三shell-语法结构---高级)
    - [（一）、条件测试](#一条件测试)
    - [（二）、分支语句](#二分支语句)
        - [1.if-else](#1if-else)
        - [2.case-in](#2case-in)
    - [（三）、循环语句](#三循环语句)
        - [1.for循环](#1for循环)
        - [2.while循环](#2while循环)
        - [3.break 和 continue](#3break-和-continue)
    - [（四）、位置参数和特殊变量](#四位置参数和特殊变量)
    - [（五）、输入输出](#五输入输出)
        - [1.输出 - echo](#1输出---echo)
        - [2.输出 - 管道|](#2输出---管道)
        - [3.输出 - tee](#3输出---tee)
        - [4.输出 - 文件重定向](#4输出---文件重定向)
        - [5.输入 - read](#5输入---read)
    - [（六）、函数](#六函数)
    - [（七）、调试Shell脚本](#七调试Shell脚本)
- [四、shell 常用工具之grep](#四shell-常用工具之grep)
    - [（一）、作用](#一作用)
    - [（二）、格式](#二格式)
    - [（三）、主要参数](#三主要参数)
    - [（四）、grep命令使用简单实例](#四grep命令使用简单实例)
    - [（五）、grep命令使用复杂实例](五grep命令使用复杂实例)
- [五、shell 常用工具之find](#五shell-常用工具之find)
    - [（一）、find 命令格式](#一find命令格式)
        - [1.find命令格式](#1find命令格式)
        - [2.find命令参数](#2find命令参数)
        - [3.find命令选项](#3find命令选项)
        - [4.使用 exec 或 ok 来执行 shell 命令](#4使用-exec-或-ok-来执行-shell-命令)
    - [（二）、find 命令选项详解](#二find-命令选项详解)
        - [1、name选项](#1name选项)
        - [2、perm选项](#2perm选项)
        - [3、prune选项](#3prune选项)
        - [4、user 和 nouser 选项](#4-user-和-nouser-选项)
        - [5、group 和 nogroup 选项](#5-group-和-nogroup-选项)
        - [6、mtime、atime、ctime选项](#6mtimeatimectime选项)
        - [7、newer选项](#7newer选项)
        - [8、type选项](#8type选项)
        - [9、size选项](#9size选项)
        - [10、depth选项](#10depth选项)
        - [11、mount选项](#11mount选项)
    - [（三）、find 应用案例](#三find-应用案例)
- [六、shell 常用工具之xargs](#六shell-常用工具之xargs)
    - [（一）、xargs 与 exec](#一xargs-与-exec)
    - [（二）、xargs使用案例](#二xargs使用案例)
- [七、shell 常用工具之sed](#七shell-常用工具之sed)
    - [（一）、基本格式](#一基本格式)
    - [（二）、常用的sed命令](#二常用的sed命令)
- [八、shell 常用工具之awk](#八shell-常用工具之sed)
- [九、Linux命令总结](#九Linux命令总结)
- [十、shell习题训练](#十shell习题训练)


---

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

在 shell 中获取一个变量的时候不能直接用变量名，而是使用用 \${VARNAME}来表示它的值，也可以简写为 $VARNAME ：

```shell
echo $VARNAME
```

注意：

- 在定义变量时不用 \$ ，取变量值时要用 $ 。
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
$ ls \
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

被双引号用括住的内容，将被视为单一字串。它防止通配符扩展，但允许变量扩展。这点与单引号的处理方式不同，单引号只是表示字面量。而双引号可以对变量进行解释。

```shell
$ DATE=$(date)
$ echo '$DATE'
# $DATE
$ echo "$DATE"
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
#! /bin/sh
  
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

再比如，要将当前目录下的 chap0、chap1、chap2 等文件名改为 chap0\~、chap1\~、chap2\~ 等（按惯例，末尾有~字符的文件名表示临时文件），这个命令可以这样写：

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
- continue 跳过本次循环，没跳出整个循环。

### （四）、位置参数和特殊变量

shell 当中有很多的特殊变量，其中都是以 $ 开头的。这些变量分别表示一些特殊的意义，以下是这些变量的整理：

```shell
$0  相当于 C 语言main函数的 argv[0]
$1、$2...    这些称为位置参数（Positional Parameter），相当于 C 语言 main 函数的 argv[1]、argv[2]...
$#  相当于 C 语言 main 函数的 argc - 1，注意这里的#后面不表示注释
$@  表示参数列表 "$1" "$2" ...，例如可以用在 for 循环中的 in 后面。
$*  表示参数列表 "$1" "$2" ...，同上
$?  上一条命令的 Exit Status
$$  当前进程号
```

以下使用一个例子来演示一下这些变量的作用：

编写文件 06.sh，写入如下内容：

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
```

执行结果为：

```shell
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

另外的 \$? 和 \$\$ 可以直接在命令行中直接执行

位置参数可以用 **shift** 命令 **左移**。

比如 shift 3 表示原来的 \$4 现在变成 \$1，原来的 \$5 现在变成 \$2 等等，原来的\$1、\$2、\$3丢弃，\$0不移动。

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
cmd >> file 2>&1       标准出错追加
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

Shell 函数没有参数列表并不表示不能传参数，事实上，函数就像是迷你脚本，调用函数时可以传任意个参数，在函数内同样是用 $0、$1、$2 等变量来提取参数，函数中的位置参数相当于函数的局部变量，改变这些变量并不会影响函数外面的 \$0、\$1、\$2 等变量。

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
注意 is_directory() 返回 0 表示真返回 1 表示假。

### （七）、调试Shell脚本

shell 脚本提供了一些用于调试脚本的选项，如下所示：

- -n：读一遍脚本中的命令但不执行，用于检查脚本中的语法错误

- -v：一边执行脚本，一边将执行过的脚本命令打印到标准错误输出

- -x：提供跟踪执行信息，将执行的每一条命令和结果依次打印出来

使用这些选项有三种方法：

一是在命令行提供参数

```shell
$ sh -x ./script.sh
```

二是在脚本开头提供参数

```shell
#! /bin/sh -x
```

第三种方法是在脚本中用 set 命令启用或禁用参数

```shell
#! /bin/sh
if [ -z "$1" ]; then
  set -x
  echo "ERROR: Insufficient Args."
  exit 1
  set +x
fi
```

set -x 和 set +x 分别表示启用和禁用 -x 参数，这样可以只对脚本中的某一段进行跟踪调试

## 四、shell 常用工具之grep

### （一）、作用

Linux 系统中 grep 命令是一种强大的 **文本搜索工具**，它能使用 **正则表达式** 搜索文本，并把匹配的行打印出来。grep 全称是 Global Regular Expression Print（全局正则表达式打印），它的使用权限是所有用户。

grep 家族包括 **grep**、**egrep** 和 **fgrep**。egrep 和 fgrep 的命令只跟 grep 有很小不同。

- egrep 是 grep 的扩展，支持更多的 re 元字符。
- fgrep 就是 fixed grep 或 fast grep，它们把所有的字母都看作单词。也就是说，正则表达式中的元字符表示为其自身的字面意义，不再特殊。

linux 使用 GNU 版本的 grep。它功能更强，可以通过 -G、-E、-F 命令行选项来使用 egrep 和 fgrep 的功能。

### （二）、格式

grep 使用的话比较简单，基本格式如下：


```shell
grep [options] 正则表达式 文件名
```

注意：正则表达式可以拿双引号引起来，也可以不需要，但一般建议还是使用双引号引起来

### （三）、主要参数

```shell
grep --help

[options]主要参数：
-c：只输出匹配行的计数。
-i：不区分大小写。
-h：查询多文件时不显示文件名。
-l：查询多文件时只输出包含匹配字符的文件名。
-n：显示匹配行及行号。
-s：不显示不存在或无匹配文本的错误信息。
-v：显示不包含匹配文本的所有行。
--color=auto ：可以将找到的关键词部分加上颜色的显示。
```

pattern正则表达式主要参数：

```shell
\：忽略正则表达式中特殊字符的原有含义。
^：匹配正则表达式的开始行。
$: 匹配正则表达式的结束行。
\<：从匹配正则表达式的行开始。
\>：到匹配正则表达式的行结束。
[ ]：单个字符，如[A]即A符合要求 。
[ - ]：范围，如[A-Z]，即A、B、C一直到 Z 都符合要求 。
.：所有的单个字符。
*：所有字符，长度可以为0。
```

### （四）、grep命令使用简单实例

```shell
$ grep ‘test’ d*
显示所有以d开头的文件中包含 test 的行。

$ grep ‘test’ aa bb cc
显示在aa，bb，cc 文件中匹配 test 的行。

$ grep ‘[a-z]\{5\}’ aa
显示所有包含每个字符串至少有5个连续小写字符的字符串的行。

$ grep ‘w\(es\)t.*\1′ aa
如果 west 被匹配，则 es 就被存储到内存中，并标记为1，然后搜索任意个字符(.*)，这些字符后面紧跟着 另外一个es(\1)，找到就显示该行。如果用 egrep 或 grep -E，就不用”\”号进行转义，直接写成’w(es)t.*\1′就可以了。
```

### （五）、grep命令使用复杂实例

明确要求搜索子目录：

```shell
grep -r
```

或忽略子目录：

```shell
grep -d skip
```

如果有很多输出时，可以通过管道将其转到 ’less’ 上阅读：

```shell
$ grep magic /usr/src/Linux/Documentation/* | less
```

这样，就可以更方便地阅读。

有一点要注意，必需提供一个文件过滤方式(搜索全部文件的话用 *)。如果忘了，’grep’ 会一直等着，直到该程序被中断。如果遇到了这样的情况，按 ，然后再试。

下面还有一些有意思的命令行参数：

```shell
grep -i pattern files ：不区分大小写地搜索。默认情况区分大小写，
grep -l pattern files ：只列出匹配的文件名，
grep -L pattern files ：列出不匹配的文件名，
grep -w pattern files ：只匹配整个单词，而不是字符串的一部分(如匹配’magic’，而不是’magical’)，
grep -C number pattern files ：匹配的上下文分别显示[number]行，
grep pattern1 | pattern2 files ：显示匹配 pattern1 或 pattern2 的行，
例如：grep "abc\|xyz" testfile    表示过滤包含abc或xyz的行
grep pattern1 files | grep pattern2 ：显示既匹配 pattern1 又匹配 pattern2 的行。
grep -n pattern files  显示行号信息
grep -c pattern files  查找总行数
```

这里还有些用于搜索的特殊符号：

```shell
\< 和 \> 分别标注单词的开始与结尾。
例如：
grep man * 会匹配 ‘Batman’、’manic’、’man’等，
grep ‘\<man’ * 匹配’manic’和’man’，但不是’Batman’，
grep ‘\<man\>’ 只匹配’man’，而不是’Batman’或’manic’等其他的字符串。
‘^’：指匹配的字符串在行首，
‘$’：指匹配的字符串在行尾，
```
## 五、shell 常用工具之find

由于 find 具有强大的功能，所以它的选项也很多，其中大部分选项都值得我们花时间来了解一下。

即使系统中含有网络文件系统(NFS)，find 命令在该文件系统中同样有效，只要你具有相应的权限。

在运行一个非常消耗资源的 find 命令时，很多人都倾向于把它放在后台执行，因为遍历一个大的文件系统可能会花费很长的时间(这里是指30G字节以上的文件系统)。

### （一）、find 命令格式

#### 1.find命令格式

```shell
find pathname -options [-print -exec -ok ...]
```

#### 2.find命令参数

```shell
pathname：find命令所查找的目录路径。例如用.来表示当前目录，用/来表示系统根目录，递归查找。
-print： find命令将匹配的文件输出到标准输出。
-exec： find命令对匹配的文件执行该参数所给出的 shell 命令。相应命令的形式为 'command' {  } \;，注意 {   } 和 \；之间的空格。
-ok： 和-exec的作用相同，只不过以一种更为安全的模式来执行该参数所给出的 shell 命令，在执行每一个命令之前，都会给出提示，让用户来确定是否执行。
```

#### 3.find命令选项

```shell
-name   按照文件名查找文件。
-perm   按照文件权限来查找文件。
-prune  使用这一选项可以使find命令不在当前指定的目录中查找，如果同时使用-depth选项，那么-prune将被find命令忽略。
-user   按照文件属主来查找文件。
-group  按照文件所属的组来查找文件。
-mtime -n +n 按照文件的更改时间来查找文件，-n表示文件更改时间距现在n天以内，+n表示文件更改时间距现在n天以前。find命令还有-atime和-ctime 选项，但它们都和-m time选项。
-nogroup 查找无有效所属组的文件，即该文件所属的组在/etc/groups中不存在。
-nouser 查找无有效属主的文件，即该文件的属主在/etc/passwd中不存在。
-newer file1 ! file2 查找更改时间比文件file1新但比文件file2旧的文件。
-type   查找某一类型的文件，诸如：
    b - 块设备文件。
    d - 目录。
    c - 字符设备文件。
    p - 管道文件。
    l - 符号链接文件。
    f - 普通文件。
-size n：[c] 查找文件长度为n块的文件，带有c时表示文件长度以字节计。
-depth   在查找文件时，首先查找当前目录中的文件，然后再在其子目录中查找。
-fstype  查找位于某一类型文件系统中的文件，这些文件系统类型通常可以在配置文件/etc/fstab中找到，该配置文件中包含了本系统中有关文件系统的信息。
-mount   在查找文件时不跨越文件系统mount点。
-follow  如果find命令遇到符号链接文件，就跟踪至链接所指向的文件。
```

另外,注意下面三个 amin、cmin、mmin 的区别:

```shell
-amin n   查找系统中最后N分钟访问的文件
-atime n  查找系统中最后n*24小时访问的文件
-cmin n   查找系统中最后N分钟被改变文件状态的文件
-ctime n  查找系统中最后n*24小时被改变文件状态的文件
-mmin n   查找系统中最后N分钟被改变文件数据的文件
-mtime n  查找系统中最后n*24小时被改变文件数据的文件
```

#### 4.使用 exec 或 ok 来执行 shell 命令

使用 find 时，只要把想要的操作写在一个文件里，就可以用 exec 来配合 find 查找，方便在有些操作系统中只允许 -exec 选项执行诸如 ls 或 ls -l 这样的命令。大多数用户使用这一选项是为了查找旧文件并删除它们。建议在真正执行 rm 命令删除文件之前，最好先用 ls 命令看一下，确认它们是所要删除的文件。

exec 选项后面跟随着所要执行的命令或脚本，然后是**一对儿 {}，一个空格和一个\，最后是一个分号**。为了使用 exec 选项，必须要同时使用 print 选项。如果验证一下find命令，会发现该命令只输出从当前路径起的相对路径及文件名。

例如：为了用 ls -l 命令列出所匹配到的文件，可以把 ls -l 命令放在 find 命令的-exec选项中

```shell
find . -type f -exec ls -l {} \;
```

上面的例子中，find 命令匹配到了当前目录下的所有普通文件，并在 -exec 选项中使用 ls -l 命令将它们列出。

在 /logs 目录中查找更改时间在5日以前的文件并删除它们：

```shell
$ find /logs -type f -mtime +5 -exec rm {} \;
```

**记住：在shell中用任何方式删除文件之前，应当先查看相应的文件，一定要小心！当使用诸如 mv 或 rm 命令时，可以使用 -exec 选项的安全模式。它将在对每个匹配到的文件进行操作之前提示你。**

在下面的例子中， find命令在当前目录中查找所有文件名以 .conf 结尾、更改时间在5日以上的文件，并删除它们，只不过在删除之前先给出提示。

```shell
$ find . -name "*.conf"  -mtime +5 -ok rm {} \;
< rm ... ./conf/httpd.conf > ? n
```

按 y 键删除文件，按 n 键不删除。

任何形式的命令都可以在 -exec 选项中使用。

在下面的例子中我们在 -exec 选项中使用 grep 命令。find 命令首先匹配所有文件名为“ passwd*”的文件，例如 passwd、passwd.old、passwd.bak，然后执行 grep 命令看看在这些文件中是否存在一个 wx 用户。

```shell
find /etc -name "passwd*" -exec grep "wx" {} \;

wx:x:1000:1000::/home/wx:/bin/bash
```

### （二）、find 命令选项详解

#### 1、name选项

文件名选项是 find 命令最常用的选项，要么单独使用该选项，要么和其他选项一起使用。

可以使用某种文件名模式来匹配文件，记住要**用引号将文件名模式引起来**。

不管当前路径是什么，如果想要在自己的根目录 HOME 中查找文件名符合 *.txt 的文件，使用 ~ 作为 'pathname'参数，波浪号~ 代表了你的 HOME 目录。

```shell
$ find ~ -name "*.txt" -print
````

想要在当前目录及子目录中查找所有的‘ *.txt’文件，可以用：

```shell
$ find . -name "*.txt" -print
```

想要的当前目录及子目录中查找文件名以一个大写字母开头的文件，可以用：

```shell
$ find . -name "[A-Z]*" -print
```

想要在 /etc 目录中查找文件名以 host 开头的文件，可以用：

```shell
$ find /etc -name "host*" -print
```

想要查找 HOME 目录中的文件，可以用：

```shell
$ find ~ -name "*" -print 或 find ~ -print
```

要想让系统**高负荷运行**，就从根目录开始查找所有的文件：

```shell
$ find / -name "*" -print
```

如果想在当前目录查找文件名以两个小写字母开头，跟着是两个数字，最后是 .txt 的文件，下面的命令就能够返回例如名为 ax37.txt 的文件：

```shell
$find . -name "[a-z][a-z][0-9][0-9].txt" -print
```

#### 2、perm选项

按照文件权限模式进行查找使用 -perm 选项。按文件权限模式来查找文件的话，最好使用**八进制**的权限表示法。

如在当前目录下查找文件权限位为 755 的文件，即文件属主可以读、写、执行，其他用户可以读、执行的文件，可以用：

```shell
$ find . -perm 755 -print
```

还有一种表达方法：在八进制数字前面要加一个横杠 - ，表示都匹配，如 -007 就相当于777，-006 相当于666

```shell
ls -l
find . -perm 006
find . -perm -006
```

- -perm mode:文件许可正好符合mode
- -perm +mode:文件许可部分符合mode
- -perm -mode: 文件许可完全符合mode

#### 3、prune选项

如果在查找文件时希望忽略某个目录，因为你知道那个目录中没有你所要查找的文件，那么可以使用 -prune 选项来指出需要忽略的目录。在使用 -prune 选项时要当心，因为如果你同时使用了 -depth 选项，那么 -prune 选项就会被 find 命令忽略。

如果希望在 /apps 目录下查找文件，但不希望在 /apps/bin 目录下查找，可以用：

```shell
$ find /apps -path "/apps/bin" -prune -o -print
```

比如要在 /home/wx 目录下查找不在 dir1 子目录之内的所有文件

```shell
find /home/wx -path "/home/wx/dir1" -prune -o -print
```

避开多个文件夹

```shell
find /home \( -path /home/wx/f1 -o -path /home/wx/f2 \) -prune -o -print
```

**注意 ( 前的 \ ,注意 ( 后的空格**

#### 4、user 和 nouser 选项

按文件属主查找文件，如在 HOME 目录中查找文件属主为 wx 的文件，可以用：

```shell
$ find ~ -user wx -print
```

在 /etc 目录下查找文件属主为 uucp 的文件：

```shell
$ find /etc -user uucp -print
```

为了查找属主帐户已经被删除的文件，可以使用 -nouser 选项。这样就能够找到那些属主在 /etc/passwd 文件中没有有效帐户的文件。在使用 -nouser 选项时，不必给出用户名； find 命令能够为你完成相应的工作。

例如，希望在 /home 目录下查找所有的这类文件，可以用：

```shell
$ find /home -nouser -print
```

#### 5、group 和 nogroup 选项

就像 user 和 nouser 选项一样，针对文件所属于的用户组， find 命令也具有同样的选项，为了在 /apps 目录下查找属于 wx 用户组的文件，可以用：

```shell
$ find /apps -group wx -print
```

要查找没有有效所属用户组的所有文件，可以使用 nogroup 选项。下面的 find 命令从文件系统的根目录处查找这样的文件

```shell
$ find / -nogroup -print
```

#### 6、mtime、atime、ctime选项

如果希望按照更改时间来查找文件，可以使用 mtime,atime 或 ctime 选项。

如果系统突然没有可用空间了，很有可能某一个文件的长度在此期间增长迅速，这时就可以用 mtime 选项来查找这样的文件。

用减号 **-** 来限定更改时间在距今 n 日**以内**的文件，而用加号 **+** 来限定更改时间在距今 n 日**以前**的文件。

希望在系统根目录下查找更改时间在5日以内的文件，可以用：

```shell
$ find / -mtime -5 -print
```

为了在 /var/adm 目录下查找更改时间在3日以前的文件，可以用：

```shell
$ find /var/adm -mtime +3 -print
```

#### 7、newer选项

如果希望查找更改时间比某个文件新但比另一个文件旧的所有文件，可以使用 -newer 选项。它的一般形式为：

```
newest_file_name ! oldest_file_name
```

其中，！是逻辑非符号。

#### 8、type选项

在 /etc 目录下查找所有的目录，可以用：

```shell
$ find /etc -type d -print
```

在当前目录下查找除目录以外的所有类型的文件，可以用：

```shell
$ find . ! -type d -print
```

在 /etc 目录下查找所有的符号链接文件，可以用

```shell
$ find /etc -type l -print
```

#### 9、size选项

可以按照文件长度来查找文件，这里所指的文件长度既可以用块（block）来计量，也可以用字节来计量。以字节计量文件长度的表达形式为 Nc ；以块计量文件长度只用数字表示即可。

在按照文件长度查找文件时，一般使用这种以字节表示的文件长度，在查看文件系统的大小，因为这时使用块来计量更容易转换。 在当前目录下查找文件长度大于 1 M 字节的文件：

```shell
$ find . -size +1024000c -print
```

在 /home/apache 目录下查找文件长度恰好为100字节的文件：

```shell
$ find /home/apache -size 100c -print
```

在当前目录下查找长度超过10块的文件（一块等于512字节）：

```shell
$ find . -size +10 -print
```

#### 10、depth选项

在使用 find 命令时，可能希望先匹配所有的文件，再在子目录中查找。使用 depth 选项就可以使 find 命令这样做。这样做的一个原因就是，当在使用 find 命令向磁带上备份文件系统时，希望首先备份所有的文件，其次再备份子目录中的文件。

在下面的例子中， find 命令从文件系统的根目录开始，查找一个名为 CON.FILE 的文件。

它将首先匹配所有的文件然后再进入子目录中查找。

```shell
$ find / -name "CON.FILE" -depth -print
```

#### 11、mount选项

在当前的文件系统中查找文件（不进入其他文件系统），可以使用 find 命令的 mount 选项。

从当前目录开始查找位于本文件系统中文件名以 XC 结尾的文件：

```shell
$ find . -name "*.XC" -mount -print
```

### （三）、find 应用案例

1、查找当前用户主目录下的所有文件

下面两种方法都可以使用

```shell
$ find $HOME -print
$ find ~ -print
```

2、查找当前目录中文件属主具有读、写权限，并且文件所属组的用户和其他用户具有读权限的文件；

```shell
$ find . -type f -perm 644 -exec ls -l {} \;
```

3、查找系统中所有文件长度为0的普通文件，并列出它们的完整路径；

```shell
$ find / -type f -size 0 -exec ls -l {} \;
```

4、查找/var/logs目录中更改时间在7日以前的普通文件，并在删除之前询问它们；

```shell
$ find /var/logs -type f -mtime +7 -ok rm {} \;
```

5、查找系统中所有属于root组的文件；

```shell
$find . -group root -exec ls -l {} \;
```

6、删除当目录中访问时间在7日以来、含有数字后缀的 admin.log 文件。

```shell
$ find . -name "admin.log[0-9][0-9][0-9]" -atime -7  -ok rm {} \;
```

7、查找当前文件系统中的所有目录并排序；

```shell
$ find . -type d | sort
```

## 六、shell 常用工具之xargs

xargs -- construct argument list(s) and execute utility

### （一）、xargs 与 exec

xargs 和 exec 的作用基本一致，都是在 find 命令之后执行相应的命令，但不同之处在于**exec会等到 find 执行完毕之后在去运行，而 xargs 则是 find 产生一部分结果就直接去执行相应的命令**。

在使用 find 命令的 -exec 选项处理匹配到的文件时， find 命令将所有匹配到的文件一起传递给 exec 执行。但有些系统对能够传递给 exec 的命令长度有限制，这样在 find 命令运行几分钟之后，就会出现溢出错误。错误信息通常是“参数列太长”或“参数列溢出”。这就是 xargs 命令的用处所在，特别是与 find 命令一起使用。

find 命令把匹配到的文件传递给 xargs 命令，而 **xargs 命令每次只获取一部分文件而不是全部**，不像 -exec 选项那样。这样它可以先处理最先获取的一部分文件，然后是下一批，并如此继续下去。

在有些系统中，使用 -exec 选项会为处理每一个匹配到的文件而发起一个相应的进程，并非将匹配到的文件全部作为参数一次执行；这样在有些情况下就会出现进程过多，系统性能下降的问题，因而效率不高；

而使用 xargs 命令则只有一个进程。另外，在使用 xargs 命令时，究竟是一次获取所有的参数，还是分批取得参数，以及每一次获取参数的数目都会根据该命令的选项及系统内核中相应的可调参数来确定。

### （二）、xargs使用案例

来看看 xargs 命令是如何同 find 命令一起使用的，并给出一些例子。

下面的例子查找系统中的每一个普通文件，然后使用 xargs 命令来测试它们分别属于哪类文件

```shell
$ find . -type f -print | xargs file
```

在当前目录下查找所有用户具有读、写和执行权限的文件，并收回相应的写权限：

```shell
$ ls -l
$ find . -perm -7 -print | xargs chmod o-w
$ ls -l
```

上面的例子其实和下面的命令作用一样：

```shell
$ find . -perm -7 -print -exec chmod o-w {} \;
```
用 grep 命令在所有的普通文件中搜索 hello 这个词：

```shell
find . -type f -print | xargs grep "hello"
```

用 grep 命令在当前目录下的所有普通文件中搜索 hello 这个词：

```shell
find . -name \* -type f -print | xargs grep "hello"
```

注意，在上面的例子中， \ 用来取消find命令中的*在shell中的特殊含义。

**find 命令配合使用 exec 和 xargs 可以使用户对所匹配到的文件执行几乎所有的命令。**

## 七、shell 常用工具之sed

sed ：流编辑器（Stream Editor），在 Shell 脚本和 Makefile 中作为过滤器使用非常普遍，也就是把前一个程序的输出引入 sed 的输入，经过一系列编辑命令转换为另一种格式输出。

sed 和 vi 都源于早期 UNIX 的 ed 工具，所以很多 sed 命令和 vi 的末行命令是相同的。

### （一）、基本格式

sed 命令行的基本格式为

```
sed option 'script' file1 file2 ...
sed option -f scriptfile file1 file2 ...
```

选项含义：

- --version            显示sed版本。
- --help               显示帮助文档。
- -n,--quiet,--silent  静默输出，默认情况下，sed程序在所有的脚本指令执行完毕后，将自动打印模式空间中的内容，这些选项可以屏蔽自动打印。
- -e script            允许多个脚本指令被执行。
- -f script-file, 
- --file=script-file   从文件中读取脚本指令，对编写自动脚本程序来说很棒！
- -i,--in-place        直接修改源文件，经过脚本指令处理后的内容将被输出至源文件（源文件被修改）慎用！
- -l N, --line-length=N 该选项指定l指令可以输出的行长度，l指令用于输出非打印字符。
- --posix             禁用GNU sed扩展功能。
- -r, --regexp-extended  在脚本指令中使用扩展正则表达式
- -s, --separate      默认情况下，sed将把命令行指定的多个文件名作为一个长的连续的输入流。而GNU sed则允许把他们当作单独的文件，这样如正则表达式则不进行跨文件匹配。
- -u, --unbuffered    最低限度的缓存输入与输出。


以上仅是 sed 程序本身的选项功能说明。

下面简单介绍几个sed脚本指令操作的例子。

```
a,append        追加
i,insert        插入
d,delete        删除
s,substitution  替换
```

例1：

```shell
$ sed "2a test" ./testfile
``` 
效果：在输出 testfile 内容的第二行后添加"test"。

例2：

```shell
$ sed "2,5d" testfile
```
效果：删除 testfile 文件第2行到第五5行的内容。

注意，**sed 命令不会修改原文件，删除命令只表示某些行不打印输出，而不是从原文件中删去。**

sed 处理的文件既可以由标准输入重定向得到，也可以当命令行参数传入，命令行参数可以一次传入多个文件，sed 会依次处理。

sed 的编辑命令可以直接当命令行参数传入，也可以写成一个脚本文件然后用 -f 参数指定，编辑命令的格式为

```
/pattern/action
```

其中 pattern 是正则表达式，action 是编辑操作。sed 程序一行一行读出待处理文件，如果某一行与 pattern 匹配，则执行相应的 action，如果一条命令没有 pattern 而只有 action，这个 action 将作用于待处理文件的每一行。

### （二）、常用的sed命令

```
/pattern/p  打印匹配pattern的行
/pattern/d  删除匹配pattern的行
/pattern/s/pattern1/pattern2/   查找符合pattern的行，将该行第一个匹配pattern1的字符串替换为pattern2
/pattern/s/pattern1/pattern2/g  查找符合pattern的行，将该行所有匹配pattern1的字符串替换为pattern2
```

使用 p 命令需要注意，sed 是把待处理文件的内容连同处理结果一起输出到标准输出的，因此 p 命令表示除了把文件内容打印出来之外还额外打印一遍匹配pattern 的行。比如一个文件 testfile 的内容是

```
123
abc
456
```

打印其中包含abc的行

```shell
$ sed '/abc/p' testfile
```

终端输出结果为：

```
123
abc
abc
456
```

要想只输出处理结果，应加上 -n 选项，这种用法相当于 grep 命令

```shell
$ sed -n '/abc/p' testfile
```

终端输出结果为：

```
abc
```

使用 d 命令就不需要 -n 参数了，比如删除含有 abc 的行

```shell
$ sed '/abc/d' testfile
```

终端输出结果为：

```
123
456
```

使用查找替换命令时，可以把匹配 pattern1 的字符串复制到 pattern2 中，比如：

```shell
$ sed 's/bc/-&-/' testfile
```

终端输出结果为：

```
123
a-bc-
456
```

pattern2 中的 & 表示原文件的当前行中与 pattern1 相匹配的字符串

再比如：

```shell
$ sed 's/\([0-9]\)\([0-9]\)/-\1-~\2~/' testfile
```

终端输出结果为：

```
-1-~2~3
abc
-4-~5~6
```

pattern2 中的 \1 表示与 pattern1 的第一个()括号相匹配的内容，\2表示与 pattern1 的第二个()括号相匹配的内容。sed 默认使用 Basic 正则表达式规范，如果指定了 -r 选项则使用 Extended 规范，那么()括号就不必转义了。

```shell
$ sed  's/yes/no/;s/static/dhcp/'  ./testfile
```

注：使用分号隔开指令。

```shell
$ sed -e 's/yes/no/' -e 's/static/dhcp/' testfile
```

注：使用-e选项。


如果 testfile 的内容是

```
<html><head><title>Hello World</title></head>
<body>Welcome to the world of regexp!</body></html>
```

现在要去掉所有的HTML标签，使输出结果为

```
Hello World
Welcome to the world of regexp!
```

怎么做呢？如果用下面的命令

```shell
$ sed 's/<.*>//g' testfile
```

结果是两个空行，把所有字符都过滤掉了。这是因为，正则表达式中的数量限定符会匹配尽可能长的字符串，这称为贪心的(Greedy)。

比如 sed 在处理第一行时，<.*> 匹配的并不是或这样的标签，而是

```
<html><head><title>Hello World</title>
```

正确的正则应该这么写：

```
$ sed 's/<[^>]*>//g' testfile
```

## 八、shell 常用工具之awk

sed 主要以**行**为单位来对文件进行处理，而需要对文件按照**列**来进行处理的话就得需要使用 awk 命令。

awk 命令比 sed 命令强的地方在于不仅能以**行**为单位还能以**列**为单位来对文件进行处理。

awk 缺省的行分隔符是**换行**，缺省的列分隔符是连续的**空格和 Tab**，但是具体的行分隔符和列分隔符都支持自定义。

awk实际上是一门很复杂的脚本语言，还有像 C 语言一样的分支和循环结构，但是基本用法和 sed 类似，awk 命令的基本形式为：

```shell
awk option 'script' file1 file2 ...
awk option -f scriptfile file1 file2
```

和 sed 类似，awk处理的文件既可以由标准输入重定向得到，也可以当命令行参数传入，编辑命令可以直接当命令行参数传入，也可以用 -f 参数指定一个脚本文件，编辑命令的格式为：

```shell
/pattern/{actions}
condition{actions}
```

和 sed 类似，pattern 是正则表达式，actions 是一系列的操作。awk 程序一行一行读出待处理文件，如果某一行与 pattern 匹配，或者满足 condition 条件，则执行相应的 actions，如果一条 awk 命令只有 actions 部分，则 actions 用于待处理文件的每一行。

比如一个 testfile 文件中的内容如下所示：

```
ProductA  30
ProductB  76
ProductC  55
```

则如下的命令可以打印出该文件中每一行中的第二列中的内容：

```shell
$ awk '{print $2;}' testfile
```

其中：

- $0表示当前整个行
- $1表示第一列
- $2表示第二列

依次类推。

同时，awk也支持按照某种条件来执行相应的操作。比如，下面的脚本便将 testfile 中产品库存低于75的在行末标注需要订货：

```shell
$ awk '$2<75 {printf "%s\t%s\n", $0, "REORDER";} $2>=75 {print $0;} testfile'
```

可以看到 awk 也有和 c 语言非常相似的 print 函数。

awk 命令的 condition 部分还可以是两个特殊的 condition **BEGIN 和 END**，对于每个待处理文件，BEGIN后面的 actions 在处理整个文件之前就已经执行了一次，而 END 后面的 actions 在整个文件处理完毕之后还需要在执行一次。

并且 awk 还可以像 C 语言一样来使用变量，但是并不需要定义变量。比如如下的命令来统计一个文件中所有的空行的行数：

```shell
$ awk '/^ *$/ {x=x+1;} END {print x;}' testfile
```

其中，x不需要进行定义，直接使用，初始值为0

awk 中也有一些预先已经定义好的变量，这些变量都具有相应的特殊含义：

- FILENAME  当前输入文件的文件名，该变量是只读的
- NR  当前行的行号，该变量是只读的，R代表record
- NF  当前行所拥有的列数，该变量是只读的，F代表field
- OFS 输出格式的列分隔符，缺省是空格
- FS  输入文件的列分融符，缺省是连续的空格和Tab
- ORS 输出格式的行分隔符，缺省是换行符
- RS  输入文件的行分隔符，缺省是换行符

比如 /etc/passwd 文件的每一行有若干个字段，字段之间以 : 分隔，就可以重新定义 awk 的列分隔符为 : 并以列为单位处理这个文件。比如，如下的命令便可以打印 /etc/passwd 中的用户账号列表：

```shell
$ awk 'BEGIN {FS=":"} {print $1;}' /etc/passwd
```

## 九、linux命令总结

针对 linux 进行运维调度的常见命令如下图所示：

![linux](/assets/images/linux/3.png)

其中关于这些命令的详细使用方式可以从这个网站中进行查找：[Linux工具快速教程](http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/index.html)

由于日常工作中经常涉及到一些对文件进行处理的命令，这里介绍下一些比较常用的对文件进行处理的命令：

wc

```
wc [option] [file]...
    -l: 统计行数
    -c: 统计字节数
    -w；统计单词数
```

tr

```
tr: 转换字符或删除字符
    tr '集合1' '集合2'
    tr -d '字符集合'
```

cut

```
This is a test line.
-d字符：指定分隔符
-f#: 指定要显示字段
    单个数字：一个字段
    逗号分隔的多个数字：指定多个离散字段
    -：连续字段，如3-5；
```

sort

```
按字符进行比较
sort [option] file...
    -f: 忽略字符大小写；
    -n: 比较数值大小；
    -t: 指定分隔符
    -k: 指定分隔后进行比较字段
    -u: 重复的行，只显示一次；
```

uniq

```
移除重复的行
-c：显示每行重复的次数
-d：仅显示重复过的行
-u: 仅显示不曾重复的行
```

## 十、shell习题训练

1.求2个数之和

```shell
#! /bin/sh
  
a=1
b=2
echo $(($a+$b))
```
2.计算1-100的和

```shell
#! /bin/sh

index=1
sum=0

while [ "$index" -le 100 ]; do
        sum=$(($sum+$index))
        index=$(($index+1))
done    
echo $sum
```

3.将一目录下所有的文件的扩展名改为bak

4.编译当前目录下的所有.c文件：

5.打印root可以使用可执行文件数，处理结果: root's bins: 2306

6.打印当前sshd的端口和进程id，处理结果: sshd Port&&pid: 22 5412

7.输出本机创建20000个目录所用的时间，处理结果:

```
real    0m3.367s
user    0m0.066s
sys     0m1.925s
```

8.打印本机的交换分区大小，处理结果: Swap:1024M

9.文本分析，取出/etc/password中shell出现的次数

```
第一种方法结果:
      4 /bin/bash
      1 /bin/sync
      1 /sbin/halt
     31 /sbin/nologin
      1 /sbin/shutdown
第二种方法结果:
        /bin/sync       1
        /bin/bash       1
        /sbin/nologin   30
        /sbin/halt      1
        /sbin/shutdown  1
```

10.文件整理，employee文件中记录了工号和姓名,（提示join）
```
employee.txt:
    100 Jason Smith 
    200 John Doe 
    300 Sanjay Gupta 
    400 Ashok Sharma 
    bonus文件中记录工号和工资
bonus.txt:
    100 $5,000 
    200 $500 
    300 $3,000 
    400 $1,250 

要求把两个文件合并并输出如下，处理结果:

    400 ashok sharma $1,250
    100 jason smith  $5,000
    200 john doe  $500
    300 sanjay gupta  $3,000
```
11.写一个shell脚本来得到当前的日期，时间，用户名和当前工作目录。

12.编写shell脚本获取本机的网络地址。

13.编写个shell脚本将当前目录下大于10K的文件转移到/tmp目录下

14.编写一个名为myfirstshell.sh的脚本，它包括以下内容。

```
a) 包含一段注释，列出您的姓名、脚本的名称和编写这个脚本的目的。
b) 问候用户。
c) 显示日期和时间。
d) 显示这个月的日历。
e) 显示您的机器名。
f) 显示当前这个操作系统的名称和版本。
g) 显示父目录中的所有文件的列表。
h) 显示root正在运行的所有进程。
i) 显示变量TERM、PATH和HOME的值。
j) 显示磁盘使用情况。
k) 用id命令打印出您的组ID。
m) 跟用户说“Good bye”
```

15.文件移动拷贝，有m1.txt m2.txt m3.txt m4.txt，分别创建出对应的目录，m1 m2 m3 m4 并把文件移动到对应的目录下

16.root用户今天登陆了多长时间

17.终端输入一个文件名，判断是否是设备文件

18.统计IP访问：要求分析apache访问日志，找出访问页面数量在前100位的IP数。日志大小在78M左右。以下是apache的访问日志节选

```
202.101.129.218 - - [26/Mar/2006:23:59:55 +0800] "GET /online/stat_inst.php?pid=d065 HTTP/1.1" 302 20-"-" "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
```

19.设计一个Shell程序，在/userdata目录下建立50个目录，即user1～user50，并设置每个目录的权限，其中其他用户的权限为：读；文件所有者的权限为：读、写、执行；文件所有者所在组的权限为：读、执行。

20.设计一个shell程序，添加一个新组为class1，然后添加属于这个组的30个用户，用户名的形式为stdxx，其中xx从01到30，并设置密码为对应的stdxx。

21.编写shell程序，实现自动删除30个账号的功能。账号名为std01至std30。

22.用户清理,清除本机除了当前登陆用户以外的所有用户

23.设计一个shell程序，在每月第一天备份并压缩/etc目录的所有内容，存放在/root/bak目录里，且文件名,为如下形式yymmdd_etc，yy为年，mm为月，dd为日。Shell程序fileback存放在/usr/bin目录下。

24.对于一个用户日志文件，每行记录了一个用户查询串，长度为1-255字节，共几千万行，请排出查询最多的前100条。 日志可以自己构造。 (提示：awk sort uniq head)

25.编写自己的ubuntu环境安装脚本

26.编写服务器守护进程管理脚本。

27.查看TCP连接状态

```
netstat -nat |awk ‘{print $6}’|sort|uniq -c|sort -rn

netstat -n | awk ‘/^tcp/ {++S[$NF]};END {for(a in S) print a, S[a]}’ 或
netstat -n | awk ‘/^tcp/ {++state[$NF]}; END {for(key in state) print key,"\t",state[key]}’
netstat -n | awk ‘/^tcp/ {++arr[$NF]};END {for(k in arr) print k,"t",arr[k]}’

netstat -n |awk ‘/^tcp/ {print $NF}’|sort|uniq -c|sort -rn

netstat -ant | awk ‘{print $NF}’ | grep -v ‘[a-z]‘ | sort | uniq -c
```

28.查找请求数请20个IP（常用于查找攻来源）：

```
netstat -anlp|grep 80|grep tcp|awk ‘{print $5}’|awk -F: ‘{print $1}’|sort|uniq -c|sort -nr|head -n20

netstat -ant |awk ‘/:80/{split($5,ip,":");++A[ip[1]]}END{for(i in A) print A[i],i}’ |sort -rn|head -n20
```

29.用tcpdump嗅探80端口的访问看看谁最高

```
tcpdump -i eth0 -tnn dst port 80 -c 1000 | awk -F"." ‘{print $1"."$2"."$3"."$4}’ | sort | uniq -c | sort -nr |head -20
```

30.查找较多time_wait连接

```
netstat -n|grep TIME_WAIT|awk ‘{print $5}’|sort|uniq -c|sort -rn|head -n20
```

31.找查较多的SYN连接

```
netstat -an | grep SYN | awk ‘{print $5}’ | awk -F: ‘{print $1}’ | sort | uniq -c | sort -nr | more
```

32.根据端口列进程

```
netstat -ntlp | grep 80 | awk ‘{print $7}’ | cut -d/ -f1
```

33.获得访问前10位的ip地址

```
cat access.log|awk ‘{print $1}’|sort|uniq -c|sort -nr|head -10
cat access.log|awk ‘{counts[$(11)]+=1}; END {for(url in counts) print counts[url], url}’
```

34.访问次数最多的文件或页面,取前20

```
cat access.log|awk ‘{print $11}’|sort|uniq -c|sort -nr|head -20
```

35.列出传输最大的几个exe文件（分析下载站的时候常用）

```
cat access.log |awk ‘($7~/.exe/){print $10 " " $1 " " $4 " " $7}’|sort -nr|head -20
```

36.列出输出大于200000byte(约200kb)的exe文件以及对应文件发生次数

```
cat access.log |awk ‘($10 > 200000 && $7~/.exe/){print $7}’|sort -n|uniq -c|sort -nr|head -100
```

37.如果日志最后一列记录的是页面文件传输时间，则有列出到客户端最耗时的页面

```
cat access.log |awk ‘($7~/.php/){print $NF " " $1 " " $4 " " $7}’|sort -nr|head -100
```

38.列出最最耗时的页面(超过60秒的)的以及对应页面发生次数

```
cat access.log |awk ‘($NF > 60 && $7~/.php/){print $7}’|sort -n|uniq -c|sort -nr|head -100
```

39.列出传输时间超过 30 秒的文件

```
cat access.log |awk ‘($NF > 30){print $7}’|sort -n|uniq -c|sort -nr|head -20
```

40.统计网站流量（G)

```
cat access.log |awk ‘{sum+=$10} END {print sum/1024/1024/
1024}’
```

41.统计404的连接

```
awk ‘($9 ~/404/)’ access.log | awk ‘{print $9,$7}’ | sort
```

42.统计http status

```
cat access.log |awk ‘{counts[$(9)]+=1}; END {for(code in counts) print code, counts[code]}'
cat access.log |awk '{print $9}'|sort|uniq -c|sort -rn
```

43.蜘蛛分析，查看是哪些蜘蛛在抓取内容。

```
/usr/sbin/tcpdump -i eth0 -l -s 0 -w - dst port 80 | strings | grep -i user-agent | grep -i -E 'bot|crawler|slurp|spider'
```

44.创建一个用户mandriva，其ID号为2002，基本组为distro（组ID为3003），附加组为linux；

```
# groupadd linux
# groupadd -g 3003 distro
# useradd -u 2002 -g distro -G linux mandriva
```

45.创建一个用户fedora，其全名为Fedora Community，默认shell为tcsh；

``` 
# useradd -c "Fedora Community" -s /bin/tcsh fedora
```

46.修改mandriva的ID号为4004，基本组为linux，附加组为distro和fedora；

```
# usermod -u 4004 -g linux -G distro,fedora mandriva
```

47.给fedora加密码，并设定其密码最短使用期限为2天，最长为50天；

```
# passwd fedora
# chage -m 2 -M 50 fedora
```

48.调试命令

```
strace -p pid
```

49.写一个脚本

```
1、创建一个组newgroup, id号为4000；
2、创建一个用户mageedu1, id号为3001，附加组为newgroup；
3、创建目录/tmp/hellodirxyz
4、复制/etc/fstab至上面的目录中
5、改变目录及内部文件的属主和属组为mageedu1;
6、让目录及内部文件的其它用户没有任何权限；

        #!/bin/bash
        # Description:
        # Version:
        # Datetime:
        # Author:

        myGroup="newgroup1"
        myUser="mageedu2"
        myDir="/tmp/hellodirxyz1"
        myID=3002

        groupadd -g 4001 $myGroup
        useradd -u $myID -G $myGroup $myUser
        mkdir $myDir
        cp /etc/fstab $myDir
        chown -R $myUser:$myUser $myDir
        chmod -R o= $myDir

        unset myGroup myUser myID myDir
```

50.统计/bin、/usr/bin、/sbin和/usr/sbin等各目录中的文件个数；

```
# ls /bin | wc -l
```

51.显示当前系统上所有用户的shell，要求，每种shell只显示一次；

```
# cut -d: -f7 /etc/passwd | sort -u
```

52.取出/etc/passwd文件的第7行；

```
# head -7 /etc/passwd | tail -1
```

53.显示第3题中取出的第7行的用户名；

```
# head -7 /etc/passwd | tail -1 | cut -d: -f1

# head -7 /etc/passwd | tail -1 | cut -d: -f1 | tr 'a-z' 'A-Z'
```

54.统计/etc目录下以P或p开头的文件个数；

```
# ls -d /etc/[Pp]* | wc -l
```

55.写一个脚本，用for循环实现显示/etc/init.d/functions、/etc/rc.d/rc.sysinit和/etc/fstab各有多少行；

```
for fileName in /etc/init.d/functions /etc/rc.d/rc.sysinit /etc/fstab; do
    wc -l $fileName
done

#!/bin/bash
for fileName in /etc/init.d/functions /etc/rc.d/rc.sysinit /etc/fstab; do
    lineCount=`wc -l $fileName | cut -d' ' -f1`
    echo "$fileName: $lineCount lines."
done

#!/bin/bash
for fileName in /etc/init.d/functions /etc/rc.d/rc.sysinit /etc/fstab; do
    echo "$fileName: `wc -l $fileName | cut -d' ' -f1` lines."
done
```

56.写一个脚本,将上一题中三个文件的复制到/tmp目录中；用for循环实现，分别将每个文件的最近一次的修改时间改为2016年12月15号15点43分；

```
for fileName in /etc/init.d/functions /etc/rc.d/rc.sysinit /etc/fstab; do
    cp $fileName /tmp
    baseName=`basename $fileName`
    touch -m -t 201109151327 /tmp/$baseName
done
```

57.写一个脚本, 显示/etc/passwd中第3、7和11个用户的用户名和ID号；

```
for lineNo in 3 7 11; do
    userInfo=`head -n $lineNo /etc/passwd | tail -1 | cut -d: -f1,3`
    echo -e "User: `echo $userInfo | cut -d: -f1`\nUid: `echo $userInfo |cut -d: -f2`"
done
```

58.显示/proc/meminfo文件中以大小写s开头的行；

```
# grep "^[sS]" /proc/meminfo
# grep -i "^s" /proc/meminfo
```

59.取出默认shell为非bash的用户；

```
# grep -v "bash$" /etc/passwd | cut -d: -f1
```

60.取出默认shell为bash的且其ID号最大的用户；

```
# grep "bash$" /etc/passwd | sort -n -t: -k3 | tail -1 | cut -d: -f1
```

61.显示/etc/rc.d/rc.sysinit文件中，以#开头，后面跟至少一个空白字符，而后又有至少一个非空白字符的行；

```
# grep "^#[[:space:]]\{1,\}[^[:space:]]\{1,\}" /etc/rc.d/rc.sysinit
```

62.显示/boot/grub/grub.conf中以至少一个空白字符开头的行；

```
# grep "^[[:space:]]\{1,\}[^[:space:]]\{1,\}" /boot/grub/grub.conf
```

63.找出/etc/passwd文件中一位数或两位数；

```
# grep --color=auto "\<[0-9]\{1,2\}\>" /etc/passwd
```

64.找出ifconfig命令结果中的1到255之间的整数；

```
# ifconfig | grep -E --color=auto "\<([1-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\>"
```

65.查看当前系统上root用户的所有信息;

```
# grep "^root\>" /etc/passwd
```

66.添加用户bash和testbash、basher，而后找出当前系统上其用户名和默认shell相同的用户；

```
# grep --color=auto "^\([[:alnum:]]\{1,\}\)\>.*\1$" /etc/passwd
```

67.找出netstat -tan命令执行的结果中以“LISTEN”或“ESTABLISHED”结尾的行；

68.取出当前系统上所有用户的shell，要求：每种shell只显示一次，且按升序显示；

```
# cut -d: -f7 /etc/passwd | sort -u
```
