# python 语法总结 - 高级部分

## 一、面向对象进阶

### 1.元类

首先得明确一点：在 python 中，类是一组用来描述如何生成一个对象的代码段，因此类是一种对象。

因为类是一种对象，当使用 class 关键字时， python 解释器便会自动的创建这个对象，因此可以在运行时动态的创建它们。

```python
def choose_class(name):
    if name == 'foo':
        class Foo(object):
            pass
        return Foo# 返回的是类对象，不是类的实例
# 创建的是类对象，而不是类的实例，可以通过类对象来创建类的实例
foo_class = choose_class('foo')
```

在 python 中使用 type() 方法来查看一个类的类型的时候会返回 type 类型，也就是说类对象的类型是 type。并且 type 方法还有另外一种功能便是可以动态的创建类。

```python
type(类名, 由父类名称组成的元祖, 包含类属性、方法的字典)
```
可以通过上面的这种方式手动的创建一个类。

```python
Foo = type('Foo', (), {})
foo_class = Foo()
```

可以看到，在 python 中，类也是对象，你可以动态的去创建一个类。这就是当使用 class 关键字的时候，python 在幕后所做的事情，而这些都是通过**元类**来实现的。

**什么是元类？**

元类就是类的类，用来创建类的东西。而 type 就是 python 中用来创建所有类的元类。

python 中所有的东西都是对象，包括整数、字符串、函数以及类。所有的这些都是由一个类创建的，这个类就是元类。

元类的主要目的：为了当创建类的时候能够自动的改变类。

**元类的主要作用？**

- 拦截类的创建
- 修改类
- 返回修改之后的类

### 2.动态语言

动态语言的定义：在运行的过程中可以修改。

因为 python 语言是动态语言，所以可以在软件中实现热更新的效果。

python 动态修改属性和方法的总结如下：

```
给对象添加属性：对象.属性 = 属性
给类添加属性：类.属性 = 属性
给对象添加实例方法：对象.方法 = types.MethodType(方法, 对象)
给类添加静态方法：类.静态方法 = 静态方法
给类添加类方法：类.类方法 = 类方法
```

### 3.生成器

在 python 中，一边循环一边计算的机制，称为**生成器（generator）**

#### 创建生成器的几种方法

方法一：列表生成式修改法

一个普通的列表生成式：

```python
a = [x for x in range(10)]
```

执行 a 的话会直接生成一个 [0,1,2,3,4,5,6,7,8,9] 的列表。但假如想要生成一个无限瀑布流的列表的话，那可能会因为占用过多的内存而导致程序被杀死。

解决办法便是将列表生成式修改为生成器：

```python
a = (x for x in range(10))
```

现在 a 便成为了一个生成器对象，可以通过 next(a) 函数每次获取生成器返回的下一个值。

生成器保存的是算法，每次调用 next(a) ，就会计算出 a 的下一个元素的值，直到计算到最后一个元素，没有更多元素的时候，便会抛出 stopIteration 异常。但这种方式并不推荐使用，而是推荐使用 for 循环，因为生成器也是属于可迭代对象。

方法二：函数法

可以将函数中所要生成元素的位置更改为 **yield 元素**。这样该函数便成为了一个生成器。

因为 yield 是阻断式输出命令，程序通过 next 方法执行的时候便会停止在 yield 这里，也就是说每一次的遍历都是以 yield 作为分界线的。

例如，使用生成器来实现斐波那契数列：

```python
def fib(times):
    n = 0
    a,b = 0,1
    while n<times:
        yield b
        a,b = b,a+b
        n += 1
    return 'done'
```

使用 for 循环便可以将每一次产生的数获取到。

```python
for n in fib(100):
    print(n)
```

但是使用 for 循环调用生成器的时候，会发现拿不到生成器的 return 语句的返回值。如果想要拿到该值的话就得使用 try-except 来捕获 StopIteration 错误：

```python
test = fib(100)
while True:
    try:
        x = next(test)
        print(x)
    except StopIteration as e:
        print(e.value)
        break
```

为了获取生成器 test 的下一个生成的元素，可以通过如下的三种方式：

- next(test)
- test.\_\_next__()
- test.send(None)

**test.\_\_next__()与test.send(None)的区别？**

- test.send(None) 的作用等价于 test.next()，作用都是让生成器执行一次并停留在 yield 处。
- test.send() 的作用是给 yield 传入一个参数变量，该变量可以在在生成器中设置接收并进行相应的操作。
- test.next() 只是执行一次迭代器，并没有传入值。

```py
def test():
    i = 0
    while i < 5:
         temp = yield i
         print(temp)
         i += 1

a = test()
a.send(None)
a.send("python")

# 第一次调用生成器 a 时不能直接调用 a.send("--")，因为这样程序会出错，所以第一次调用 send 必须得传入 None

# 程序输出结果
# 0

# python
# 1
```

#### 生成器总结：

- 生成器是一个能记住上一次返回时在函数体中的位置的函数。
- 生成器不仅记住了函数的数据状态，而且还能记住函数在流控制构造中的位置。

#### 生成器的特点

- 节约内存
- 迭代到下一次的调用时，所使用的参数都是第一次所保留下的，即是说，在整个所有函数调用的参数都是第一次所调用时保留的，而不是新创建的。
- 生成器中可以有多个 yield

#### 多任务-协程

多任务实现可以通过三种不同的方式：

- 进程
- 线程
- 协程

其中协程的速度是最快的，接下来就来介绍一下如何通过协程来实现多任务。

```py
def test1():
    while True:
        print("--1--")
        yield None # 使用生成器阻断执行

def test2():
    while True:
        print("--2--")
        yield None # 使用生成器阻断执行

t1 = test1()
t2 = test2()

while True:
    t1.__next__()
    t2.__next__()
```

通过使用生成器 yield 来达成阻断程序运行的效果，使得程序只有在执行 __next\__() 时才执行一个任务。这样通过三个死循环便达到了多任务的效果。

### 4.迭代器

迭代是访问集合中元素的一种方式。而迭代器是一个可以记住迭代集合时遍历位置的对象。迭代器从第一个元素开始往后迭代，直到最后一个元素为止。迭代器**只能前进，不能后退**。

#### 可迭代对象

可迭代对象（itrable）是指可以直接作用于 for 循环的对象。

可迭代对象包括：

- 集合数据类型
    - list
    - tuple
    - dict
    - set
    - str
- 生成器 
- 带 yield 的生成器对象

#### 判断是否可以迭代

可以使用 isinstance() 来判断一个对象是否是可迭代对象。

首先需要导入指定包：

```py
from collections import Iterable
```

然后进行判断

```py
isinstance(所要判断的对象,Iterable)
```

#### 迭代器

可以被 next() 函数调用并不断返回下一个值的对象被称为迭代器（Iterator）

可以通过 isinstance() 函数来判断一个对象是否是迭代器对象

首先需要导入指定包：

```py
from collections import Iterator
```

然后进行判断

```py
isinstance(所要判断的对象,Iterator)
```

#### iter()函数

可以使用 iter() 函数来将可迭代对象（Iterable）变为迭代器（Iterator）

#### 迭代器总结

- 凡是可作用于 for 循环的对象都是可迭代对象（Iterable）类型
- 凡是可作用于 next() 函数的对象都是迭代器（Iterator）类型
- 集合类型如 list、dict、str等都是 Iterable 但不是 Iterator，也不能通过 iter() 函数获得一个 iterator 对象。
- **生成器一定是迭代器，但迭代器不一定是生成器**。

### 5.闭包

#### 函数引用

```py
def test1():
    print("---1---")

test1()#调用函数
ret = test1#引用函数
ret()#通过引用调用函数
```

在 python 中，所有的“=”都是对象的引用。

#### 什么是闭包

函数内部定义了一个函数，并且该函数用到了外边函数的变量，那么将里面的这个函数称为**闭包**

```py
def test(number):
    # 定义闭包
    def test_in(number_in):
        print(number+number_in)
    # 返回闭包结果
    return test_in

test(100)#只是执行了test()，并没有执行闭包
ret = test(100)#100传递给了number，函数引用
ret(1000)#1000传递给了number_in，并执行闭包
```

#### 闭包应用

需求：使用代码创建一个一元一次方程，要求给出不同的x值，输出对应的y值。方程公式为：

```py
y = ax+b
```

基本实现：

```py
def test1(a,b,x):
    print(a*x+b)

test1(1,1,0)
test1(1,1,1)
```

这种实现方式虽然简单，但调用复杂，需要每次都传入3个参数。

闭包实现：

```py
def test2(a,b):
    def test2_in(x):
        print(a*x+b)
    return test2_in

line = test2(1,1)#传入a，b
line(1)#传入x
line(0)
```

这种实现方式只需要在第一次传入a，b的值，以后只需传入x即可。

```py
# 另一条直线
line1 = test2(5,6)
line1(1)
```

又创建一条直线，并且在执行时，ab的值都会被保存下来。

可以看到，**闭包具有提高代码重用性的作用**

#### 闭包总结

- 闭包优化了变量，原来需要类对象完成的工作，闭包也能完成。
- 由于闭包引用了外部函数的局部变量，则外部函数的局部变量没有及时释放，消耗内存。

### 6.装饰器

**重点内容**

#### 函数引用

```py
def test():
    print("---1---")
def test():
    print("---2---")

test()#执行test函数，打印结果为2
```

首先，第一行定义一个 test 函数时便创建一个对应的函数体，并使用 test 指向该函数体。当再定义 test 时便会再创建一个函数体，再用test指向，因为test已经指向了第一个函数体，此时便会将test重新指向下一个函数体。

#### 装饰器

```py
def f1():
    print("---1---")
def f2():
    print("---2---")
```

此时，需要在 f1(),f2() 函数调用前进行相应的权限判断。最好的解决办法便是**装饰器**

```py
# func是函数引用，必须得有
def w2(func):
    # 定义一个闭包
    def inner():
        print("进行权限判断")
        # 执行函数
        func()
    # 返回闭包
    return inner
```

装饰器其实是建立闭包的基础上的。以上就是定义了一个闭包，不同之处在于该闭包传入了一个函数引用，并在闭包中执行了该函数。

```py
f1 = w2(f1)
# 装饰器添加完成，f1函数执行时就已经有了权限检测。
f1()
```

在经过装饰器之后，f1便已经指向了 w2 中定义的闭包，而闭包中的变量却早已指向 f1，等于先执行了闭包中的内容，再执行了原有 f1 中的代码。这就是**装饰器：在函数执行前后添加相应的代码，而函数的调用并不受影响**

而为了更加方便的使用装饰器，便利用python中独有的**语法糖@**来使用装饰器

```py
@w1
def f1():
    pass
# 等价于
f1 = w1(f1)
```

@w1 等价于 w1(f1)，并将执行完的返回值赋值给 @w1下面的函数f1，即w1的返回值再重新赋值给f1。

装饰器是在函数定义前使用的。

#### 多个装饰器

```py
def decorate1(fn):
    def func():
        print("d1 "+fn()+" d1")
    return func()

def decorate1(fn):
    def func():
        print("d2 "+fn()+" d2")
    return func()

# 以上定义了两个装饰器
# 加载装饰器
@decorate1
@decorate2
def test():
    print("hello world")

# 执行函数
test()

# 执行结果
# d1 d2 hello world d2 d1
```

即有多个装饰器的情况下，先执行第一个装饰器，在执行第二个装饰器，依次类推。但在执行第一个装饰器时，需要传入下一个装饰器的返回值，而第二个装饰器中又需要下一个的返回值。一直调用到原函数，然后在逐层向上返回对应的结果。也就是说装饰器的使用就是一个**栈结构**。

装饰器在调用的时候，先执行装饰器中的内容，即在@装饰器时便执行了装饰器中的内容。而在执行 test() 时才会执行闭包中的内容。因为在装饰器中，闭包相当于一个函数的定义，只是在声明函数之后并return，只有在调用test()时return的闭包才会开始执行。

#### 装饰器传参

一般情况下，需要使用装饰器的函数可以分为无参数函数，固定函数，不定长参数函数。

1. 无参数函数

```py
def func(object):
    def func_in():
        object()
    return func_in()

@func
def test():
    print()

test()
```

无参数函数就直接使用装饰器进行装饰，闭包中不传递参数。

2. 定长参数函数

```py
def func(object):
    def func_in(a, b):
        object(a, b)
    return func_in()

@func
def test1(a, b):
    print()

test1(1,2)
```

装饰器等价于 test1 = func(test1) 即 func_in 所指向的被 test1 指向。即 test1 实际指向了闭包函数，所以闭包定义的于调用 test1 传递参数个数一致。而在闭包中执行的是以前的 test1 也需要传入对应参数。

2. 不定长参数函数

不定长参数使用 \*args、 \*\*kwargs 来作为形参。

```py
def func(object):
    def func_in(*args, **kwargs):
        object(*args, **kwargs)
    return func_in()

@func
def test2(*args, **kwargs):
    print()

test2(1,2,3,4,5,6)
# 不同的函数调用使用的是同一个装饰器
test2(1,2,3)
```

不定长参数使用对应的接收，与定长参数一样，只需在闭包函数和闭包中原函数调用的地方更改即可。

#### 对带有返回值的函数进行装饰

假如一个函数具有返回值，在使用了装饰器之后，需要在调用原函数的闭包中接收该返回值，并在闭包的最后返回。

```py
def func(object):
    def func_in():
        ret = object()
        print()
        return ret
    return func_in

@func
def test3():
    print()
    return "haha"

ret = test3()
print(ret)
```

这样就可以在装饰器中接收原函数的返回值并返回，使得在执行装饰器后的函数时可以正常执行。

注意：不要直接使用 return object()，因为装饰器有时还须在原函数执行完之后在进行某些操作，所以得在闭包完成之后在进行返回。

#### 通用的装饰器

可以在装饰器中进行相关操作，使得其可以在有返回值，无返回值，有传入参数，无传入参数的函数中使用，使其成为一种通用的装饰器。

```py
def func(object):
    # 满足有无参数
    def func_in(*args,**kwargs):
        print()
        ret = object(*args,**kwargs)
        print()
        # 满足有无返回值
        return ret
    return func_in
```

#### 带有参数的装饰器

在装饰器中还可以传入参数，这样便可以根据传入的参数的不同来执行相应的装饰操作。

```py
def func(arg):
    # 一个普通的装饰器
    def func_test(object):
        def func_test_in():
            # 对传入的参数进行相应的判断
            object()
        return func_test_in
    # 返回装饰器
    return func_test
```

一个带参数的装饰器就相当于先执行这个函数，然后返回一个普通的无参数的装饰器，最后用这个无参数的普通装饰器来进行装饰操作。

带参数的装饰器好处在于：**可以在执行装饰时进行一些额外的判断**。

#### 装饰器的功能

- 引入日志
- 函数执行时间统计
- 执行函数前预处理
- 执行函数后清理功能
- 权限校验等场景
- 缓存

**封闭开放**原则：规定已经实现的功能代码不允许被修改，但是可以被扩展。

- 封闭：已实现的代码块
- 开放：对扩展进行开发

#### 类装饰器

类装饰器：一个接口约束，必须接收一个 callable 对象作为参数，然后返回一个 callable 对象（函数或重写了__call__()方法的对象）

```py
class Test(object):
    def __init__(self, func):
        self.__func = func
    # 类装饰器
    def __call__(self):
        # 执行对应功能
        self.__func()

@Test
def test():
    print()

test()
```

## 二、其他知识点

### 1.import导入模块

在导入模块时，寻找模块的路径是有一个先后次序的。具体的路径可以通过 **import sys** 然后查看 sys 下的 path 路径 **sys.path**来查看。其显示的内容是一个列表。从上往下查找列表中的路径。

同时也可也以通过修改该属性对应的值来控制模块的导入路径。

```py
sys.path.append("新路径")
```

这样，导入在其他路径下的模块时便可以使用该方法将路径添加，然后直接 **import 模块名**导入即可。

### 2.重新导入模块

模块被导入后，import module 不能重新导入模块。需要使用：

```py
from imp import *
reload(模块名)
```

这样，模块便能够被重新导入。

注：模块被导入之后所具有的功能便是模块被导入之前的功能，假如在此期间将模块进行更改的话便需要进行**reload**重新加载。

### 3.循环导入

模块 a 中导入了模块 b，而模块 b 中又导入了模块 a，这种情况被称为**循环导入**。应该尽量避免这种情况的发生，这样会导致出现导入错误。

但万一有这种需求的话，一个可行的解决办法便是通过主模块来调用其他子模块。

### 4.==、is

**重点内容**

==：用来判断值是否相等（值比较）
is：用来比较两个引用是否指向了同一个对象（引用比较）

注意：python中的数字不同

### 5.拷贝

对对象进行拷贝可以分为两种拷贝方式：深拷贝和浅拷贝

- 浅拷贝：用于对一个对象的顶层拷贝（只拷贝引用，并没有拷贝内容）

```py
a = [1,2,3]
b = a
```

- 深拷贝：用于对一个对象的深层拷贝（拷贝了内容）

```py
import copy
c = copy.deepcopy(a)
```

其中，值得注意的是：通过 deepcopy 方式拷贝的对象其内部也会嵌套进行深层拷贝。

copy 模块下有 copy 和 deepcopy两个方法，两者的区别：

```py
import copy

a = [1,2,3]
b = [4,5]
c = [a,b]

d = copy.copy(c)
e = copy.deepcopy(c)

a.append(6)

print(d)
print(e)
```

copy只能深层拷贝第一层的内容，而嵌套拷贝则是使用浅层拷贝；deepcopy 不仅第一层是使用深层拷贝，其余各层也是嵌套进行深层拷贝。

```py
a = [1,2,3]
b = [4,5,6]
c = (a,b)

d = copy.copy(c)

id(c)
id(e)

# 结果c和e的地址相同，因为元祖是不可变类型
```

copy.copy 函数能够自动判断所拷贝的内容是可变类型还是不可变类型，可变类型则执行首层深拷贝，其余执行浅拷贝，但若是不可变类型则直接执行浅拷贝。

python中的可变类型：

- 列表
- 字典
- 集合

python中的不可变类型：

- 元祖
- 数字
- 字符串

### 6.进制、位运算

计算机中有的进制包括：二进制、八进制、十进制、十六进制

数字在计算机中是使用**二进制补码**的形式来保存的，因此数字之间的运算需要按照补码进行运算。

计算机中数字的表示一般有三种情况：原码、反码、补码

- 正数：原码=反码=补码
- 负数：
    - 反码=符号位不变，其他位取反
    - 补码=反码+1

```
二进制表示+1和-1（原码表示）
+1:0000 0000 0000 0001
-1:1000 0000 0000 0001
符号位1表示负号、0表示正号

补码表示：
+1:0000 0000 0000 0001
-1:1111 1111 1111 1111

一个字节八位，两个字节表示一位数字
```

原码、反码、补码存在的意义在于：**数字计算可以取得正确的结果**

如何从补码转回原码呢？

原码 = 补码的符号位不变，数据位取反，然后尾+1

可以把减法用加法来计算，这样就只需要设计**加法计算器**了。

进制之间的相互转换：

各种进制之间是通过十进制来进行中转，就可以转为各种进制，因为十进制是我们人类正常使用的数字表示方法。

以十进制 18为例，展示其余进制表示18的方式：

- 二进制：0b10010
- 八进制：O022
- 十进制：18
- 十六进制：0x12
```
二进制 -》 十进制：int("ob10010",2)
十进制 -》 二进制：bin(18)

八进制 -》 十进制：int("O022",8)
十进制 -》 八进制：oct(18)

十六进制 -》 十进制：int("ox12",16)
十进制 -》 十六进制：hex(18)
```

在计算机中快速地进行乘除法是通过**位运算**来实现的。具体来说：把一个数的各位整体向左移动一位，就变为了原来的**2倍**；同理，整体向右移动一位，则**除以2**。

编程中常见的位运算操作符：

- &：按位与
- ｜：按位或
- ^：按位异或（相同为0，不同为1）
- ～：按位取反
- <<：按位左移
- \>>：按位右移

位运算的优点：

- 直接操作二进制
- 省内存
- 效率高

**计算机底层执行数值计算时都是通过位移和加法进行的**

### 7.私有化

这里针对变量的定义来规定一些变量定义的方式：

- **：公有变量。
- _**：单前置下划线，私有化属性或方法，from somemodule import * 禁止导入，类对象和子类可以访问。
- __**：双前置下划线。避免与子类中的属性命名冲突，无法在外部直接访问（名字重整，所以访问不到）。
- __**\_\_：双前后下划线，用户名字空间的魔法对象或属性。
- **_：单后置下划线，用于避免与python关键字冲突。

在 python 中，_ 下划线就相当于其他语言中的 private。

通过 name mangling （名字重整，目的是为了防止子类重写基类的方法或者属性，如：_class__object 可以将变量名进行修改，就可以访问私有属性了） 机制来访问私有变量。

但使用 import 模块是可以使用 _x，__xx的变量，因为这种情况是将整个模块拿来使用。

### 8.属性property

在类中定义的私有属性可以通过设置 setter 和 getter 方法来对其进行操作。

通过使用 property 可以将类中的私有变量利用其 getter 和 setter 方法来将其作为公有变量进行访问。

```py
class Money(object):
    def __init__(self):
        self._money = 0

    def getMoney(self):
        return self._money

    def setMoney(self, value):
        if isinstance(value, int):
            self._money = value
        else:
            print("error:不是整数数字")

    money = property(getMoney, setMoney)

m = Money
# 调用set方法赋值
m.money = 200
# 调用get方法，打印200
print(m.money)
```

注意，property 方法中一定得先写 getter 方法在写setter 方法。property 的作用相当于把方法进行了封装，开发者在对属性设置数据时更方便。（property中只写方法名，不用写小括号，并不是调用方法）

上述使用 property 的方式比较麻烦，可以使用我们之前介绍过的**装饰器**来完成 setter 和 getter 的操作。

```py
class Money(object):
    def __init__(self):
        self._money = 0
    
    # money 为属性名
    @property
    def money(self):
        return self._money
    
    # 两个方法名字相同
    @money.setter
    def money(self, value):
        if isinstance(value, int):
            self._money = value
        else:
            print("error:不是整数数字")
```

通过装饰器将 setter 和 getter 配置之后，再调用属性时便会直接进行调用。

### 8.作用域

**命名空间**：一个变量起作用的范围。

由于变量作用域的不同，所以变量又分为**全局变量（globals）**和**局部变量（locals）**。

- globals()：用来保存全局变量，使用字典类型保存。
- locals()：用来保存一个方法内部的局部变量，使用字典类型保存。

而 python 在程序中查找一个对象的顺序是按照 **LEGB**原则的。

```
locals -> enclosing function -> globals -> builtins
```

- locals：当前所在的命名空间，函数的参数也属于命名空间内的变量。
- enclosing：外部嵌套函数的命名空间（闭包中常见）

```py
def function():
    a =10
    def functions():
        # a为外部嵌套函数的命名空间
        print(a)
    return functions
```
- globals：全局变量，函数定义所在模块的命名空间。
- builtins：内建模块的命名空间，python在启动的时候会自动为我们载入很多的函数、类。如 dict、list、type、print，这些都属于 __bulitin\_\_ 模块中。

### 9.垃圾回收

#### 小整数对象池

python 为了优化速度，使用了**小整数对象池（类似于Java的缓存池，[-128,127])**，避免为整数频繁申请和销毁内存空间。

python对小整数的定义是 [-5,257)，这些整数对象是提前建立好的，不会被垃圾回收。在一个 python 程序中，所有位于这个范围内的整数使用的都是同一个对象。

同理，单个字母也是一样的，a～z单个字母也是同一个对象。

#### 大整数对象池

对于每一个大整数来说，均会创建一个新对象。

#### intern 机制

```py
a = 'abcde'
b = 'abcde'
# b引用了a所引用的对象
```

字符串为不可变类型，且 python 中也有一个**intern机制**，只占用一个 ‘abcde’ 所占的内存空间。

也就是说：

- 小整数 [-5,257) 共用对象，常驻内存。
- 单个字符共用对象，常驻内存。
- 单个单词（字符串），不可修改，默认开启 intern 机制，共用对象，引用记述为0，则销毁。
- 字符串**含有空格**，不可修改，没有开启 intern 机制，不共用对象，引用计数为0，则销毁。
- 大整数不共用内存，引用计数为0，则销毁。
- 数值类型和字符串类型在 python 中都是不可变对象，这意味着无法修改这个对象的值，每次对变量的修改，实际上是创建一个新的对象。

#### Garbage Collection（GC垃圾回收）

python 使用**引用计数机制为主，标记-清除和分代收集两种机制为辅**的内存回收策略。

python 中每一个东西都是对象，核心就是一个结构体：**pyobject**

```c++
typedef struct_object{
    // 引用计数
    int ob_refcnt;
    struct_typeobjrct *ob_type;
} pyobject
```

引用计数机制的优点：

- 简单。
- 实时性：一旦没有引用内存就直接释放了。实时性还带来一个好处：处理回收内存的时间分到到了平时。

引用计数机制的缺点：

- 维护引用计数消耗资源。
- 循环引用导致无法销毁对象，因此引入了其他垃圾回收机制。

GC系统所承担的工作：

- 为新生成的对象分配内存。
- 识别垃圾对象。
- 从垃圾对象中回收内存。

如果将应用程序比做人的身体，代码、算法比做人的大脑，而GC则是人的心脏。

python 在执行时是使用**可用列表**来创建对象，这些用来回收特定 list 对象，而创建剩余的对象则直接向 OS 请求内存，并使用变量指向。

**引用计数**：每当对象的引用数减为0，python立即将其释放，把内存还给操作系统。

但 python 中还存在着一种数据结构为循环数据结构，如循环链表。两个节点都设置为 None，但之间还存在着相互引用关系。

python 的引用计数算法不能够处理相互指向自己的对象。因此 python 便引用**Generational GC**算法。

具体来说，python 使用**零化（Generation Zero）链表**来持续追踪活跃的对象。随后，python 会循环遍历零代链表上的每个对象，检查链表中每个相互引用的对象，根据规则减掉其引用计数。

```
generation zero
                   |
<——> 1 <——> 1 <——> 3 <——> 1 <——> 2 <——>
                   |             |
```

通过识别内部引用，python 能减少许多零代链表对象的引用计数。

```
                   |
<——> 0 <——> 0 <——> 2 <——> 0 <——> 1 <——>
                   |             |
```

收集器将零代链表中引用计数为0的对象回收，剩下的活跃的对象则被移动到一个新的链表：**一代链表**。

```
generation one
     |
<——> 2 <——> 1 <——>
     |      |
```

随着程序运行，python解释器保持对新对象的创建以及因为引用计数为零而被释放掉的对象的追踪。这两个计数值之间由于循环引用的原因而导致存在差异，一旦这个差异累计超过了某个阀值，则 python 的收集机制就启动了。

随着时间的推移，程序所使用的对象逐渐从零代链表移动到了一代链表。而 python 继续通过此方法将剩下的活跃对象移动到二代链表。

python 处理零代链表最为频繁，其次才是一代然后才是二代。

几代链表之间的转移是通过创建新对象和回收对象之间的差异达到**阀值**时才执行转移的。

**弱代假设**：年前的通常死的比较快，年老的活得久。因此便采用上面的机制。

在 python 中导致引用计数+1的情况有：

- 对象被创建，a=1
- 对象被引用，b=a
- 对象被作为参数传入到一个函数中，func(a)
- 对象作为一个元素，存储在容器中，list1=[a,a]

在 python 中导致引用计数-1的情况有：

- 对象的别名被显式销毁，del(a)
- 对象的别名被赋予新的对象，a=124
- 一个对象离开他的作用域，例如函数执行完毕时，func函数中的局部变量（全局变量不会）
- 对象所在的容器被销毁，或从容器中删除

在 python 中可以通过 getrefcount() 方法来查看一个对象的引用次数

```py
import sys

a = 'hello world'
# 调用 getrefcount 函数传入 a，导致引用次数+1
sys.getrefcount(a)

# 结果为2
```

垃圾回收中引用计数机制的缺点是由于**循环引用**的问题，通过下面的方式可以关闭 python 中的 GC：

```py
import gc
# 关闭python中的gc
gc.disable()
```

由于循环引用，导致垃圾回收器无法进行回收，这样就会导致内存泄露。

```py
# 导入gc模块
import gc

# 列表对象，存储垃圾回收之后的对象
gc.garbage
# 显式进行垃圾回收操作，会返回不可达到的对象数目，返回对象以及对应的dict
gc.collect()
```

有三种情况会触发 python 的垃圾回收：

- 调用 gc.collect()
- 当 gc 模块的计数器达到阀值时
- 程序退出的时候

gc模块会提供一个接口给开发者设置垃圾回收的选项。其主要的一个功能便是解决循环引用的问题。

- gc.set_debug(flags)：设置 gc 的 debug 日志，一般设置为 gc.DEBUG_LEAK
- gc.collect([generation])：显式进行垃圾回收，可以输入参数，0代表只检查第一代的对象，1代表检查第一、二代的对象，2代表检查一、二、三代对象。默认为2.
- gc.get_threshold()：获取 gc 模块中自动执行垃圾回收的频率。
- gc.set_threshold()：设置自动执行垃圾回收的频率。
- gc.get_count()：获取当前自动执行垃圾回收的计数器。

注意，必须要 import gc 模块，并且设置 is_enable() = True 才会启动自动垃圾回收。这个机制的主要作用是**发现并处理不可达的垃圾对象**。

**垃圾回收 = 垃圾检查+垃圾回收**

python 采用**分代收集**的方法，把对象分为**三代**，一开始，对象在创建的时候放在第一代，如果在一次一代的垃圾检查中，该对象存活下来就会被放在第二代中，同理在一次二代的检查中，该对象存活下来就会被放在第三代中。

gc 模块唯一处理不了的是循环引用的类都有 __del\_\_ 方法，所以项目中要避免定义 __del\_\_ 方法。

### 10.内建属性

可以通过 **dir(类名)** 来查看类中的内建属性和方法。

| 常用专有属性 | 说明 | 触发方式 |
| ----  | ----  | ---- |
| __init\_\_  | 构造初始化函数 | 创建实例之后，赋值时使用，在\_\_new\_\_之后 |
| __new\_\_ | 生成实例所需属性 | 创建实例时 |
| __class\_\_ | 实例所在的类 | 实例.\_\_class\_\_ |
| __str\_\_ | 实例字符串表示，增强实例的可读性 | print(类实例) |
| __repr\_\_ | 实例字符串表示，增强实例的准确性 | 类实例回车或者print(repr(类实例)) |
| __del\_\_ | 析构 | del(类实例)，删除类实例|
| __dict\_\_ | 实例自定义属性 | vars(类实例.\_\_dict\_\_) |
| __doc\_\_ | 类文档，子类不继承 | help(类或实例) |
| __getattribute\_\_ | 属性访问拦截器 | 访问实例属性时 |
| __bases\_\_ | 类的所有父类构成元素 | 类名.\_\_bases\_\_ |

以上的专有属性需要自定义时得在类中重写对应的方法。

### 11.内建函数

使用 dir(builtins\_\_)函数可以看到许多 python 解释器启动之后默认加载的属性和函数，这些函数称为 **内建函数**。这些内建函数会在启动 python 解释器时默认加载。

- range函数：生成一个指定开始结束步长的迭代器。

```py
range([start,] stop[, step])
# start：开始处，默认为0；stop可省略；step为步长
```

在 python2 中 range 函数返回列表，python3 中 range 函数返回一个迭代器。

- map函数：根据提供的函数对指定序列做映射。

```py
map(function, sequence[, sequence,...])
# function为函数；sequence为序列，可以有一个或多个，这取决于 function 需要几个参数；返回为 list
```

map函数返回值为 list。参数序列中的每一个元素分别调用 function 函数，返回包含每次 function 函数返回值的 list。

```py
map(lambda x:x*x, [1,2,3])
# 结果为[1, 4, 9]
map(lambda x,y:x+y, [1,2,3], [4,5,6])
# 结果为[5, 7, 9]
```

- filter函数：对指定序列执行过滤操作

```py
filter(function or None, sequence)
# function为一个函数，返回值为 True或者False；sequence可以为str、tuple、list；返回为str、tuple、list
```

filter 函数会对序列参数 sequence 中的每个元素调用 function函数，最后返回的结果包含调用结果为 True 的元素。返回值的类型和参数 sequence 的类型相同。

```py
filter(lambda x:x%2, [1,2,3,4])
# 返回结果为 [1,3]
```

- reduce函数：reduce函数会对参数序列中的元素进行累积。

```py
reduce(function, sequence[, initial])
#function函数有两个参数；sequence序列可以是str、tuple、list；initial为固定初始值；返回值为数值
```

reduce 依次从 sequence 中取一个元素，和上一次调用 function 的结果作为参数再次调用 function。第一次调用 function时，如果提供 initial 参数，会以 sequence 的第一个元素和 initial 作为参数调用 function，否则会以序列 sequence 中的前两个元素做参数调用 function。注意，function函数不能为 None。

```py
reduce(lambda x,y:x+y, [1,2,3,4])
# 返回10
reduce(lambda x,y:x+y, [1,2,3,4], 5)
# 返回15
```

- sorted函数：对对象进行排序操作。

```py
sorted(iterable, cmp=None, key=None, reverse=False)
```

sorted函数对可迭代对象进行排序操作，并可指向对应参数。返回类型与原可迭代对象相同。

```py
sorted([1,4,2,6,3,5])
# 返回[1,2,3,4,5,6]
sorted([1,4,2,6,3,5], reverse=1)
# 返回[6,5,4,3,2,1]
```

### 12.集合set

集合中存储的数据的最大特点是：**数据不存在重复**

集合对象支持如下的数学运算：

- union（联合）
- intersection（交）
- difference（差）
- sysmmetric_difference（对称差集）

集合对象的格式为：{元素, ...}

集合对象支持的操作有：

- 交集：&，a&b（a中有的b中也有的）
- 并集：|，a|b（a中和b中都有的）
- 差集：-，a-b（a中除b之外的）
- 对称差集：^，a^b（在a中或b中，但不会同时出现在二者中，也就是ab的并集减去ab的交集）

### 13.functools

functools 是从 python2.5中引入的，存放一些工具函数。

```py
# 导入包
import functools

# 查看包中的内置属性
dir(functools)
```

functools 中使用频率较高的2个函数：

- partial函数（偏函数）

把一个函数的某些参数设置指定的默认值，返回一个新的函数，调用这个新函数会更简单。

```py
import functools

def test1(*args, **kwargs):
    print(*args)
    print(**kwargs)

t1 = functools.partial(test1, 1, 2, 3)
t1()
# 返回 (1,2,3) {}
t1(4,5,6)
# 返回 (1,2,3,4,5,6) {}
t1(a="python", b="test")
# 返回 (1,2,3) {a:"python", b:"test"}
```

- wraps函数

使用装饰器时，由于被装饰后的函数已经变为了另一个函数。此时，在执行原函数的 __doc\_\_ 时会显示闭包中的文档信息，而非原函数中的。因此，python 中的 functools 包中提供了一个叫 **wraps** 的**装饰器**来消除这样的副作用。

```py
import functools

def note(func):
    "note function"
    @functools.warps(func)
    def wrapper():
        "wrapper function"
        print("someting")
        return func()
    return wrapper

@note
def test():
    "test function"
    print("test")

print(test.__doc__)
# 打印test function
```

### 14.模块进阶

python有一套很有用的**标准库（standard library）。标准库会随着 python 解释器一起安装到电脑中。

常用的标准库有：

| 标准库 | 说明 |
| --- | --- |
| builtins | 内建函数，默认加载 |
| os | 操作系统接口 |
| sys | python自身的运行环境 |
| functools | 常用的工具 |
| json | 编码和解码json对象 |
| logging | 记录日志，调试 |
| multiprocessing | 多进程 |
| threading | 多线程 |
| copy | 拷贝 |
| time | 时间 |
| datetime | 日期和时间 |
| calendar | 日历 |
| hashlib | 加密算法 |
| random | 生成随机数 |
| re | 字符串正则表达式 |
| socket | 标准的 BSD Sockets API |
| shutil | 文件和目录管理 |
| glob | 基于文件通配符搜索 |

### 15.调试

**pdb**是 python 中基于命令行的调试工具。

启动方式：

```py
python3 -m pdb example.py
```

启动之后可以输入如下的命令以及简写来执行文件的调试工作。

| 调试命令（简写） | 命令 | 作用 |
| --- | --- | --- |
| b | break | 显示所有断点 |
| c | continue | 继续执行函数 |
| l | list | 查看当前行的代码段 |
| s | step | 进入函数 |
| r | return | 执行代码直到从当前函数返回 |
| q | quit | 中止并退出 |
| n | next | 执行下一行 |
| p | print | 打印变量的值 |
| h | help | 帮助 |
| a | args | 查看传入参数 |
| enter（回车） | | 重复上一条命令 |
| b lineno | break lineno | 在执行行设置断点 |
| b file:lineno | break file:lineno | 在指定文件的行设置断点 |
| | clear num | 删除指定断点 |
| | bt | 查看函数调用堆栈 |


python 支持多种调试方式：

1. 执行时调试（推荐使用）

```py
python3 -m pdb example.py
```

程序启动，停止在第一行等待单步调试

2. 交互调试

进入 python 或者 ipython 解释器：

```py
import pdb

# 调试testfun函数，包含参数
pdb.run('testfun(args)')
```

3. 程序里埋点

当程序执行到 pdb.set_trace() 位置时停下来调试

```py
import pdb

pdb.set_trace()
```

在执行时调试修改变量的方式：

```py
!arg = new_value
```

4. 日志调试

通过查看系统产生的日志来进行调试。

