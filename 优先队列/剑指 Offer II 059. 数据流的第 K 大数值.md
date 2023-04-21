# 剑指 Offer II 059. 数据流的第 K 大数值 

设计一个找到数据流中第 k 大元素的类（class）。注意是排序后的第 k 大元素，不是第 k 个不同的元素。

请实现 KthLargest 类：

KthLargest(int k, int[] nums) 使用整数 k 和整数流 nums 初始化对象。
int add(int val) 将 val 插入数据流 nums 后，返回当前数据流中第 k 大的元素。

输入：
["KthLargest", "add", "add", "add", "add", "add"]
[[3, [4, 5, 8, 2]], [3], [5], [10], [9], [4]]
输出：
[null, 4, 5, 5, 8, 8]

解释：
KthLargest kthLargest = new KthLargest(3, [4, 5, 8, 2]);
kthLargest.add(3);   // return 4
kthLargest.add(5);   // return 5
kthLargest.add(10);  // return 5
kthLargest.add(9);   // return 8
kthLargest.add(4);   // return 8

```
class KthLargest {
public:
    priority_queue<int,vector<int>,greater<int> >que;//注意，这里用的是小顶堆，堆顶存放的是最小的元素，每次pop出最小的元素，剩下的堆顶元素就是剩余元素李最小的。当堆的大小是k的时候，堆顶就是第k大的元素
    int key = 0;
    KthLargest(int k, vector<int>& nums) {
        key = k;
        for(auto i:nums)
            que.push(i);
    }
    int add(int val) {
        que.push(val);
        while(que.size() > key)
        {
            que.pop();
        }
        return que.top();
    }
};

/**
 * Your KthLargest object will be instantiated and called as such:
 * KthLargest* obj = new KthLargest(k, nums);
 * int param_1 = obj->add(val);
 */
```

