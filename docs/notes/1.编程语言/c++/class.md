类的基本思想是数据抽象（data abstraction）和封装（encapsulation）。数据抽象是一种依赖于接口（interface）和实现（implementation）分离的编程（以及设计）技术。类的接口包括用户所能执行的操作；类的实现则包括类的数据成员、负责接口实现的函数体以及定义类所需的各种私有函数。

封装实现了类的接口和实现的分离。封装后的类隐藏了它的实现细节，也就是说，类的用户只能使用接口而无法访问实现部分。

类想要实现数据抽象和封装，需要首先定义一个**抽象数据类型（abstract data type）**，在抽象数据类型中，由类的设计者负责考虑类的实现过程；使用该类的程序员只需要抽象地思考类型做了什么，而无需了解类型的工作细节。

### 1.1 定义抽象数据类型

#### 1.1.1 设计Sales_data类

书店程序

Sales_data类

**数据成员**：

bookNo：string类型，表示ISBN编号

units_sold：unsigned类型，表示某本书的销量

revenue：double类型，表示这本书的总销售输入

**成员函数**

combine 将一个Sales_data对象加到另一个对象上

isbn 返回对象的ISBN编号

avg_price 返回售出书籍的平均价格

**非成员接口函数**

add 执行两个Sales_data对象的加法

read 将数据从istream读入到Sales_data对象中

print 将Sales_data对象的值输入到ostream

#### 1.1.2 定义Sales_data类

```
struct Sales_data {
    // 成员函数
    std::string isbn() const { return bookNo; }  // 定义在内的内部
    Sales_data& combine(const Sales_data&);
    double avg_price() const;

    // 成员变量
    std::string bookNo;
    unsigned units_sold = 0;
    double revenue = 0.0;
}

// 非成员接口函数
Sales_data add(const Sales_data&, const Sales_data&);
std::ostream &print(std::ostream&, const Sales_data&);
std::istream &read(std::istream&, Sales_data& );
```

成员函数必须在类的内部声明，它的定义既可以在类的内部也可以在类的外部。

作为接口组成部分的非成员函数，它们的定义和声明都在类的外部。

**this**

```
Sales_data total;
total.isbn();
```
当我们调用成员函数时，实际上是替某个对象在调用它。如果isbn指向Sales_data的成员（例如bookNo），则它隐式地指向调用该函数的对象的成员。在上面所示的调用中，当isbn返回bookNo时，实际上它隐式地返回total.bookNo。

成员函数通过一个名为**this**的额外的隐式参数来访问调用它的那个对象。当我们调用一个成员函数时，用请求该函数的对象地址初始化this，例如，如果调用
```
total.isbn();
```
则编译器负责把total的地址传递给isbn的隐式形参this，可以等价地认为编译器将该调用重写成了如下的形式：
```
// 伪代码，用于说明调用成员函数的实际执行过程
Sales_data::isbn(&total)
```
其中，调用Sales_data的isbn成员时传入了total的地址。

在成员函数内部，我们可以直接使用调用该函数的对象的成员，而无需通过成员访问运算符来做到这一点，因为this所指的正是这个对象。任何对类成员的直接访问都被看作this的隐式引用，也就是说，当isbn使用bookNo时，它隐式得使用this指向的成员，就像我们书写了this->bookNo一样。

*this指针是对象在类成员函数中的引用，它隐式地指向调用该函数的对象的地址，当我们通过某个对象调用类的成员函数时，则this被请求该函数的对象地址所初始化。*

**const成员函数**

isbn函数的另一个关键之处是紧随参数列表后的const关键字，这里const的作用是修改隐式this指针的类型。

默认情况下，this的类型是指向类类型的非常量版本的常量指针。例如在Sales_data成员函数中，this的类型是Sales_data *const。尽管this是隐式的，但它仍然需要遵循初始化规则，意味着（默认情况下）我们不能把this绑定到一个常量对象上。这一情况也就使得我们不能在一个常量对象上调用普通的成员函数。

如果isbn是一个普通函数而且this是一个普通的指针参数，则我们就应该把this声明为const Sales_data *const。毕竟，在isbn的函数体内不会改变this所指向的对象，所以把this设置为指向常量的指针有助于提高函数的灵活性。

然而，this是隐式的并且不会出现在参数列表中，所以在哪将this声明成指向常量的指针就成为我们必须面对的问题。c++语言的做法是允许把const关键字放在成员函数的参数列表后，此时，**紧跟在参数列表后面的const表示this是一个指向常量的指针**。像这样使用const成员的函数被称为**常量成员函数**

因为this是指向常量的指针所以常量成员函数不能改变调用它的对象的内容。因此const对函数的约束，其实是对调用该函数的对象的约束，即不能在函数中无法改变该对象的任何成员。在上例中，isbn可以读取调用它的对象的数据成员，但是不能写入新值。

*注意：常量对象，以及常量对象的引用或指针都只能调用常量成员函数。*

**结论：**

1、若某个类成员函数中不会修改到类的任何值，那么一般都要将其设置为const函数。

2、常量成员函数能被普通对象和常量对象调用。

3、若定义某个类的常量对象，那么该对象只能调用该类中的常量成员函数。

**类作用域和成员函数**

类本身就是一个作用域，类的成员函数的定义嵌套在类的作用域之内。因此isbn中用到的名字bookNo其实就是定义在Sales_data内的数据成员。

值得注意的是，即使bookNo定义在isbn之后，isbn也能使用bookNo。因为编译分两部分：首先编译成员的声明，然后才轮到成员函数体（如果有的话）。因此成员函数可以随意使用类中的其他成员而无须在意这些成员出现的次序。

**在类的外部定义成员函数**

定义在外部的成员函数必须和它的声明匹配，类外部定义的成员的名字必须包含它所属的类名：
```
double Sales_data::avg_price() const {
    if (units_sold)
        return revenue/units_sold;
    else
        return 0;
}
```

**定义一个返回this对象的函数**

```
Sales_data& Sales_data::combine(const Sales_data &rhs) {  // 把rhs的成员加到this对象的成员上
    units_sold += rhs.units_sold;
    revenue += rhs.revenue;
    return *this;  // 返回调用该函数的对象
}

// 当我们调用时
total.combine(trans);  // 更新变量total当前的值
```
上面这个调用返回total的引用。

#### 1.1.3 定义类相关的非成员函数

**定义read和print函数**

这些函数从操作概念上来说属于类的接口的组成部分，但它们实际上并不属于类本身。定义非成员函数的方式与定义其他函数一样，通常把函数的声明与定义分开来。一般来说，如果非成员函数是类接口的组成部分，则这些函数的声明应该与类在同一个头文件内。

```
istream &read(istream &is, Sales_data &item) 
{
    double price = 0;
    is >> item.bookNo >> item.units_sold >> price;
    item.revenue = price * item.units_sold;
    return is;
}

ostream &print(ostream &os, const Sales_data &item)
{
    os << item.isbn() << " " << item.units_sold << " " 
       << item.revenue << " " <<item.avg_price();
    return os;
}
```
read函数从给定流中将数据读取到给定的对象里，print函数则负责将给定对象的内容打印到给定的流中。

read和print分别接受一个各自IO类型的引用作为其参数，这是因为IO类属于不能被拷贝的类型，因此我们只能通过引用来传递它们。而且，因为读取和写入操作都会改变流的内容，所以两个函数接收的都是普通引用，而非对常量的引用。

**定义add函数**

add函数接收两个Sales_data对象作为其参数，返回值是一个新的Sales_data，用于表示前两个对像的和。

```
Sales_data add(const Sales_data &lhs, const Sales_data &rhs)
{
    Sales_data sum = lhs;  // 把lhs的数据成员拷贝给sum
    sum.combine(rhs);      // 把rhs的数据成员加到sum中
    return sum;
}
```

默认情况下。拷贝类的对象其实拷贝的是对象的数据成员。

#### 1.1.4 构造函数

程序只能通过成员函数来访问数据成员，因此需要设计合适的成员函数，才能成功的接将对象初始化。每个类都分别定义了它的对象被初始化的方式，类通过一个或几个特殊的*成员函数*来控制其对象的初始化过程，这些函数交做**构造函数**。构造函数的任务是初始化类对象的数据成员，无论何时只要类的对象被创建，就会执行构造函数。

构造函数：1、和类名相同 2、没有返回值，也不用被声明为void类型 3、原型位于类声明的公有部分 4、程序声明对象时，自动调用构造函数 5、构造函数的参数名不能和类成员相同，一种常见的做法是，在数据成员名中使用m_前缀（或者在成员名中使用_后缀）

默认构造函数：仅当没有定义任何构造函数的时候，编译器才会提供默认构造函数，为类定义了构造函数后程序员就必须为它提供默认的构造函数。如果提供了非默认构造函数，但没有提供默认构造函数，使用下面的声明就会出错。
```
Sales_data item;  // not possible with current constructor
```

这样做的原因是可能想禁止创建未初始化的对象，然而要创建对象，而不显示的初始化，则必须定义一个不接受任何参数的构造函数。定义默认的构造函数有两种：1、给已有构造函数的所有参数提供默认值； 2、 通过函数重载来定义另一个构造函数————一个没有参数的构造函数：
```
// 第一种方法
Stock(const string & co = "Error", int n = 0; double pr = 0.0);
// 第二种方法
Sales_data ();
// 或者
Sales_data () = default;
```

**构造函数初始值列表**

负责为新创建的对象的一个或几个数据成员赋值。构造函数的初始值是成员名字的一个列表，每个名字后面紧跟括号括起来的（或者在括号内的）成员初始值。

```
Sales_data
```
### 1.2 访问控制与封装

### 1.3 类的其他特性

### 1.4 类的作用域

### 1.5 构造函数再探

### 1.6 类的静态成员