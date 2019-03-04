# 一、数组简介
数组是一种基本的数据结构，用于按顺序存储元素的集合。但是可以随机存取，因为数组中的每个元素都可以通过数索引来识别。
数组可以有多个维度，从*一维数组*开始，它也被称为线性数组。

## 数组中的操作
(’’’)
//C++
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
(’’’)

# 二、动态数组简介
由于数组有固定的容量，我们需要在初始化时指定数组的大小，有时它会非常不方便并可能造成浪费。因此，大多数编程语言都提供内置的*动态数组*，它仍是一个随机存取的列表数据结构，但大小是可变的。例如在c++中的**vector**，以及在Java中的**ArrayList**。

## 动态数组中的操作
(’’’)
//C++
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
(’’’)

