# 整数除法

[剑指 Offer II 001. 整数除法 - 力扣（LeetCode）](https://leetcode.cn/problems/xoh6Oh/)

给定两个整数 a 和 b ，求它们的除法的商 a/b ，要求不得使用乘号 '*'、除号 '/' 以及求余符号 '%' 。

 

注意：

整数除法的结果应当截去（truncate）其小数部分，例如：truncate(8.345) = 8 以及 truncate(-2.7335) = -2
假设我们的环境只能存储 32 位有符号整数，其数值范围是 [−231, 231−1]。本题中，如果除法结果溢出，则返回 231 − 1

```
class Solution {
public:
    int divide(int dividend, int divisor) {
        if(dividend == INT_MIN && divisor == -1)//此时越界了
            return INT_MAX;
        if(dividend == 0)
            return 0;
        if(divisor == 1)
            return dividend;
        if(divisor == -1)
            return -dividend;
        int flag = 1;
        if( (dividend < 0 && divisor > 0) || (dividend > 0 && divisor < 0) )//除数和被除数异号
            flag = -1;//-1代表异号
        if(dividend > 0)
            dividend = -dividend;
        if(divisor > 0)
            divisor = -divisor;
        int res = divideCore(dividend,divisor);
        return flag == -1?-res:res;
    }
    int divideCore(int dividend, int divisor)//注意，这个函数使用的是两个变量是负数的情况。之所以都是负数，是因为负数转正数有可能会越界，但是正数转负数不会，所以就统一用负数来做了
    {
        int ans = 0;
        while(dividend <= divisor)//因为都是负数，所以这里是小于号
        {
            int val = divisor;
            int count = 1;
            while(dividend - val <= val)
            {
                val += val;
                count += count;
            }
            dividend -= val;
            ans += count;
        }
        return ans;
    }
};
```

不会的话就去看剑指offer2.