---
layout: post
title: 数据结构之链表中常犯的一些一些错误
---

### 1、剑指offer中的一道简单的题
### 题目描述
输入一个链表，从尾到头打印链表每个节点的值。

### 分析
这道题很简单，首先如果可以改变原来链表的数据的话，可以先进行链表翻转，然后打印。
如果链表结构不能改变的话，可以借助栈进行处理。
代码如下：
```c++
class Solution {
public:
    vector<int> printListFromTailToHead(ListNode* head) {              
        stack<int> stack;
        ListNode* p=head;
        while(p){//当p指针不为空时，应该是while(p)而不是while（!p）啊啊啊啊啊
            stack.push(p->val);
            p=p->next;//每次进行链表操作时，总是忘记这一句，已经很多次了
        } 
        vector<int> res;  
        while(!stack.empty()){
            res.push_back(stack.top());
            stack.pop();
        }
        return res;
    }
};
```

**注意** 常犯的错误有二：
**1** 在进行链表遍历时，经常忘记更新p，即忘记p=p->next这句。。。。
**2** 判断指针是否为空时，常搞混了，例如while(p)才代表p指针不指向空，而不是while（!p)啊啊啊啊。







##  参考



