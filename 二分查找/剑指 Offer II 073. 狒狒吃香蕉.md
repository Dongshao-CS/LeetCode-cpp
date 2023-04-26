# 剑指 Offer II 073. 狒狒吃香蕉

**题目要求：**

狒狒喜欢吃香蕉。这里有 n 堆香蕉，第 i 堆中有 piles[i] 根香蕉。警卫已经离开了，将在 h 小时后回来。

狒狒可以决定她吃香蕉的速度 k （单位：根/小时）。每个小时，她将会选择一堆香蕉，从中吃掉 k 根。如果这堆香蕉少于 k 根，她将吃掉这堆的所有香蕉，然后这一小时内不会再吃更多的香蕉，下一个小时才会开始吃另一堆的香蕉。  

狒狒喜欢慢慢吃，但仍然想在警卫回来前吃掉所有的香蕉。

返回她可以在 h 小时内吃掉所有香蕉的最小速度 k（k 为整数）。

```
class Solution {
public:
    int minEatingSpeed(vector<int>& piles, int h) {
        int max_num = 0;
        for(auto num:piles)
            max_num = max(max_num,num);
        int left = 1;
        int right = max_num;
        while(left <= right)
        {
            int mid = (right - left)/2 + left;
            int time = getHours(piles,mid);
            if(time <= h)//够快了，但是不确定是不是最小速度，还需要进一步去验证
            {
                if(mid == 1 || getHours(piles,mid - 1) > h)//mid最小就是1，要保证被除数不能是0
                    return mid;
                else
                    right = mid - 1;
            }else{//吃太慢了，要更快一点
                left = mid + 1;
            }
        }
        return 0;
    }
    int getHours(vector<int>& piles,int k)//k是每小时吃掉的香蕉数目
    {
        int ans = 0;
        for(auto num:piles)
        {
            ans += num/k;
            if(num%k != 0)
                ans ++;
        }
        return ans;
    }
};
```

