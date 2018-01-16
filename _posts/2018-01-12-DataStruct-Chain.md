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
#### Reverse Linked List
**题目描述** 

输入一个链表，反转链表后，输出链表的所有元素。

**分析**

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

#### Reverse Linked ListII
**题目描述** 
Reverse a linked list from position m to n. Do it in-place and in one-pass.

For example:
Given 1->2->3->4->5->NULL, m = 2 and n = 4,

return 1->4->3->2->5->NULL.

Note:
Given m, n satisfy the following condition:
1 ≤ m ≤ n ≤ length of list.

**分析**

这道题是对上一道题的扩展，选择链表中的某段进行翻转。这道题的要求是原地进行，并且只通过一次遍历完成。思路是，先找到第一个需要翻转的节点的前一个节点即第m-1个节点，记录下该节点，然后从该节点的后面一个节点开始，开始进行链表翻转，直到第n个节点，注意要记录下第n+1个节点的位置。然后将翻转后的链表连接到第m-1个节点后，第n+1个节点前。（**这里容易忽略的情况是当第一个节点是头结点的情况。对于链表问题，根据以往的经验，一般都是要建立一个dummy节点，连上原链表的头结点，这样的话，就算头结点有变动，我们还可以通过dummy->next来获得新链表的头结点。**），

```c++
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        ListNode *dummy=new ListNode(-1);
        dummy->next=head;
        ListNode *ptr=dummy;
        //ptr为第m-1个节点
        for(int i=1;i<m;i++){
            ptr=ptr->next;
        }
        //将第m到第n个节点翻转
        ListNode *pre=nullptr,*cur=ptr->next;
        for(int i=m;i<=n;i++){
            ListNode *succ=cur->next;
            cur->next=pre;
            pre=cur;
            cur=succ;
        }
        //将翻转后的节点连接到原来的链表上
        ptr->next->next=cur;//ptr->next指向的还是原来的第m个节点的位置，即现在翻转后的链表段中最后一个节点的位置。
        ptr->next=pre;
        return dummy->next;
    }
};
```

**小技巧：当操作链表时，如果头结点改变了，可以事先建立一个假想的节点dummy，让dummy->next指向头节点，这样，返回时返回dummy->next即是新的头结点，这样，头结点改变了也不影响程序的正确性。**

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
【转】[https://www.cnblogs.com/dancingrain/p/3405197.html](https://www.cnblogs.com/dancingrain/p/3405197.html)
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
![_config.yml]({{ site.baseurl }}/images/circle_chain2.png)

从上面的分析可知，当fast和slow相遇时，slow还没有走完链表，假设fast已经在环内循环了n（1<=n)圈。假设slow走了s步，fast则走了2s步，又由于fast走过的步数=s+n*r（s+在环上多走的n圈），则有下面的等式：
2*s = s + n  * r ; (1)
 => s = n*r (2)
 如果假设整个链表的长度是L，入口和相遇点的距离是x（如上图所示），起点到入口的距离是a（如上图所示），则有
 a + x = s = n * r; (3)  由（2）推出
a + x = (n - 1) * r + r  = (n - 1) * r + (L - a) (4) 由环的长度 = 链表总长度 - 起点到入口点的距离求出
a = (n - 1) * r + (L -a -x) (5)

**集合式子（5）以及上图我们可以看出，从链表起点head开始到入口点的距离a,与从slow和fast的相遇点（如图）到入口点的距离相等。**
因此，我们可以分别用一个指针（ptr1，ptr2），同时从head与slow和fast的相遇点出发，每一次操作走一步，直到ptr1==ptr2，此时的位置也就是入口点！
###### leetcode相关题目——Linked List CycleII
**题目描述**
Given a linked list, return the node where the cycle begins. If there is no cycle, return null.

Note: Do not modify the linked list.

Follow up:
Can you solve it without using extra space?
**思路**
根据之前的分析，我们可以分别用一个指针（ptr1，ptr2），同时从head与slow和fast的相遇点出发，每一次操作走一步，直到ptr1==ptr2，此时的位置也就是入口点！

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        //先用快慢指针找到slow和fast的相遇点
        ListNode *slow=head,*fast=head;
        while(fast && fast->next){
            slow=slow->next;
            fast=fast->next->next;
            if(slow==fast)
                break;
        }
        //没有环，则返回nullptr
        if(!fast || !fast->next){
            return nullptr;
        }      
        //分别从head和相遇点出发，每次走一步，当二者相遇时，即为所求的节点
        while(head!=slow){
            head=head->next;
            slow=slow->next;
        }   
        return slow;
    }
};
```
#### 3）如果存在环，求环上的节点的个数：
思路1：记录下相遇节点存入临时变量tempPtr，然后让slow(或者fast，都一样)继续向前走slow = slow -> next；一直到slow == tempPtr; 此时经过的步数就是环上节点的个数；

思路2： 从相遇点开始slow和fast继续按照原来的方式向前走slow = slow -> next; fast = fast -> next -> next；直到二者再次项目，此时经过的步数就是环上节点的个数 。


第一种思路很简单，其实就是一次遍历链表的环，从而统计出点的个数，没有什么可以详细解释的了。

对于第二种思路，我们可以这样想，结合上面的分析，fast和slow没一次操作都会使得两者之间的距离较少1。我们可以把两者相遇的时候看做两者之间的距离正好是整个环的

长度r。因此，当再次相遇的时候所经过的步数正好是环上节点的数目。

#### 4）如果存在环，求出链表的长度：
到这里，问题已经简单的多了，因为我们在问题1、2、3中已经做得足够的”准备工作“。
我们可以这样求出整个链表的长度：

链表长度L = 起点到入口点的距离 + 环的长度r;

已经知道了起点和入口点的位置，那么两者之间的距离很好求了吧！环的长度也已经知道了，因此该问题也就迎刃而解了！

#### 5）如果存在环，求出链表的长度：
求出环上距离任意一个节点最远的点（对面节点）
如下图所示，点1和4、点2和5、点3和6分别互为”对面节点“ ，也就是换上最远的点，我们的要求是怎么求出换上任意一个点的最远点。
![_config.yml]({{ site.baseurl }}/images/circle_chain3.png)
对于换上任意的一个点ptr0, 我们要找到它的”对面点“，可以这样思考：同样使用上面的快慢指针的方法，让slow和fast都指向ptr0，每一步都执行与上面相同的操作（slow每次跳一步，fast每次跳两步），

当fast = ptr0或者fast = prt0->next的时候slow所指向的节点就是ptr0的”对面节点“。

为什么是这样呢？我们可以这样分析：
![_config.yml]({{ site.baseurl }}/images/circle_chain4.png)

#### 6）对于问题6（扩展）如何判断两个无环链表是否相交，和7（扩展）如果相交，求出第一个相交的节点
对于问题6（扩展）如何判断两个无环链表是否相交，和7（扩展）如果相交，求出第一个相交的节点，其实就是做一个问题的转化：
假设有连个链表listA和listB，如果两个链表都无环，并且有交点，那么我们可以让其中一个链表（不妨设是listA）的为节点连接到其头部，这样在listB中就一定会出现一个环。
因此我们将问题6和7分别转化成了问题1和2.
看看下图就会明白了：
![_config.yml]({{ site.baseurl }}/images/circle_chain5.png)

### 7、leetcode—— Partition List
#### 题目描述
Given a linked list and a value x, partition it such that all nodes less than x come before nodes greater than or equal to x.

You should preserve the original relative order of the nodes in each of the two partitions.

For example,
Given 1->4->3->2->5->2 and x = 3,
return 1->2->2->4->3->5.

#### 分析
这道题要求我们划分链表，把所有小于给定值得节点都移到前面，大于或等于该值的节点顺序不变，相当于一个局部排序问题。
**解法一：**首先找到第一个大于或者等于给定值的节点，用题目给的例子来说就是先找到4，然后再找小于3的值，每找到一个就将其取出置于4之前即可。
代码如下：
```c++
class Solution {
public:
	ListNode* partition(ListNode* head, int x) {
		ListNode *dummy = new ListNode(-1);
		dummy->next = head;
		ListNode *pre = dummy;
		while (pre->next && pre->next->val < x)//找到不小于x的第一个节点的前一个节点
			pre = pre->next;
		ListNode *cur = pre;
		while (cur->next){
			if (cur->next->val < x){
				ListNode *tmp = cur->next;
				cur->next = tmp->next;
				tmp->next = pre->next;
				pre->next = tmp;
				pre = pre->next;				
			}
			else{
				cur = cur->next;
			}
		}
		return dummy->next;
	}
};
```

**小技巧：当考察要移动的节点时，需要将其前面的节点的next指到其后面的节点上，所以要知道其前面的节点。根据单向链表的特点，不能由后向前查找节点，故在上面的代码中，cur指向的是当前考察节点的前面一个节点（若是cur指向当前考察的节点，则还需另外一个变量记录前面的节点，增加了代码的复杂性）。**

**解法二：**将所有小于给定值的节点取出组成一个新的链表，此时原链表中剩余的节点的值都大于或等于给定值，只要将原链表直接接到新链表后即可，代码如下：
```c++
ListNode* partition2(ListNode* head, int x) {
		ListNode *dummy = new ListNode(-1);
		dummy->next = head;
		ListNode *h2 = new ListNode(-1);
		ListNode *p = h2, *cur = dummy;
		while (cur->next){
			if (cur->next->val < x){
				p->next = cur->next;
				p = p->next;
				cur->next=p->next;				
				p->next = nullptr;
			}
			else{
				cur = cur->next;
			}
		}
		p->next = dummy->next;
		return h2->next;
	}
```

### 8、leetcode—— Rotate List
#### 题目描述
Given a list, rotate the list to the right by k places, where k is non-negative.

Example:
```
Given 1->2->3->4->5->NULL and k = 2,

return 4->5->1->2->3->NULL.
```
#### 思路
**解法一：** 
最初想到的解法是用快慢指针，快指针先走k步，然后慢指针再走，快指针fp->next为null时，慢指针sp->next指向的即为旋转后的链表的第一个节点，然后即可以进行旋转操作。
**注意：**
**值得注意的有以下两点：**
**1.当链表为空的情况。**
**2.当k大于链表的长度时。**
所以首先应当先遍历链表，计算出链表的长度count，然后取k=k%count。然后再按照上述的快慢指针进行旋转。

代码如下：

```c++
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {        
        if(!head || k==0)
            return head;
        ListNode *p=head;
        int count=0;
        while(p){//计算链表的长度
            count++; 
            p=p->next;//这句又忘记了。。。。。
        } 
        k=k%count;//k对count取余
        if(k==0)
            return head;
        ListNode *fp=head,*sp=head;//用快慢指针
        for(int i=0;i<k;i++)
            fp=fp->next;
        while(fp->next){
            sp=sp->next;
            fp=fp->next;
        }
        fp->next=head;
        head=sp->next;
        sp->next=nullptr;
        return head;    
    }
};
```
**解法二：** 
只用一个指针，原理是先遍历链表获得链表的长度count，然后把链表的头尾连接起来，然后再往后走n-k%n个节点就达到新链表的头结点的前一个节点，这时展开链表即可。

代码如下：
```c++
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {    
        if(!head || k==0)
            return head;
        ListNode *p=head;
        int count=1;
        while(p->next){
            count++;
            p=p->next;
        }
        p->next=head;
        k=k%count;
        for(int i=0;i<count-k;i++){
            p=p->next;
        }
        head=p->next;
        p->next=nullptr;
        return head;
    }
};
```


### 8、leetcode—— RRemove Duplicates from Sorted List II（删除重复节点）
#### 题目描述
Given a sorted linked list, delete all duplicates such that each element appear only once.

For example,
Given 1->1->2, return 1->2.
Given 1->1->2->3->3, return 1->2->3.

#### 思路
定义一个指针，指向链表的第一个元素，然后比较第一个元素和第二个元素，若相同，则删除第二个元素，若不相同，则将指针移到第二个元素处。
一直执行上述步骤，直到链表尾部。

代码：
```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
       ListNode *cur = head;
		while (cur){
			while ( cur->next  &&  cur->val==cur->next->val)			{
				cur->next = cur->next->next;
			}
			cur = cur->next;
		}
		return head;
    }
};
```

### 9、leetcode—— RRemove Duplicates from Sorted List II（删除重复节点II）
#### 题目描述
Given a sorted linked list, delete all nodes that have duplicate numbers, leaving only distinct numbers from the original list.

For example,
Given 1->2->3->3->4->4->5, return 1->2->5.
Given 1->1->1->2->3, return 2->3.

#### 思路
我的思路是用一个val值记录前一个节点的值，用pre指针记录已经处理过的链表的最后一个节点，用指针cur指向当前考察的节点。因为链表的头结点可能改变，所以用一个假象的节点dummy，dumm->next指向头结点。
初始时，pre指向dummy，cur指向head，val的类型定义为long long型，并且初始值设为LLONG_MAX（因为考察的值的类型为int型，val的初始值应当不能和int型中的任何值相同，所以将其设置为long long型），然后对当前的cur节点，其思想有点类似删除链表中值为val的节点，不过不同的是val的值是变化的。且只有当cur的值不和前一个节点相同并且不和下一个节点相同时，才将其保留到链表中。
代码如下：
```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {        
        ListNode *dummy=new ListNode(-1);
        dummy->next=head;
        long long  val = LLONG_MAX;
        ListNode *pre=dummy,*cur=head;
        while(cur){
            if(cur->val!=val){
                val=cur->val;//当cur的值不和val相同时，更新val
                if(!cur->next || cur->val!=cur->next->val){//更新pre节点
                    pre->next=cur;
                    pre=cur;
                }
            }
            cur=cur->next;
        }
        pre->next=nullptr;
        return dummy->next;
    }
};
```


##  参考

[[LeetCode] Sort List 链表排序](https://www.cnblogs.com/grandyang/p/4249905.html)
[判断链表中是否有环 ----- 有关单链表中环的问题](https://www.cnblogs.com/dancingrain/p/3405197.html)
[[LeetCode] Partition List 划分链表](https://www.cnblogs.com/grandyang/p/4321292.html)


