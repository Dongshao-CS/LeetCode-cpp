
# 前言

不久前记录了单调队列的用法，而优先队列则是和单调队列很相似的一种数据结构。

普通的队列是一种先进先出的数据结构，元素在队列尾追加，而从队列头删除。

在优先队列中，元素被赋予优先级。当访问元素时，具有最高优先级的元素最先删除。优先队列具有最高级先出的行为特征。在优先队列里，元素被储存在"堆里面"，里面的元素是大致有序的（不是严格有序的），但是每次pop出来的元素，都是最大（或者最小）的元素，然后pop出去之后，再重新调整堆内的元素，使最大（最小）的元素处于最顶端。

# C++中优先队列的实现

**和队列基本操作相同**：

top 访问队头元素

empty 队列是否为空

size 返回队列内元素个数

push 插入元素到队尾 (并排序)

emplace 原地构造一个元素并插入队列

pop 弹出队头元素

swap 交换内容

定义： **priority_queue**

Type 就是数据类型，Container 就是容器类型（Container必须是用数组实现的容器，比如vector,deque等等，但不能用 list。STL里面默认用的是vector），Functional 就是比较的方式。

当需要用自定义的数据类型时才需要传入这三个参数；

```arduino

priority_queue <int,vector<int>,greater<int> > q;

priority_queue <int,vector<int>,less<int> >q;

复制代码
```

## **什么是大顶堆和小顶堆**

**一、什么是堆**
堆是一种非线性结构，可以把堆看作一棵二叉树，也可以看作一个数组，即： **堆就是利用完全二叉树的结构来维护的一维数组**。
堆可以分为大顶堆和小顶堆。
**大顶堆**：每个结点的值都大于或等于其左右孩子结点的值。
**小顶堆**：每个结点的值都小于或等于其左右孩子结点的值。
如果是排序， **求升序**用大顶堆， **求降序**用小顶堆。
一般我们说 `topK` 问题，就可以用大顶堆或小顶堆来实现，
**最大的 K 个**：小顶堆
**最小的 K 个**：大顶堆
**二、大顶堆的构建过程**
**大顶堆的构建过程就是从最后一个非叶子结点开始从下往上调整。**
**最后一个非叶子节点怎么找**？这里我们用数组表示待排序序列，则最后一个非叶子结点的位置是：数组长度/2-1。假如数组长度为9，则最后一个非叶子结点位置是 9/2-1=3。
比较当前结点的值和左子树的值，如果当前节点小于左子树的值，就交换当前节点和左子树；
交换完后要检查左子树是否满足大顶堆的性质，不满足则重新调整子树结构；
再比较当前结点的值和右子树的值，如果当前节点小于右子树的值，就交换当前节点和右子树；
交换完后要检查右子树是否满足大顶堆的性质，不满足则重新调整子树结构；
无需交换调整的时候，则大顶堆构建完成。

在C++优先队列里面，对于基础类型 **默认是大顶堆**，比如priority_queue< int > a，等同于 priority_queue

priority_queue

下面是用pair做优先队列元素的例子：

```arduino

using namespace std;
int main()
{
    priority_queueint, int> > a;
    pair<int, int> b(1, 2);
    pair<int, int> c(1, 3);
    pair<int, int> d(2, 5);
    a.push(d);
    a.push(c);
    a.push(b);
    while (!a.empty())
    {
        cout << a.top().first << ' ' << a.top().second << '\n';
        a.pop();
    }
}
复制代码
```

输出是：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d16119f84ce24d93bbc096c61641ace7~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.image?)

下面是用自定义数据类型做优先队列元素的例子：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9c5e8fc6bc304bae8830e563881da720~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.image?)

# 经典题目

## 一、前k个高频元素

题目链接：[347. 前 K 个高频元素 - 力扣（LeetCode）](https://link.juejin.cn?target=https%3A%2F%2Fleetcode.cn%2Fproblems%2Ftop-k-frequent-elements%2F "https://leetcode.cn/problems/top-k-frequent-elements/")

题目要求：给你一个整数数组 `nums` 和一个整数 `k` ，请你返回其中出现频率前 `k` 高的元素。你可以按 **任意顺序** 返回答案。

C++ 代码：

```arduino
class Solution {
public:
    class myCmp{
    public:
        bool operator()(const pair<int,int>& a,const pair<int,int>& b)
        {
            return a.second > b.second;
        }
    };
    vector<int> topKFrequent(vector<int>& nums, int k) {

        priority_queueint,int>,vectorint,int>>,myCmp> pri_que;
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
复制代码
```

## 二、丑数

题目链接：[264. 丑数 II - 力扣（LeetCode）](https://link.juejin.cn?target=https%3A%2F%2Fleetcode.cn%2Fproblems%2Fugly-number-ii%2F "https://leetcode.cn/problems/ugly-number-ii/")

题目要求：给你一个整数 `n` ，请你找出并返回第 `n` 个 **丑数** 。

**丑数** 就是只包含质因数 `2`、 `3` 和/或 `5` 的正整数。

思路：首先，将最小的丑数1入堆，每次pop出最小的丑数x，而且2x,3x,5x也都是丑数，分别都压入堆中 。如此重复n次，第n个pop出来的元素就是第n个丑数。

```scss
class Solution {
public:
    int nthUglyNumber(int n) {

        priority_queue,greater >que;
        que.push(1);
        unordered_setrecord;
        long ans = 0;
        while(n)
        {
            ans = que.top();
            que.pop();
            n --;
            if(record.find(2*ans) == record.end())
            {
                que.push(2*ans);
                record.insert(2*ans);
            }
            if(record.find(3*ans) == record.end())
            {
                que.push(3*ans);
                record.insert(3*ans);
            }
            if(record.find(5*ans) == record.end())
            {
                que.push(5*ans);
                record.insert(5*ans);
            }
        }
        return ans;
    }
};
复制代码
```
