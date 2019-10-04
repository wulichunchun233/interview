## 1 数组
### 一、数组简介
数组是一种基本的数据结构，用于按顺序存储元素的集合。但是可以随机存取，因为数组中的每个元素都可以通过数索引来识别。
数组可以有多个维度，从*一维数组*开始，它也被称为线性数组。

#### 数组中的操作

```C++
#include <iostream>
int main() {
    //1、初始化操作
    int a0[5];
    int a1[5] = {1, 2, 3}; //其他的元素将被初始化为默认值

    //2、获取数组的长度
    int size = sizeof(a1) / sizeof(*a1);
    cout << "The size of a1 is :" << size << endl;

    //3、获取数组中的元素
    cout << "The first element of a1 is : " << a1[0] << endl;

    //4、遍历数组中的元素
    cout << "[version 1] The contents of a1 are :";
    for (int i=0; i<size ; ++i) {
        cout << " " << a1[i];
    }
    cout << endl;
    cout << "[version 2] The contents of a1 are:";
    for (int& item : a1) {
        cout << " " << item;
    }
    cout << endl;

    //5、修改数组中的元素
    a1[0] = 4;

    //6、排序
    sort(a1, a1+size);
}
```

### 二、动态数组简介
由于数组有固定的容量，我们需要在初始化时指定数组的大小，有时它会非常不方便并可能造成浪费。因此，大多数编程语言都提供内置的*动态数组*，它仍是一个随机存取的列表数据结构，但大小是可变的。例如在c++中的**vector**，以及在Java中的**ArrayList**。

#### 动态数组中的操作

```C++
#include <iostream>

int main() {
    //1、初始化
    vector<int> v0;
    vector<int> v1(5, 0);

    //2、拷贝
    vector<int> v2(v1.begin(), v1.end());
    vector<int> v3(v2);

    //3、将数组array映射为动态数组vector
    int a[5] = {0, 1, 2, 3, 4};
    vector<int> v4(a, *(&a+1));

    //4、获取数组长度
    cout << "The size of v4 is : " << v4.size() << endl;

    //5、获取数组元素
    cout << "[version 1] The contents of v4 are :";
    for (int i=0; i<v4.size(; ++i)>) {
        cout << " " << v4[i];
    }
    cout << endl;

    cout << "[version 2] The contents of v4 are :";
    for (int& item : v4) {
        cout << " " << item;
    }
    cout << endl;

    cout << "[version 3] The contents of v4 are :";
    for (auto item = v4.begin(); item != v4.end(); ++item) {
        cout << " " << *item;
    }
    cout << endl;

    //6、修改元素值
    v4[0] = 5;

    //7、排序
    sort(v4.begin(), v4.end());

    //8、在数组末端增加一个元素
    v4.push_back(-1);

    //9、删除数组中最后一个元素
    v4.pop_back();
}
```


## 2 优先队列

priority_queue， 头文件```<queue>```

优先队列和队列的使用方式的区别是优先队列在插入元素时，在将元素插入队尾后还要根据比较值对该元素进行位置的调整。缺省的情况是一个大堆，从大到小排列

优先级队列区别于普通队列的一点是：优先级队列如果插入的节点是结构体类型，则要在结构体中重载比较操作符函数。

### 一、声明

优先队列声明的基本格式：
```
priority_queue <结构类型> 队列名；
```
比如：
```
priority_queue <int> i; （从大到小排序）
priority_queue <double> d;
```
不过，我们最常用的的这几种：
```
priority_queue <node> q;
// node是一个结构体
// 结构体里重载了'<'小于符号
priority_queue <int, vector<int>, greater<int> > q;
// 注意不要#include<vector>文件
// 注意后面两个'>'不要写在一起，">>"是右移符号
priority_queue <int, vector<int>, less<int> > q;
```

### 二、基本操作

与队列的基本操作如出一辙，以一个名为q的优先队列为例：
```
q.size();    // 返回q里元素个数
q.empty();   // 返回q是否为空，空则返回为1，否则返回0
q.push(k);   // 在q的末尾插入k
q.pop();     // 删掉q的第一个元素
q.top();     // 返回q的第一个元素
q.back();    // 返回q的末尾元素
```

#### 默认的优先队列（结构体，重载小于）

先看看这个结构体是什么
```
struct node {
    int x, y;
    bool operator < (const node &a) const
    {
        return x < a.x;
    }
};
```
这个node结构体有两个成员，x和y，它的小于规则是x小者小。再来看验证程序：
```
#include <cstdio>
#include <queue>
using namespace std;

struct node {
    int x, y;
    bool operator < (const node &a) const
    {
        return x < a.x;
    }
}k;

priority_queue <node> q;

int main() 
{
    k.x = 10, k.y = 100; q.push(k);
    k.x = 12, k.y = 60; q.push(k);
    k.x = 14, k.y = 40; q.push(k);
    k.x = 6, k.y = 80; q.push(k);
    k.x = 8, k.y = 20; q.push(k);
    while (!q.empty()) {
        node m = q.top();
        q.pop();
        printf("(%d,%d)", m.x, m.y);
    }
}

```
程序大意就是插入(10,100),(12,60),(14,40),(6,20),(8,20)这五个node。 
再来看看它的输出： 

(14,40) (12,60) (10,100) (8,20) (6,80)

#### less和greater优先队列

greater中使用了“>”,所以在使用greater的时候应该重载“>”, 

less中使用了“<”,所以在使用less或者在缺省的情况下应该重载“<”。 

还是以int为例，先来声明：
```
priority_queue <int, vector<int>, less<int> > p;     // 默认的另一种写法
priority_queue <int, vector<int>, greater<int> > q;  // 从低到高
```

程序和结果如下：
```
#include <cstdio>
#include <queue>
using namespace std;

priority_queue <int, vector<int>, less<int> > p;
priority_queue <int, vector<int>, greater<int> > q;

int a[5] = {10, 12, 14, 6, 8};

int main() {
    for (int i=0; i<5; i++){
        p.push(a[i]);       
        q.push(a[i]);
    }
    printf("less<int>:")
    while(!p.empty())
        printf("%d ",p.top()),p.pop();  

    pritntf("\ngreater<int>:")
    while(!q.empty())
        printf("%d ",q.top()),q.pop();      
}
```
结果： 
```
less<int>:14 12 10 8 6 
greater<int>:6 8 10 12 14
```

所以，我们可以知道，less是从大到小，greater是从小到大。

### 三、特殊

在函数重载的规定中又指出，重载的操作对象只能是类类型或枚举类型，那么如果优先级队列中存放的是指针类型的对象就只能使用另一种方法，使用仿函数，传递一个优先级比较的算法到优先级队列中。

```
priority_queue <Node*, vector<Node*>, Compare > qi
```
这里的Node*是指队列中存储的是指向Node的结构体的指针，此时就不能使用标准库中用<操作符来实现优先级排序的这种方式了。这里我们实现一个类使用仿函数传入一个比较的算法，就可以实现优先级的判断了。 如下例：

```
struct Node
{
    int priotiry;
    char val;
    Node(char ch, int pri)
        :val(ch)
        ,priotiry(pri)
    {}

    char getval()
    {
        return val;
    }
};

struct cmp
{
    bool operator()(const Node *n1, const Node *n2) const
    {
        return n1->priotiry < n2->priotiry;
    }
};


int main()
{
    Node *n1 = new Node('a',5);
    Node *n2 = new Node('b',4);
    Node *n3 = new Node('c',6);
    Node *n4 = new Node('d',3);
    Node *n5 = new Node('e',1);

    priority_queue<Node*,vector<Node*>,cmp> p1;
    p1.push(n1);
    p1.push(n2);
    p1.push(n3);
    p1.push(n4);
    p1.push(n5);

    for (int i = 0; !p1.empty(); ++i)
    {
        cout << p1.top()->getval() << " ";
        p1.pop();
    }
    return 0;
}
```
结果：

c a b d e

### 四、应用
[leetcode ]前k个高频元素

**题目描述：**

给定一个非空的整数数组，返回其中出现频率前 k 高的元素。

示例 1:

输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]

示例 2:

输入: nums = [1], k = 1
输出: [1]

说明：

1、你可以假设给定的 k 总是合理的，且 1 ≤ k ≤ 数组中不相同的元素的个数。

2、你的算法的时间复杂度必须优于 O(n log n) , n 是数组的大小。

**思路：**

这道题给了我们一个数组，让我们统计前k个高频的数字，那么对于这类的统计数字的问题，首先应该考虑用HashMap来做，建立数字和其出现次数的映射，然后再按照出现次数进行排序。我们可以用堆排序来做，使用一个最大堆来按照映射次数从大到小排列，在C++中使用priority_queue来实现，默认是最大堆：

```
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        vector<int> res;
        unordered_map<int, int> m;
        priority_queue<pair<int,int> > q;
        
        for (auto a: nums) {
            ++m[a];
        }
        
        for (auto it: m) {
            q.push({it.second, it.first});
        }
        
        for (int i=0; i<k; ++i) {
            res.push_back(q.top().second);
            q.pop();
        }
        
        return res;
        
    }
};
```