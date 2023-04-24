# 剑指 Offer II 061. 和最小的 k 个数对 

[剑指 Offer II 061. 和最小的 k 个数对 - 力扣（LeetCode）](https://leetcode.cn/problems/qn8gGX/?envType=study-plan-v2&id=coding-interviews-special)

给定两个以升序排列的整数数组 nums1 和 nums2 , 以及一个整数 k 。

定义一对值 (u,v)，其中第一个元素来自 nums1，第二个元素来自 nums2 。

请找到和最小的 k 个数对 (u1,v1),  (u2,v2)  ...  (uk,vk) 。

**思路：**

首先要注意，自己定义仿函数的时候，operator后面要加括号。

**这道题用最大堆来做，维护的是k个最小的元素，如果遇到更大的元素就不管，不入堆。遇到更小的元素就入队，然后看看que的大小是否超过了k，超过了就pop**

```
class Solution {
public:
    class mycmp
    {
    public:
        bool operator()(const pair<int,int>& p1,const pair<int,int>& p2)
        {
            return p1.first + p1.second < p2.first + p2.second;//大顶堆
        }
    };
    vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
        int len_1 = nums1.size();
        int len_2 = nums2.size();
        vector<vector<int>>ans;
        priority_queue< pair<int,int>,vector<pair<int,int>>,mycmp >que;
        for(int i = 0;i < len_1;i ++)
        {
            for(int j = 0;j < len_2;j ++)
            {
                int sum = nums1[i] + nums2[j];
                if(que.size() == k && sum >= que.top().first + que.top().second)
                    break ;//因为数组是升序排列的，此时sum已经都大于队列的最大元素了，那么当前这一趟继续遍历下去肯定更大于栈顶元素，就没有必要继续遍历了
                que.push( pair<int,int>(nums1[i],nums2[j]) );
                if(que.size() > k)
                    que.pop();
            }
        }
        int size = que.size();
        for(int i = 0;i < size;i ++)//之所以遍历que的size而不是k，是因为最后两个数组可能凑不齐k个数，但是que的size是肯定小于等于k的，所以以size为准
        {
            ans.push_back(vector<int>{que.top().first,que.top().second});
            que.pop();
        }
        return ans;
    }
};
```

