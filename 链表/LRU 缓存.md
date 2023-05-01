# LRU 缓存

[146. LRU 缓存 - 力扣（LeetCode）](https://leetcode.cn/problems/lru-cache/)

题目要求：

请你设计并实现一个满足  LRU (最近最少使用) 缓存 约束的数据结构。
实现 LRUCache 类：
LRUCache(int capacity) 以 正整数 作为容量 capacity 初始化 LRU 缓存
int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。
函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。

**解析：**
**用链表和map实现**。 

为了能够实现get，快速查询链表中有没有存在指定的key，可以用map存放key，然后key对应的就是相应的节点，存放的是链表节点指针的类型。如果存在要查找的key，那就利用map快速定位到相应的节点，然后把节点的value取出来即可。

然后每次get和put之后的节点要**移动**到头节点上来，表示最近被使用。**越靠近尾节点，就越久未使用。**

由于要移动节点（就是把节点从链表中移除，然后挪到头节点的位置上来），**所以只能用双向链表来实现**。不然按照单向链表，只能是从头往后面开始遍历，时间复杂度就不是O（1）了。

要记得，当put之后链表长度超过capacity之后，要及时把尾部元素删除，然后对应的在map里的记录也要删除。

```
struct listNode
{
    int key;
    int value;
    listNode* pre;
    listNode* next;
    listNode():key(0),value(0),pre(nullptr),next(nullptr){};
    listNode(int key,int value):key(key),value(value),pre(nullptr),next(nullptr){}
    listNode(int key,int value,listNode* pre,listNode* next):key(key),value(value),pre(pre),next(next){}
};
class LRUCache {
private:
    int size;
    int capacity;
    unordered_map<int,listNode*>record;
    listNode* head = new listNode();
    listNode* tail = new listNode();
public:
    LRUCache(int _capacity): capacity(_capacity),size(0){
        
        head->next = tail;
        tail->pre = head;
    }
    
    int get(int key) {
        if(record.find(key) != record.end())
        {
            listNode* node = record[key];
            removeTohead(node);
            return node->value;
        }
        return -1;
    }
    
    void put(int key, int value) {
        if(record.find(key) != record.end())
        {
            listNode* node = record[key];
            removeTohead(node);
            node->value = value;
        }else{
            listNode* node = new listNode(key,value);
            record[key] = node;
            addTohead(node);
            size ++;
            if(size > capacity)
            {
                listNode* node = tail->pre;
                record.erase(node->key);
                delete_node(node);
                //delete(temp);
                size --;
            }
        }
    }
    void addTohead(listNode* node)
    {
        head->next->pre = node;
        node->next = head->next;
        node->pre = head;
        head->next = node;
    }
    void removeTohead(listNode* node)
    {
        node->next->pre = node->pre;
        node->pre->next = node->next;
        addTohead(node);
    }
    void delete_node(listNode* node)
    {
        tail->pre->pre->next = tail;
        tail->pre = tail->pre->pre;
        delete node;
    }
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```

