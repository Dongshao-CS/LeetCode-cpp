# 合并k个升序链表

[23. 合并 K 个升序链表 - 力扣（LeetCode）](https://leetcode.cn/problems/merge-k-sorted-lists/)

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。



**思路：**
利用优先队列求解：

```
/**
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    struct cmp
    {
        bool operator()(const ListNode* a,const ListNode* b)
        {
            return a->val > b->val;//小顶堆
        }
    };
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        ListNode* dummyNode = new ListNode();
        ListNode* cur = nullptr;
        dummyNode->next = cur;
        ListNode* pre = dummyNode;
        priority_queue<ListNode*,vector<ListNode*>,cmp>pri_que;
        for(int i = 0;i < lists.size();i ++)
        {
            if(lists[i] != nullptr)
                pri_que.push(lists[i]);
        }
        while(!pri_que.empty())
        {
            cur = pri_que.top();
            pri_que.pop();
            pre->next = cur;
            if(cur->next != nullptr)
                pri_que.push(cur->next);
            pre = cur;
        }
        return dummyNode->next;
    }
};
```

