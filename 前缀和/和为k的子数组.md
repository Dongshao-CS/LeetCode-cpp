# 和为k的子数组

[560. 和为 K 的子数组 - 力扣（LeetCode）](https://leetcode.cn/problems/subarray-sum-equals-k/)

给你一个整数数组 `nums` 和一个整数 `k` ，请你统计并返回 *该数组中和为 `k` 的连续子数组的个数* 。

**解析：**

我们只需要求出满足条件的子数组的个数，不用求出具体的下标，所以，不一定需要两次循环遍历。

关键是问题的转化：原题意可以等价为：有几种 i、j 的组合，满足 prefixSum[j] − prefixSum[i − 1] == k。

我们可以用map来记录（又是牺牲空间换取时间的做法）遍历过的所有sum所出现的**频率**。然后遍历到当前元素的时候，求出sum（**从第一个元素开始累加到当前元素**），看看之前有没有出现sum - k，如果右，就说明有子数组的和是k，然后把对应的sum - k的出现频率加上去就可以。

```
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int ans = 0;
        unordered_map<int,int>record;
        record[0] = 1;//初始化很重要
        int sum = 0;
        for(auto num:nums)
        {
            sum += num;
            if(record.find(sum - k) != record.end())
                ans += record[sum - k];
            record[sum] ++;
        }
        return ans;
    }
};
```

