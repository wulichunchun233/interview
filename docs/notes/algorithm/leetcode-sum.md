<!-- TOC -->

- [leetcode 整理](#leetcode-整理)
    - [技巧学习](#技巧学习)
        - [1.给cin加速](#1给cin加速)
    - [字符串](#字符串)
        - [1.分隔回文串](#1分隔回文串)

<!-- /TOC -->
# leetcode 整理
## 技巧学习
### 1.给cin加速
看到leetcode上排名前几的代码前都有这么一段代码，如下：
```
static const auto io_sync_off = []()
{
    // turn off sync
    std::ios::sync_with_stdio(false);
    // untie in/out streams
    std::cin.tie(nullptr);
    return nullptr;
}();
```
原来而cin，cout之所以效率低，是因为先把要输出的东西存入缓冲区，再输出，导致效率降低，上面的代码可以来打消iostream的输入/输出缓存，可以节省许多时间，使效率与scanf与printf相差无几。

*解析*

**1、Lambda**

先看一下这个语法：
```
static const auto io_sync_off = []()
{
    ...
}();
```
这个乍一看很像是函数，但前后又是[]又是()的，还没函数返回值的，它实际上是**Lambda捕获**

>Lambda表达式是C++11引入的特性，是一种描述函数对象的机制，它的主要应用是描述某些具有简单行为的函数。lambda也可以成为匿名函数。

上面的语法等价于：
```
static const auto function() {
    ...
}
static const auto io_sync_off = function();
```
**2、std::ios::sync_with_stdio(false);**

因为c++中的std::cin和std::cout为了兼容C，保证在代码中同时出现std::cin和scanf或std::cout和printf时输出不发生混乱，所以c++用一个缓冲区来同步C的标准流。通过std::ios_base::sync_with_stdio函数可以解除这种同步，让std::cin和std::cout不再经过缓冲区，自然就节省了许多时间。

**3、std::cin.tie(nullptr);**

因为std::cin默认是与std::cout绑定的，所以每次操作的时候（也就是调用“<<”或者“>>”）都要刷新（调用flush），这样增加了IO的负担，通过tie(nullptr)来解除std::cin和std::cout之间的绑定，来降低IO的负担使效率提升。

>*注意：* 使用std::ios::sync_with_stdio(false)和std::cin.tie(nullptr)之后要避免和scanf和printf混用以免出现问题。


## 字符串

### 1.分隔回文串

**思路：** 使用回溯和动深度遍历，对于字符串“aab”，第一次遍历的时候，先判断'a'是不是回文字符，我们发现是，所以将"a"加入list中，依次下去，第一次遍历完的时候result中加入了["a","a","b"]。第二次遍历的时候，我们发现"aa"是回文串，将"aa"加入list中，然后判断'b'是回文字符，将"b"加入到list中，第二次遍历完后result中包含了["aa","b"]；接着进行第三次遍历，发现"aab"不是回文串，并且输入字符的长度就是3，这时输出result。

**代码：**
```
class Solution {
    vector<vector<string>> res;
    
    void DFS(string& s, int begin, vector<string>& t){
        if(begin>=s.size())
            res.push_back(t);
        else{
            t.push_back("");
            for(int end=begin; end<s.size(); end++)
                if(isParl(s, begin, end)){
                    t[t.size()-1] = s.substr(begin, end-begin+1);
                    DFS(s, end+1, t);
                }
            t.pop_back();
        }
    }
    
    bool isParl(string& s, int begin, int end){
        while(begin < end){
            if(s[begin] != s[end])
                return false;
            begin++;
            end--;
        }
        return true;
    }
    
public:
    vector<vector<string>> partition(string s) {
        vector<string> t;
        DFS(s, 0, t);
        return res;
    }
    

};
```
