# 前k个高频元素

题目链接：[347. 前 K 个高频元素 - 力扣（LeetCode）](https://leetcode.cn/problems/top-k-frequent-elements/)

题目要求：给你一个整数数组 `nums` 和一个整数 `k` ，请你返回其中出现频率前 `k` 高的元素。你可以按 **任意顺序** 返回答案。

C++ 代码：

```
class Solution {
public:
    class myCmp{
    public://这里自己定义的仿函数一定要是public的，不能是private的
        bool operator()(const pair<int,int>& a,const pair<int,int>& b)
        {
            return a.second > b.second;//注意，这里和快排里面自己定义的cmp函数不一样，如果是 >,就是小根堆，反之就是大根堆
        }
    };
    vector<int> topKFrequent(vector<int>& nums, int k) {
        //用c++自带的stl里的优先队列来做
        priority_queue<pair<int,int>,vector<pair<int,int>>,myCmp> pri_que;//注意定义的方式
        unordered_map<int,int>record;
        for(auto i:nums)
            record[i] ++;
        for(auto it = record.begin();it != record.end();it ++)
        {
            pri_que.push(*it);
            if(pri_que.size() > k)
                pri_que.pop();
        }
        vector<int>ans;
        while(k)
        {
            ans.push_back(pri_que.top().first);
            pri_que.pop();
            k --;
        }
        return ans;
    }
};
```

