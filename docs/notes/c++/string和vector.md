## *字符串、向量和数组
c++语言定义了一个内容丰富的抽象数据类型库。string和vector是两种最重要的标准库类型。string支持可变长字符串，vector表示可变长的集合。还有一种标准库类型的迭代器，它是string和vector的配套类型，常被用于访问string中的字符或是vector中的元素。
### 1.1 命名空间的using声明
作用域操作符（::），编译器从操作符左侧名字所示的作用域中寻找右侧的名字。
using声明的格式如下：
using *namespace::name;*
一旦声明了上述语句，就可以直接访问命名空间中的名字，不用再添加std::形式的前缀使用它们。每个名字都需要独立的using声明。
头文件不应包含using声明，因为头文件的内容会拷贝到所有引用它的文件里面去，如果头文件里有个using声明，那么每个使用了该头文件的文件都会有这个声明，对于某些程序来说，由于不经意间包含了一些名字，会产生始料未及的名字冲突。

### 1.2 标准库类型string
string表示可变长的字符序列，使用string类型必须首先包含string头文件。
#### 1.2.1 定义和初始化string对象
```
string s1; //默认初始化，s1是一个空串
string s2(s1); //s2是s1的副本  （直接初始化）
string s2 = s1; //等价于s2(s1)，s2是s1的副本 （拷贝初始化）
string s3("value"); //s3是字面值"value"的副本，除了字面值最后的那个空字符外
string s3 = "value"; //等价于s3("value")，s3是字面值"value"的副本
string s4(n, 'c'); 把s4初始化为有连续n个字符c组成的串
```
**直接初始化和拷贝初始化**
使用等号（=）初始化一个变量，实际上执行的是拷贝初始化，编译器把等号右侧的初始值拷贝到新创建的对象中去。与之相反，如果不使用等号，则执行的是直接初始化。

#### 1.2.2 string对象上的操作
```
os << s   将s写到输出流os当中，返回os
is >> s   从is中读取字符串赋给s，字符串以空白符分隔，返回is
getline(is, s)    从is中读取一行赋给s，返回is
s.empty()    s为空返回true，否则返回false
s.size()    返回s中字符的个数
s[n]    返回s中第n个字符的引用，位置n重0记起
s1+s2    返回s1和s2连接后的结果
s1=s2    用s2的副本代替s1中原来的字符
s1==s2    如果s1和s2中所含的字符完全一样，则它们相等；string对象的相等性判断对字母的大小写敏感
s1!=s2    
<, <=, >, >=    利用字符在字典中的顺序进行比较，且对字母的大小写敏感
```

**读写string对象**
使用标准库中的iostream来读写string对象，string对象会自动忽略开头的空白（即空格、换行符、制表符等）并从第一个真正的字符开始读起，直到遇见下一处空白为止。
```
string s1, s2;
cin >> s1 >> s2; //把第一个输入读到s1中，把第二个输入读到s2中
```
**读取未知数量的string对象**
```
int main()
{
    string word;
    while (cin >> word) //反复读取，直到到达文件尾
        cout << word << endl; //逐个输出单词，每个单词后紧跟一个换行
    return 0;
}
```

**使用getline读取一整行**
getline函数的参数是一个输入流和一个string对象，函数从所给定的输入流中读取内容，直到遇到换行符为止（换行符也被读进来了），然后把所读的内容存入到string对象里去（不存换行符）。getline只要一遇到换行符就结束读取操作并返回结果，如果输入一开始就是换行符，那么所得的结果是个空string。

**string的empty和size操作**
empty函数根据string对象是否为空返回一个对应的布尔值，size函数返回string对象的长度（即string对象中字符的个数）

**string::size_type类型**

string类及其他大多数标准库类型都定义了几种配套的类型。这些配套类型体现了标准库类型与机器无关的特性，类型size_type即是其中的一种。

string::size_type是一个无符号类型的值，并且能足够存放下任何string对象的大小。所有用于存放string类的size函数返回值的变量，都应该是string::size_type类型的。c++11新标准中，允许编译器通过auto或decltype来推断变量的类型：
```
auto len = line.size(); //len的类型是string::size_type
```
如果一条表达式里已经有了size()函数就不要再使用int了，这样可避免混用int和unsigned可能带来的问题。

**为string对象赋值**
对于string对象而言，允许把一个对象的值赋值给另一个对象。
```
string st1(10, 'c'), st2; //st1的内容是cccccccccc；st2是一个空字符串
st1 = st2; //此时st1和st2都是空字符串
```

**两个string对象相加**
相加是把左侧的运算对象与其右侧的运算对象串接而成。结果是一个新的string对象，它包含的字符由两部分组成，前半部分是加号左侧string对象所含的字符、后半部分是加号右侧string对象所含的字符。
```
string s1 = "hello, ", s2 = "world\n";
string s3 = s1 + s2; //s3的内容是hello, world\n
s1 += s2; //等价于s1 = s1 + s2
```

**字面值和string对象相加**
字符串字面值与string是不同的类型，标准库允许把字符字面值和字符串字面值转换成string对象，所以在需要string对象的地方就可以使用这两种字面值来替代。当把string对象和字符串字面值及字符字面值混在一条语句中使用时，必须确保每个加法运算符（+）的两侧的运算对象至少有一个是string。
```
string s1 = "hello", s2 = "world";
string s3 = s1 + ", " + s2 + '\n'; //正确，每个加法运算符都有一个运算对象是string
string s4 = "hello" + "," ; //错误，两个运算对象都不是string
string s5 = "hello" + ", " + s2; //错误，不能将字面值直接相加
```
### 1.2.3 处理string对象中的字符
为了处理string对象中的字符，比如检查一个string对象是否包含空白，或者把string对象中的字母改成小写，再或者检查某个特定的字符是否出现等。cctype头文件中定义了一组处理标准库函数处理这些工作。
```
//cctype头文件中的函数
isalnum(c)  当c是字母或数字时为真
isalpha(c)  当c是字母时为真
iscntrl(c)  当c是控制字符时为真
isdigit(c)  当c是数字时为真
isgraph(c)  当c不是空格但可打印时为真
islower(c)  当c是小写字母时为真
isprint(c)  当c是可打印字符时为真（即c是空格或c具有可视形式）
ispunct(c)  当c是标点符号是为真（即c不是控制字符、数字、字母、可打印空白中的一种）
isspace(c)  当c是空白时为真（即c是空格、制表符、纵向制表符、回车符、换行符、进纸符中的一种）
isupper(c)  当c是大写字母时为真
isxdigit(c)  当c是十六进制数字时为真
tolower(c)  如果c是大写字母，输出对应的小写字母；否则原样输出c
toupper(c)  如果c是小写字母，输出对应的大写字母；否则原样输出c
```

**处理string中的每个字符**
使用范围for语句遍历给定序列中的每个元素，并对序列中的每个值执行某种操作，语法如下：
```
for (declaration : expression)
    statement
```
expression是一个对象，用于表示一个序列。declaration部分负责定义一个变量，该变量被用于访问序列中的基础元素，每次迭代，declaration部分的变量会被初始化为expression部分的下一个元素值。

```
for(auto c : str)
    cout << c << endl;
```
使用auto关键字让编译器来决定变量c的类型，在这里c的类型是char。
使用范围for语句和ispunct函数来统计string对象中标点符号的个数。
```
decltype(s.size()) punct_cnt = 0; //punct_cnt的类型和s.size的返回类型一样
for (auto c : s)
    if(ispunct(c))
        ++punct_cnt;
cout << punct_cnt << "punctuation characters in " << s << endl;
```

**使用范围for语句改变字符串中的字符**
如果想要改变string对象中字符的值，必须把循环变量定义为引用类型。记住，所谓引用只是给定对象的一个别名，因此当使用引用作为循环控制变量时，这个变量实际上被绑定到了序列的每个元素上，使用这个引用，我们就能改变它绑定的字符。
```
/*把字符串改写成大写字母的形式*/
for(auto &c : s)
    c = toupper(c); //c是一个引用，因此赋值语句将改变s中字符的值
cout << s << endl;
```

**只处理一部分字符**
想访问string对象中的单个字符有两种方式：1、使用下标；2、迭代（后面讲到）
下标运算符（[]）接收的输入参数是string::size_type类型的值，这个参数表示要访问的字符的位置，大于0小于s.size()；返回值是该位置上字符的引用。
在对string对象使用下标前，先要确认那个位置上确实有值。

## 1.3 标准库类型vector
标准库类型vector表示对象的集合，其中所有对象的类型都相同。集合中的每个对象都有一个与之对应的索引，索引用于访问对象。因为vector“容纳着”其他对象，所以它也常备称为“容器”。使用vector，必须包含适当的头文件。
```
#include <vector>
using std::vector;
```
c++既有类模板，也有函数模板，其中vector是一个类模板。

模板本身不是类或函数，相反可以将模板看作为编译器生成类或函数编写的一份说明。编译器根据模板创建类或函数的过程称为**实例化**，当使用模板时，需要指出编译器应把类或函数实例化成何种类型。

对于类模板来说，我们通过提供一些额外信息来指定模板到底实例化成什么什么样的类，需要提供哪些信息由模板决定。提供信息的方式总是这样：即在模板名字后面跟一对尖括号，在尖括号内放上信息。

以vector为例，提供的额外信息是vector内所存放对象的类型：
```
vector<int> ivec; //ivec保存int类型的对象
vector<Sales_item> Sales_vec; //保存Sales_item类型的对象
vector<vector<string>> file; //该向量的元素时vector对象
```

vector是模板而非类型，由vector生成的类型必须包含vector中元素的类型，例如vector<int>。

### 1.3.1 定义和初始化vector对象
```
vector<T> v1   v1是一个空vector，它潜在的元素时T类型的，执行默认初始化
vector<T> v2(v1)  v2中包含有v1所有元素的副本，两个vector对象的类型必须相同
vector<T> v2 = v1  等价于v2(v1)，v2中包含有v1所有元素的副本
vector<T> v3(n, val)  v3包含了n个重复的元素，每个元素的值是val
vector<T> v4(n)  v4包含了n个重复地执行了值初始化的对象
vector<T> v5{a,b,c...}  v5包含了初始值个数的元素，每个元素被赋予相应的初始值
vector<T> v5={a,b,c...} 等价于v5{a,b,c...}
```

**列表初始化vector对象**
c++11新标准提供了另外一种为vector对象的元素赋初值的方法，即列表初始化。此时，用花括号括起来的0个或多个初始元素值被赋给vector对象。
```
vector<string> articles = {"a", "an", "the"};
vector<string> articles = ("a", "an", "the"); //错误
```
上述的vector对象包含3个元素：第一个是字符串“a”，第二个是字符串“an”，最后一个是字符串“the”。

c++语言提供了几种不同的初始化方式，大多是情况下这些初始化方式都可以相互等价的使用。不过有以下3中特殊情况：
1、使用拷贝初始化时（即使用=），只能提供一个初始值
2、如果提供的是一个类内初始值，则只能使用拷贝初始化或使用花括号的形式初始化
3、如果提供的是初始元素值的列表，则只能把初始值都放在花括号里进行列表初始化，而不能放在圆括号里。

**创建指定数量的元素**
```
vector<int> ivec(10, -1); //10个int类型的元素，每个元素都被初始化为-1
vector<string> svec(10, "hi!"); //10个string类型的元素，每个都被初始化为“hi!”
```

**值初始化**
只提供vector对象容纳的元素数量而忽略初始值，此时库会创建一个值初始化的元素初值，并把它赋给容器中的所有元素。这个初始值由vector对象中元素的类型决定。 
```
vector<int> ivec(10); //10个元素，每个都初始化为0
vector<string> svec(10); //10个元素，每个都是空string对象
```
### 1.3.2 向vector对象中添加元素
一般是创建一个空vector，在运行时利用vector的成员函数**push_back**向其中添加元素。push_back负责把一个值当成vector对象的尾元素“压”到vector对象的“尾端”。

*如果循环体内部包含有向vector对象添加元素的语句，则不能使用范围for循环。*

### 1.3.3 其他vector操作
```
v.empty()  如果v不含有任何元素，则返回真；否则返回假
v.size()   返回v中元素的个数
v.push_back(t)  向v的尾端添加一个值为t的元素
v[n]  返回v中第n个位置上元素的引用
v1 = v2  用v2中的元素拷贝替换v1中的元素
v1 = {a,b,c...}  用列表中的元素拷贝替换v1中的元素
v1 == v2  v1和v2相等当且仅当他们的元素数量相同且对应位置的元素值都相同
v1 != v2
<,<=,>,>=  以字典序进行比较
```

访问vector对象中元素的方法和访问string对象中字符的方法差不多，也是通过元素在vector中的位置。就和string的差不多

**计算vector内对象的索引**
使用下标运算符能获取到指定的元素。和string一样，vector对象的下标是从0开始计起，下标的类型是相应的size_type类型。
vector对象（以及string对象）的下标运算符可用于访问已存在的元素，而不能用于添加元素。

## 1.4 迭代器
