# 剑指 Offer II 002. 二进制加法

[剑指 Offer II 002. 二进制加法 - 力扣（LeetCode）](https://leetcode.cn/problems/JFETK5/)

**题目要求：**

 给定两个 01 字符串 `a` 和 `b` ，请计算它们的和，并以二进制字符串的形式输出。

输入为 **非空** 字符串且只包含数字 `1` 和 `0`。

```
class Solution {
public:
    string addBinary(string a, string b) {
        string s = "";
        reverse(a.begin(),a.end());
        reverse(b.begin(),b.end());
        int flag = 0;//设置进位标识
        int i = 0;
        while(i < a.size() || i < b.size())
        {
            int num_a = i >= a.size()?0:a[i] - '0';
            int num_b = i >= b.size()?0:b[i] - '0';
            int sum = num_a + num_b + flag;
            if(sum == 3)
            {
                flag = 1;
                s.push_back('1');
            }else if(sum == 2){
                flag = 1;
                s.push_back('0');
            }else{
                flag = 0;
                s.push_back(sum + '0');
            }
            i ++;
        }
        if(flag == 1)//此时仍然有进位，说明上面没有进if，两个串的长度相等，最后需要再加一个进位，比如10 + 10.最后应该是100.
            s.push_back('1');
        reverse(s.begin(),s.end());
        return s;
    }
};
```

