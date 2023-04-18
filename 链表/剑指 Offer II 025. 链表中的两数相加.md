# 剑指 Offer II 025. 链表中的两数相加 

[剑指 Offer II 025. 链表中的两数相加 - 力扣（LeetCode）](https://leetcode.cn/problems/lMSNwu/)

**题目要求：** 给定两个 非空链表 l1和 l2 来代表两个非负整数。数字最高位位于链表开始位置。它们的每个节点只存储一位数字。将这两数相加会返回一个新的链表。

可以假设除了数字 0 之外，这两个数字都不会以零开头。

### 思路：

最简单的思路就是反转链表，然后依次相加。

**但是如果不想改变链表的结构的话，那就要利用栈来记录遍历过的链表节点，然后如果有进位了就不断出栈，把进位加上去，直到没有进位了为止。**



**用pre来记录上一个访问过的节点，用temp来记录当前已经建立好的链表的最后一个节点。**

```
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        //用栈记录遍历过的元素，如果进位是1就不断弹栈
        ListNode* cur_1 = l1;
        ListNode* cur_2 = l2;
        ListNode* dummyNode = new ListNode();
        ListNode* cur = dummyNode;
        bool flag = false;
        stack<ListNode*>sta;
        ListNode* pre = new ListNode();//记录在弹栈的过程中，上一次出栈的节点
        int len_1 = 0,len_2 = 0;//记录两个链表的长度
        while(cur_1 != nullptr)
        {
            cur_1 = cur_1->next;
            len_1 ++; 
        }
        while(cur_2 != nullptr)
        {
            cur_2 = cur_2->next;
            len_2 ++; 
        }
        cur_1 = l1;
        cur_2 = l2;
        int key = abs(len_1 - len_2);
        if(len_1 > len_2)
        {
            while(key)
            {
                cur->next = new ListNode(cur_1->val);
                sta.push(cur->next);
                cur = cur->next;
                cur_1 = cur_1->next;
                key --;
            }
        }else{
            while(key)
            {
                cur->next = new ListNode(cur_2->val);
                sta.push(cur->next);
                cur = cur->next;
                cur_2 = cur_2->next;
                key --;
            }
        }
        while(cur_1 != nullptr && cur_2 != nullptr)
        {
            int val = cur_1->val + cur_2->val;
            cur->next = new ListNode(val%10);
            ListNode* temp = cur->next;//先把cur的next给暂存起来
            pre = cur->next;
            if(val >= 10)
                flag = true;
            while(flag == true)
            {
                if(!sta.empty())
                {
                    pre = sta.top();
                    ListNode* node = sta.top();
                    sta.pop();
                    if(node->val == 9)//仍然有进位，那就继续往前pop
                    {
                        node->val = 0;
                    }else{
                        node->val += 1;
                        flag = false;
                    }
                }else{
                    //栈都空了，但是仍然有进位
                    ListNode* node = new ListNode(1);
                    node->next = pre;//指向刚刚出栈的节点
                    sta.push(node);
                    dummyNode->next = node;
                    flag = false;
                }
            }
            //sta.push(cur->next);//这里不能把cur的next给push进去了，因为在上面那个过程中，cur的next可能会变
            sta.push(temp);
            cur = temp;//cur要指向最后一个建立好的节点
            cur_1 = cur_1->next;
            cur_2 = cur_2->next;
        }
        return dummyNode->next;
    }
};
```

