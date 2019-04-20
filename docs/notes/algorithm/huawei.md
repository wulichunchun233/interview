### 1、求int型数据在内存中存储时1的个数（47.51%）

**1.1 题目描述**

输入一个int型的正整数，计算出该int型数据在内存中存储时1的个数。

输入描述:
 输入一个整数（int类型）

输出描述:
 这个数转换成2进制后，输出1的个数

示例：
输入 5
输出 2

**1.2 解题**

```
#include <iostream>

using namespace std;

int main() {
    int n ;
    int counts = 0;
    cin >> n ;
    while(n){
        if (n%2 == 1)
            ++counts;
        n /= 2;
    }
    
    cout << counts <<endl;
   return 0;
}
```
### 2、数字颠倒（46.37% —— 字符串）

**2.1 题目描述**

输入一个整数，将这个整数以字符串的形式逆序输出

程序不考虑负数的情况，若数字含有0，则逆序形式也含有0，如输入为100，则输出为001

输入描述:
输入一个int整数

输出描述:
将这个整数以字符串的形式逆序输出

示例：
输入 1516000
输出 0006151

**2.2 解题**

```
#include <iostream>
#include <string>

using namespace std;

int main() {
    string a;
    cin >> a;
    int len = a.size();
    
    for (int i=len-1; i>=0; --i){
        cout << a[i];
    }
    
    return 0;
}
```

### 3、字符串最后一个单词的长度（23.35% —— 字符串）

**3.1 题目描述**

计算字符串最后一个单词的长度，单词以空格隔开。 

输入描述:
一行字符串，非空，长度小于5000。

输出描述:
整数N，最后一个单词的长度。

示例：
输入 hello world
输出 5

**3.2 解题**

```
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str ;
    int last_len = 0;
    getline(cin, str);
    int len = str.size();
    for(int i = len-1; i>=0; --i){
        if (isspace(str[i]))
            break;
        else
            ++last_len;
    }
    
    cout << last_len << endl;
    return 0;
}
```
### 4、计算字符个数（21.72%）

**4.1 题目描述**

写出一个程序，接受一个由字母和数字组成的字符串，和一个字符，然后输出输入字符串中含有该字符的个数。不区分大小写。

输入描述:
输入一个有字母和数字以及空格组成的字符串，和一个字符。

输出描述:
输出输入字符串中含有该字符的个数。

示例：
输入 ABCDEF A
输出 1

**4.2 解题**

```
#include <iostream>
#include <string>

using namespace std;

int main() {
    string input_str;
    char input_char;
    int res = 0;
    
    cin >> input_str;
    cin >> input_char;
    
    int len = input_str.size();
    
    for(int i=0; i<len; ++i){
        if (toupper(input_str[i]) == toupper(input_char))
            ++res;
    }
    
    cout << res <<endl;
    
    return 0;
}
```

### 5、字符串分隔（21.87% —— 字符串）

**5.1 题目描述**

•连续输入字符串，请按长度为8拆分每个字符串后输出到新的字符串数组； 
•长度不是8整数倍的字符串请在后面补数字0，空字符串不处理。 

输入描述:
连续输入字符串(输入2次,每个字符串长度小于100)

输出描述:
输出到长度为8的新字符串数组

示例1

输入

abc

123456789

输出

abc00000

12345678

90000000

**5.2 解题**

不对
```
#include <iostream>
#include <string>
#include <vector>

using namespace std;

int main() {
    string str1, str2;
    cin >> str1 >> str2;
    string str = str1 + ' ' +str2;
    vector<string> res;  

    for (int i=0; i<str.size(); ) {
        string tem_str(8,0);
        for (int j=0; j<8; ++j, ++i) {
            tem_str[j] = str[i];
        }
        res.push_back(tem_str);
    }
    
    for (auto a : res)
        cout << a << endl;
    
    return 0;   
}

```
结果

您的代码已保存
答案错误:您提交的程序没有通过所有的测试用例
case通过率为0.00%

用例:
urivthvtlqqerctlxmjvkgvfclaaduwmaadedpadanl
batkqdhjnrwtsmzidswdnenqpsblsszldyttytrgenaizwehntqiaaufble

对应输出应该为:

urivthvt
lqqerctl
xmjvkgvf
claaduwm
aadedpad
anl00000
batkqdhj
nrwtsmzi
dswdnenq
psblsszl
dyttytrg
enaizweh
ntqiaauf
ble00000

你的输出为:

urivthvt
lqqerctl
xmjvkgvf
claaduwm
aadedpad
anl batk

如果对上面的代码进行修改，判断空格（两各个字符串的拼接处），加上break语句，时间会超出限制

```
#include <iostream>
#include <string>
#include <vector>

using namespace std;

int main() {
    string str1, str2;
    cin >> str1 >> str2;
    string str = str1 + ' ' +str2;
    vector<string> res;  

    for (int i=0; i<str.size(); ) {
        string tem_str(8,0);
        for (int j=0; j<8; ++j, ++i) {
            if (isspace(str[i]))
                break;
            tem_str[j] = str[i];
        }
        res.push_back(tem_str);
    }
    
    for (auto a : res)
        cout << a << endl;
    
    return 0;
}
```

您的代码已保存
内存超限:您的程序使用了超过限制的内存
case通过率为0.00%

参考别人的正确解法：

***不要考虑算法，可以不用保存，直接处理输出 ***
```
#include <iostream>
#include <string>

using namespace std;

void putstr(string s){
    int i=0,j;
    for (i=0; i<s.size(); ++i) {
        cout << s[i];
        if ((i+1)%8 == 0)
            cout << endl;
    }
    for (j=i; (j)%8 != 0; j++)
        cout << 0;
}

int main() {
    string str1, str2;
    cin >> str1 >> str2;
    string str = str1 + ' ' +str2;
    putstr(str1);
    cout << endl;
    putstr(str2);
    return 0;   
}
```
### 6、进制转换（25% —— 字符串）

**6.1题目描述**

写出一个程序，接受一个十六进制的数值字符串，输出该数值的十进制字符串。（多组同时输入 ）

输入描述:
输入一个十六进制的数值字符串。

输出描述:
输出该数值的十进制字符串。

示例1
输入：0xA
输出：10

**6.2解题**

```
#include <iostream>
#include <string>
#include <cmath>  // pow

using namespace std;

void hex_to_ten(string str ){
    int len = str.size();
    int res = 0;
    //去掉0x
    for (int i = len-1; i>=2; --i){  
        int num = 0;
        // 处理 ‘A’-‘F’ 和 字符数字
        if ( str[i]== 'A' || str[i] == 'a')
            num = 10;
        else if ( str[i]== 'B' || str[i] == 'b')
            num = 11;
        else if ( str[i]== 'C' || str[i] == 'c')
            num = 12;
        else if ( str[i]== 'D' || str[i] == 'd')
            num = 13;
        else if ( str[i]== 'E' || str[i] == 'e')
            num = 14;
        else if ( str[i]== 'F' || str[i] == 'f')
            num = 15;
        else
            num = str[i] - '0';
        // 计算
        res += num*pow(16,len-i-1);   
    }
    cout << res << endl;
}

int main() {
    string str;
    while (cin >> str) {
        hex_to_ten(str);
    }
    return 0;
}

```

### 7、质数因子（27.45% —— 排序）

**7.1题目描述**

功能:输入一个正整数，按照从小到大的顺序输出它的所有质数的因子（如180的质数因子为2 2 3 3 5 ）

最后一个数后面也要有空格

详细描述：


函数接口说明：

public String getResult(long ulDataInput)

输入参数：

long ulDataInput：输入的正整数

返回值：

String

输入描述:
输入一个long型整数

输出描述:
按照从小到大的顺序输出它的所有质数的因子，以空格隔开。最后一个数后面也要有空格。

示例1
输入：180
输出：2 2 3 3 5

**7.2解题**

一般的人可能会考虑每一次遍历一遍2到N的全部的整数，找到一个质数因子a，然后N/=a，直到N等于1，但是这种方法其实就是暴力搜索，时间效率并不好

其实有一种更好的方法，就是设定i=2，i一直递增，当N%i==0的时候，N/=i，否则i++，直到i>N，这样找到的所有N%i==0的i就是N的所有的质数因子

但是这样为何可行呢，

我们假设从2开始，找到的第一个N%i==0的i为a1，首先a1一定是质数，因为假如a1是合数的话在2和a1之间一定存在其他N可以整除的质数，但是i是从2开始找到的第一个可以整除的数，因此i只能是质数，也就是说i是N最小的质因子

我们进行N/=i，直到（N/=i）!=0,这里得到的每一个i都是N的质因子，假如这个时候N还有其他质因子存在，那么N>i，否则```N<i```算法结束

这个时候从2到a1的所有的质因子都分解完毕

然后从a1继续往后找，找到第二个N可以整除的数a2，a2不可能是合数，因为假如a2是和数的话，2到a1，或者是a1到a2之间一定存在没有分解的质数，而这是不可能的，所以a2一定是质数，且是N第二大的质因子，进行N/=a2，直到（N/=a2）!=0

继续以上操作，当找到最后一个质因子的时候，N==i，这个时候```(N/=i)=1<i```，算法结束，至此，N的所有质因子都找到了

```
#include <iostream>
 
using namespace std;
 
int main()
{
      long int a;
      cin>>a;
      for(int i=2;i<=a;i++)
      {
            while((0==a%i)&&(a!=0))
            {
                  cout<<i<<" ";
                  a=a/i;
            }
      }
}
```


### 8、取近似值（44。25%）

**8.1题目描述**

写出一个程序，接受一个正浮点数值，输出该数值的近似整数值。如果小数点后数值大于等于5,向上取整；小于5，则向下取整。

输入描述:
输入一个正浮点数值

输出描述:
输出该数值的近似整数值

示例1
输入：5.5
输出：6

**8.2解题**

嗯，什么都不用，只要输出正确答案就行
```
#include <iostream>

using namespace std;

int main() {
    double x;
    cin >> x;
    int x1 = (int)x;
    double x2 = x - x1;
    int x3 = ( x2 >= 0.5 ) ? x1+1 : x1;
    cout << x3;
    return 0;
}
```

或者

思路：

正数好理解，就是我们常用的四舍五入，用输入的数+0.5然后取整输出就好。

关键是负数，这个问题描述的很不清楚，按理来说-5.5向上取整得到的应该是-5，可是，华为oj平台得到的结果是-6才是正确的，所以，如果是负数我们只能这么判断，先将这个数取反（得到正数），然后+0.5，然后用int来截断取整，最后输出的时候再将负号加回去。-（int）(-a+0.5) 或者 （int）(a-0.5)
```
#include <iostream>
using namespace std;
int main()
{
    float a;
    cin>>a;
    int num;
    if(a>0)
        num=(int)(a+0.5);
    else
        num=(int)(a-0.5);
    cout<<num<<endl;
    return 0;
}
```

### 9、合并表记录（29.69% —— 栈）

**9.1题目描述**

数据表记录包含表索引和数值，请对表索引相同的记录进行合并，即将相同索引的数值进行求和运算，输出按照key值升序进行输出。

输入描述:
先输入键值对的个数
然后输入成对的index和value值，以空格隔开

输出描述:
输出合并后的键值对（多行）

示例1
输入：

4

0 1

0 2

1 2

3 4

输出：

0 3

1 2

3 4

**9.2解题**
```
#include <iostream>
#include <map>
using namespace std;

int main() {
    int n = 0;
    cin >> n;
    map <int, int > arr;
    int value, key;
    
    while (--n >= 0) {
        cin >> key >> value;
        arr[key] += value;
    }
    
    for (map<int,int> :: iterator i = arr.begin() ; i != arr.end() ; ++i) {
        cout << i->first << ' ' << i->second << endl;
    }
    return 0;
}
```

### 10、提取不重复的整数（33.47%）

**10.1题目描述**

输入一个int型整数，按照从右向左的阅读顺序，返回一个不含重复数字的新的整数。

输入描述:
输入一个int型整数

输出描述:
按照从右向左的阅读顺序，返回一个不含重复数字的新的整数

示例1

输入：9876673

输出：37689

**10.2解题**

```
#include <iostream>
#include <set>

using namespace std;

int main() {
    int num ;
    int new_num = 0;
    cin >> num;
    
    set<int> s;
    
    while (num) {
        int n = num % 10;
        int len = s.size();
        s.insert(n);
        if (len != s.size())
            new_num = new_num*10 + n;
        num /= 10;
    }
    
    cout << new_num << endl;
    
    return 0;
    
}
```
### 11、字符个数统计（34.06%）

**11.1题目描述**

编写一个函数，计算字符串中含有的不同字符的个数。字符在ACSII码范围内(0~127)。不在范围内的不作统计。

输入描述:
输入N个字符，字符在ACSII码范围内。

输出描述:
输出范围在(0~127)字符的个数。

示例1
输入：abc
输出：3

**11.2解题**

```<=  >=```号要写对，char可以直接和int型比较

```
#include <iostream>
#include <string>
#include <set>
#include <stdlib.h>

using namespace std;

int main() {
    string s;
    while (cin >> s) {
        int counts = 0;
        set<char> sets;
        for (int i=0; i<s.size(); ++i) {
            int len = sets.size();
            sets.insert(s[i]);
            if (len != sets.size()) {
                if ( (0 <= s[i]) && ( 127 >= s[i]))
                    ++counts;
            }
        }
        cout << counts << endl;
    }
    return 0;
}
```


### 12、密码验证合格程序

**12.1题目描述**

密码要求:

1.长度超过8位

2.包括大小写字母.数字.其它符号,以上四种至少三种 

3.不能有相同长度超2的子串重复

说明:长度超过2的子串

输入描述:
一组或多组长度超过2的子符串。每组占一行

输出描述:
如果符合要求输出：OK，否则输出NG

输入：

021Abc9000
021Abc9Abc1
021ABC9000
021$bc9000

输出：

OK
NG
NG
OK

**12.2解题**

求解思路： 按照要求， 分别建立三个函数 check_lengh, check_char_kinds和check_substr_repeat。前两个函数难度不大，对于判断字符串内是否存在重复长度超二的子串，有两种解法：

1. 暴力搜索： 从下标开始搜索，取出长度为3的子字符串，然后利用std::string.find()进行查找；

2. 后缀数组方法：将字符串的子串 [i,n)存入vector容器suffix中，调用<algorithm>中的sort对子串按照字典序排列，遍历相邻子串suffix[i]与suffix[i+1]，求出其相同的字符数目，如果数目>2，即表示存在重复子串；

```
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

bool check_length(const string &password) {
    return password.size() > 8;
}


bool check_char_kinds(const string &password) {
    int digit{0}, lowercase{0}, uppercase{0}, others{0};
    
    for(int i=0; i<password.size(); ++i) {
        if( isdigit(password[i]) ){
            digit = 1;
            continue;
        } else if( islower(password[i]) ){
            lowercase = 1;
            continue;
        } else if( isupper(password[i]) ){
            uppercase = 1;
            continue;
        } else {
            others = 1;
            continue;
        }
    }
    return digit + lowercase + uppercase + others >= 3 ? true : false;
}

bool check_substr_repeat_1(const string &password) {
    // simple search and match
    for(int i=0; i<password.size() -3; ++i) {
        string str1 = password.substr(i,3);
        if( password.find(str1, i+1) != string::npos)  // ?
            return false;
    }
    return true;
}

bool check_substr_repeat_2(const string &password) {
    // 后缀数组法
    vector<string> suffix(password.size());
    for (int i=0; i<password.size() ; ++i){
        suffix[i] = password.substr(i);
    }
    sort(suffix.begin(), suffix.end());
    
    for(int i=0; i<suffix.size()-1; ++i) {
        int len = 0;
        int max_len = max(suffix[i].size(), suffix[i+1].size());
        int j = 0;
        while ( j<max_len && suffix[i][j]==suffix[i+1][j] ) {
            ++len;
            ++j;
            if (len > 2)
                return false;
        }
    }
    return true;
}

int main() {
    ios::sync_with_stdio(false);
    string ps;
    while(cin >> ps){
        if( check_length(ps) && check_char_kinds(ps) && check_substr_repeat_2(ps) )
            cout << "OK" <<endl;
        else 
            cout << "NG" <<endl;
    }
    return 0;
}
```

### 13、

**13.1题目描述**

**13.2解题**