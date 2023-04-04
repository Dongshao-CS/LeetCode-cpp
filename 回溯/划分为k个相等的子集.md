# 划分为k个相等的子集

**题目链接**：[698. 划分为k个相等的子集 - 力扣（LeetCode）](https://leetcode.cn/problems/partition-to-k-equal-sum-subsets/)

**题目要求**：给定一个整数数组 `nums` 和一个正整数 `k`，找出是否有可能把这个数组分成 `k` 个非空子集，其总和都相等。

**总体策略：贪心 + 回溯剪枝**

首先对nums数组进行排序，然后按照「从大到小」的方式进行搜索，确保了当前集合的搜索，无须对已搜索到的集合进行调整。
也就是说我们搜索的第一个集合是所有nums[i] 中的最大值所在的那个集合；二次搜索是所有nums[i] 减去第一个集合后剩余元素中最大值所在的集合 。这引导我们，如果当前集合如果连第一个值都无法搜到（即剩余元素的最大值不能作为当前集合的元素），必然无解（可行性剪枝）（就是那个cur_sum == 0;return false的那个剪枝）。

这样的**「搜索 + 剪枝」**的解法本质是利用了**「贪心」**来做策略：我们每个回合的搜索总是在搜索**「剩余未使用元素的最大值」**所在的那个集合，并且按照**「优先使用大数值」**的原则来构造。

这道题其实是组合问题，**然后只要有一组组合满足条件就可以返回true了。**

剩余思路见代码注释：

```
class Solution {
public:
    int len = 0;
    bool canPartitionKSubsets(vector<int>& nums, int k) {
        //其实是组合问题，只要有一组组合满足条件就可以返回true了
        len = nums.size();
        vector<bool> record(len,false);//记录每个位置上的元素是否被使用过
        int sum = 0;
        for(auto i:nums)
            sum += i;
        if(sum%k)
            return false;
        int per = sum/k;//平均值是per
        sort(nums.begin(),nums.end());//排序的目的是，从后往前找，贪心地先找大的元素，看看大的元素是否可以找到对应的集合满足条件，如果没有就直接是false
        //因为原理就是，大的元素不好拼凑出per来，而小的元素相对来说更容易拼凑，所以优先解决大的元素的所在集合问题，剩下的小的元素就更好解决了
        //就比如k == 2，nums是{10，10，5，2，3}，肯定是优先选5，然后剩下2和3，因为总和虽然一样，2和3可以拼凑更多，所以优先用大的5.
        //后面如果有元素也需要5来拼凑集合，那就去5后面找更小的数去拼凑，找不到的话也不给换的，就直接是false了
        return dfs(k,0,per,0,len - 1,record,nums);
    }
    bool dfs(int k,int count,int per,int cur_sum,int index,vector<bool>& record,vector<int>& nums)//count用来记录当前已经找到多少个满足条件的子集了，cur_sum是当前正在寻找的子集的加和。
    //index是分割点的坐标，index右边的元素就是已经被寻找过了，要往index左边去寻找
    {
        //设置递归终止条件
        if(count == k)
            return true;
        if(cur_sum == per)//count加一
            return dfs(k,count + 1,per,0,len - 1,record,nums);
        for(int i = index;i >= 0;i --)
        {
            if(record[i] == true || cur_sum + nums[i] > per)//剪枝
                continue ;
            record[i] = true;
            if( dfs(k,count,per,cur_sum + nums[i],i - 1,record,nums) == true )
                return true;
            record[i] = false;
            if(cur_sum == 0)//这个剪枝很有讲头，意思就是当前元素是该集合最大的一个数，然后往后面找了一圈都没找到合适的数能组成集合
            //即如果当前集合如果连第一个值都无法搜到（即剩余元素的最大值不能作为当前集合的元素）,那就是false
                return false;
        }
        return false;//当前集合找不到解，返回false
    }
};
```

