# leetcode 整理

## 字符串

### 1.分隔回文串

**思路：**使用回溯和动深度遍历，对于字符串“aab”，第一次遍历的时候，先判断'a'是不是回文字符，我们发现是，所以将"a"加入list中，依次下去，第一次遍历完的时候result中加入了["a","a","b"]。第二次遍历的时候，我们发现"aa"是回文串，将"aa"加入list中，然后判断'b'是回文字符，将"b"加入到list中，第二次遍历完后result中包含了["aa","b"]；接着进行第三次遍历，发现"aab"不是回文串，并且输入字符的长度就是3，这时输出result。

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
