---
layout: post
title: 数据结构之链表
---

### 1、剑指offer中的一道简单的题——从尾到头打印链表中每个节点的值
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
**1 在进行链表遍历时，经常忘记更新p，即忘记p=p->next这句。。。。**
**2 判断指针是否为空时，常搞混了，例如while(p)才代表p指针不指向空，而不是while（!p)啊啊啊啊。** 

### 2、leetcode——Merge Two Sorted Lists(合并两个已排序的链表)
### 题目描述
Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

Example:

```
Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4
```
### 分析
这与合并两个已排序的数组类似，只是将数组下标换成指针。

```c++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode *dummy=new ListNode(-1);
        ListNode *p=dummy;
        while(l1 || l2){
            if(l1 &&(!l2 || l1->val<=l2->val)){//注意两个条件判断中一定要有一个包含相等的情况
                p->next=l1;
                p=p->next;
                l1=l1->next;
            }
            if(l2 &&(!l1 || l2->val<l1->val)){
                p->next=l2;
                p=p->next;
                l2=l2->next;
            }            
        }
        return dummy->next;
    }
};
```
**注意** 常犯的错误：
**两个条件判断中一定要有一个包含相等的情况，否则会陷入死循环。**


### 3、leetcode——Sort List(链表排序)
### 题目描述
Sort a linked list in O(n log n) time using constant space complexity.

### 分析
常见排序方法有很多，插入排序，选择排序，堆排序，快速排序，冒泡排序，归并排序，桶排序等等。。它们的时间复杂度不尽相同，而这里题目限定了时间必须为O(nlgn)，符合要求只有快速排序，归并排序，堆排序，而根据单链表的特点，最适于用归并排序。

```c++
class Solution {
public:
	ListNode* sortList(ListNode* head) {
		if (!head || !head->next)
			return head;
		ListNode *slow = head, *fast = head, *pre = head;
		while (fast && fast->next){//用了快慢两个指针，慢指针每次跳一格，快指针每次跳两格。
			pre = slow;
			slow = slow->next;
			fast = fast->next->next;
		}
		pre->next = nullptr;
		return mergeTwoLists(sortList(head), sortList(slow));
	}

	ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
		ListNode *dummy = new ListNode(-1);
		ListNode *p = dummy;
		while (l1 || l2){
			if (l1 && (!l2 || l1->val <= l2->val)){//注意两个条件判断中一定要有一个包含相等的情况
				p->next = l1;
				p = p->next;
				l1 = l1->next;
			}
			if (l2 && (!l1 || l2->val<l1->val)){
				p->next = l2;
				p = p->next;
				l2 = l2->next;
			}
		}
		return dummy->next;
	}
};
```
**注意** 

**1.这里比较有意思的是用到了快慢两个指针，当快指针走到终点时，慢指针才走到中点。**
**注意这个终止条件：while (fast && fast->next)。**

### 4、剑指offer——反转链表
### 题目描述
输入一个链表，反转链表后，输出链表的所有元素。

### 分析
![_config.yml]({{ site.baseurl }}/images/reverse_list.png)
在上图所示的链表中，h、i、j是3个相邻的节点。假设进过若干操作，我们已经把节点h之前的指针全部调整完毕，这些节点的next都指向前一个节点。接下来我们把i的next指向h，此时链表结构如第二个图所示。由于i的next指向了它的前一个节，导致我们无法在链表中遍历到节点j，因此，为了避免链表在节点i处断开，我们需要在调整节点i的next指针之前将j保存下来。另外，由于我们需要将i的next指针指向它的前面一个节点，因此我们需要事先记录前面的节点h。
因此，我们需要相应定义3个指针，分别指向当前遍历的节点，它的前一个节点，它的后一个节点。

```c++
    ListNode* reverse(ListNode* head){		
		if (!head && !head->next)
			return head;
		ListNode *pre = nullptr, *cur = head;
		while (cur){
			ListNode *succ = cur->next;
			cur->next = pre;
			pre = cur;
			cur = succ;
		}
		return pre;
	}
```
**注意** 
**注意代码的鲁棒性。在这里要考虑当链表为空和链表只包含一个节点的情况。**


### 5、leetcode——Reorder List
### 题目描述
Given a singly linked list L: L0→L1→…→Ln-1→Ln,
reorder it to: L0→Ln→L1→Ln-1→L2→Ln-2→…

You must do this in-place without altering the nodes' values.

For example,
Given {1,2,3,4}, reorder it to {1,4,2,3}.

### 分析
这道链表重排序问题可以拆分为以下三个部分：
1. 首先利用快慢指针找到链表的中点，并从中点将链表拆分为前后两个部分。
2. 将后部分链表翻转。
3. 将翻转后的链表插入到第一个链表中。

代码如下：
```c++
void reorderList(ListNode* head) {
		if (!head || !head->next)//当链表为空或者链表只包含一个节点
			return;
		ListNode *slow = head, *fast = head,*pre=nullptr;
		while (fast && fast->next){
			pre = slow;
			slow = slow->next;
			fast = fast->next->next;
		}
		pre->next = nullptr;
		slow=reverse(slow);
		merge(head, slow);
	}

	ListNode* reverse(ListNode* head){		
		if (!head && !head->next)
			return head;
		ListNode *pre = nullptr, *cur = head;
		while (cur){
			ListNode *succ = cur->next;
			cur->next = pre;
			pre = cur;
			cur = succ;
		}
		return pre;
	}

	void merge(ListNode* l1, ListNode* l2){//根据上面的划分，l2的节点数应该等于l1或者比l1多一个
		ListNode *cur1 = l1, *cur2 = l2, *pre=nullptr;
		while (cur1){
			ListNode *succ = cur1->next;
			cur1->next = pre = cur2;
			cur2 = cur2->next;
			pre->next = succ;
			cur1 = succ;
		}	
		pre->next = cur2;
	}
```
### 6、有关单链表中环的问题
#### 相关知识
给定一个单链表，判断其中是否有环，已经是一个比较老同时也是比较经典的问题。

首先，关于单链表中的环，一般涉及到以下问题：
1. 给一个单链表，判断其中是否有环的存在；
2. 如果存在环，找出环的入口；
3. 如果存在环，求出环上的节点数；
4. 如果存在环，求出链表的长度；
5. 如果存在环，求出环上距离任意一个节点最远的点（对面节点）；
6. 如何判断两个无环链表是否相交；
7. 如果相交，求出第一个相交的节点；

#### 1）判断是否有环（链表头指针为head）
对于这个问题我们可以采用“快慢指针”的方法。就是有两个指针fast和slow，开始的时候两个指针都指向链表头head，然后在每一步

操作中slow向前走一步即：slow = slow->next，而fast每一步向前两步即：fast = fast->next->next。

由于fast要比slow移动的快，如果有环，fast一定会先进入环，而slow后进入环。当两个指针都进入环之后，经过一定步的操作之后

二者一定能够在环上相遇，并且此时slow还没有绕环一圈，也就是说一定是在slow走完第一圈之前相遇。证明可以看下图：
![_config.yml]({{ site.baseurl }}/images/circle_chain1.png)
当slow刚进入环时每个指针可能处于上面的情况，接下来slow和fast分别向前走。slow每次向前走一步，fast向前追了两步，因此**每一步操作后fast到slow的距离缩短了1步**，这样继续下去就会使得
两者之间的距离逐渐缩小：...、5、4、3、2、1、0 -> 相遇。又因为在同一个环中fast和slow之间的距离不会大于环的长度，因此

到二者相遇的时候slow一定还没有走完一周（或者正好走完以后，这种情况出现在开始的时候fast和slow都在环的入口处）。

###### leetcode相关题目——Linked List Cycle
**题目描述**
Given a linked list, determine if it has a cycle in it.

Follow up:
Can you solve it without using extra space?

**思路**
用两个快慢指针，快指针每次走2步，慢指针每次走1步，如果有环的话，快慢指针最终定会相遇。

```c++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode *slow=head,*fast=head;
        while(fast && fast->next){
            slow=slow->next;
            fast=fast->next->next;
            if(slow==fast)
                return true;
        }
        return false;
    }
};
```
#### 2）找出环的入口

##  参考

[[LeetCode] Sort List 链表排序](https://www.cnblogs.com/grandyang/p/4249905.html)
[判断链表中是否有环 ----- 有关单链表中环的问题](https://www.cnblogs.com/dancingrain/p/3405197.html)[https://www.cnblogs.com/dancingrain/p/3405197.html](https://www.cnblogs.com/dancingrain/p/3405197.html)

