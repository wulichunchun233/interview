# 牛客网刷题列表

## 2018校招真题

### 1. 找出最接近的对称数字

##### 题目描述：

输入一个正整数的字符串，输出与它最接近的对称数字(不包括它自己)的字符串

注1: 输入字符串的长度最多不会超过18

注2: 当大于输入数字和小于输入数字的对称数字与输入数字距离相同时，取小的数字作为答案 

##### 输入描述:

输入为一个正整数的字符串

##### 输出描述:

输出为与输入数字最接近的对称数字(不包括输入本身)的字符串

##### 思路:

为了使对称数字最接近，应该尽量保持高位不变，然后修改低位数字从而达到对称。我一开始想到的是反序，从中间位置，后一半和前一半对称填写，但考虑到这几种特殊情况就不会了

考虑比较特殊的数字如：
10,100,1000
101,1001,10001
99,999,9999

如果给定的数本来就是对称的呢

如果输入的数字不对称，直接截取前半部分反序输出
对称的话，取几个数字试验一下：999输出1001，99999输出100001，696输出686，6996输出7007，69996输出 70007，62926输出62826，6299926输出6300036，9899989输出9900099，9999999输出10000001，101输出99，202输出212，808输出818，1001输出999，1020201输出1021201。

从中得出的结论：
最中间数不是0和9的时候，将其减1。
9：最中间数有2个及其以上的9（1991，19991是，191不是），对称部分+1，否则-1。注意全为9时，特殊处理，（eg：999输出1001），这里输出位数和输入位数不同
0：最中间数仅有1个0时，对称部分+1（606输出616而不是595）（11011输出11111），这里需要注意101是个例外（输出99），因为它输入输出位数不一样。然后所有的100...001（n位）对应输出99..9（n-1位）

##### 代码:
```
//这个代码通过了，想不到如此简单，还有点不明白为什么
#include <iostream>
#include <string>
using std::string;
using std::cin;
using std::cout;
using std::endl;

int main() {
    string str;
    cin >> str;
    int size = str.size();
    int mid = size>>1;
    for(int i=0; i<mid; ++i) {
        *(str.end()-1-i) = *(str.begin()+i);
    }
    cout << str << endl;
    return 0;
}
```