### 1、求int型数据在内存中存储时1的个数

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
### 2、数字颠倒

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

### 3、字符串最后一个单词的长度

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
### 4、计算字符个数

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

### 5、字符串分隔

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
