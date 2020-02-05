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