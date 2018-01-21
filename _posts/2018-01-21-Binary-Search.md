---
layout: post
title: 基础算法之二分查找
---

## 1、二分查找总结
### 1.版本A——三分支，查找失败时返回-1
**注：** 以下都采用左闭右开区间

![binarySearch1]({{ site.baseurl }}/images/binarySearch.png)

以任一元素S[mi]=x为界，都可以将该区间分为三个部分，且根据此时向量的有序性必有：
S[lo,mi)<=S[mi]<=S[mi+1,hi）
于是，只需将目标元素e与x做一比较，即可根据比较结果分为以下三种情况进行进一步处理：
1 ) 若 e <x， 则目标元素如果存在 ， 必属于左侧于区间S[lo,mi)，故可深入其中继续查找； 2) 若 x < e ， 则目标元素如果存在 ，必属于右侧于区间 S[mi+1,hi） ，故也可深入其间继续在找 3 ) 若 e== x ， 则意味着已经命中，故查找随即终止。
也就是说，每经过至多两次的比较，我们或者已经找到目标元素，或者可以将查找问题简化为一个规模更小的新问题。以下介绍的查找算法都可以归于这一模式，不同点仅在于其对于切分点mi的选取策略，以及每次深入 递归之前所做的比较次数。

#### 实现

```c++
/*
*查找区间为[lo,hi)
*每次迭代可能要做两次比较，有三个分支
*成功查找可以提前终止
*在有多个命中元素命中的时候，不能保证返回最靠后者
*在查找失败时，简单的返回-1，而不能指示查找失败的位置
*/
int binarySearch(vector<int> vecs, int value){//版本A，三分支,查找成功时返回下标，失败时返回-1,
		int lo = 0, hi = vecs.size();
		while (lo < hi){
			int mi = (lo + hi) >> 1;//右移运算比除法运算速度要快
			if (value < vecs[mi])
				hi = mi;
			else if (vecs[mi] < value)
				lo = mi + 1;
			else
				return mi;
		}
		return -1;
	}
```

#### 复杂度
O(log(n))

#### 查找长度
在上述的二分查找算法中，迭代过程涉及的计算主要包括元素大小的比对，秩的运算及赋值，相对复杂度的常系数而言，元素的比较操作的权重往往更大，复杂对象的比较操作往往非常耗时。因此，对查找算法性能更为精细的评估，应更多地关注算法过程中关键码的比较次数，即查找长度（search length）。通常需分别针对成功和失败查找。

在上述的三分支查找算法中，在每次迭代中，为确定左右分支方向而需要做得比较次数分别是1、2.由此造成了不同情况所对应的查找长度的不均性。尽管该版本从表面上看完全均衡，但分析可知最长和最短分支所对应的查找长度相差两倍，这也从一个侧面印证了该算法实际的不均衡性。

具体改进的方法：
1.Fibonacci查找：mi不选择中点，而是选择中点偏后的Fibonacci项。

2.将三分支改成两分支，

### 2.版本B——两分支，查找失败时返回-1
**注：** 以下都采用左闭右开区间

无论朝哪个方向深入 ， 都只需做 1 次元茸的大小比较。相应地 ， ;):法
在每次选代中(或递归层次上)币ífl只有两个分立方向iJ ，而不再是三个 。

![binarySearch2]({{ site.baseurl }}/images/binarySearch2.png)

具体过程类似于版本A，在每个切分点mi处，仅做一次比较。若e<A[mi]，则深入A[lo,mi)继续查找，否则，深入A[mi,hi)继续查找。

#### 实现


```c++
/*
*查找区间为[lo,hi)
*每次迭代仅需做一次比较，有两个分支
*成功查找不会提前终止
*在有多个命中元素时，不会返回最靠后的
*查找失败时，简单的返回-1，不能指示失败的位置
*因为终止条件是区间中只有一个元素，所以当输入为空时，需要单独判断
*/
int binarySearch2(vector<int> vecs, int value){//两分支，
		if (vecs.empty())
			return -1;
		int lo = 0, hi = vecs.size();
		while (1 < hi - lo){
			int mi = (hi + lo) >> 1;
			if (value < vecs[mi])
				hi = mi;
			else
				lo = mi;			
		}
		return (value == vecs[lo] ? lo : -1);
	}
```
### 3.版本C——两分支，能返回查找失败的位置
**注：** 以下都采用左闭右开区间
在版本B的基础上略做修改，可以得到版本C

```c++
/*
*查找区间为[lo,hi)
*每次迭代仅需一次比较，有两个分支
*成功查找不能提前终止
*在有多个命中元素时，总能保证返回最靠后者
*在查找失败时，能够返回失败的位置
*/

int binarySearch3(vector<int> vecs, int value){
		int lo = 0, hi = vecs.size();
		while (lo < hi){
			int mi = (hi + lo) >> 1;
			if (value < vecs[mi])
				hi = mi;
			else
				lo = mi + 1;
		}
		return --lo;
	}
```

#### 正确性
从形式上看，版本C与版本B的差异主要有三点。首先，只有当向量额区间宽度缩小到0（而不是1）时，迭代算法才终止。另外，在每次转入后端分支时，子向量的左边界取做mi+1,（而不是mi）。从表面上看，后一调整可能存在错误的风险——此时只能确定切分点A[mi]<=e，而贸然将A[mi]排除在进一步的查找范围之外，似乎会因遗漏目标元素而导致本应该成功的查找以失败告终。然而这种担心大可不必。
以下验证其正确性。
为确定版本c的正确性，不妨考察该算法中的循环体中的如下不变性：如图所示，A[0,lo)中的元素皆不大于e，A[hi,n)中的元素皆大于e。首次进入该循环时，lo=0且hi=n，A[0,lo),A[hi,n)均为空，不变性自然成立。以下数学归纳：如图a所示，假设某次进入该循环时，以上的不变性成立。根据算法的流程无非两种情况。若e<A[mi],如图b所示，在令hi=mi并使A[hi,n)向左扩展之后，该区间内的元素皆不小于A[mi],当然也仍然大于e。反之，若A[mi]<=e,如图c所示，在令lo=mi+1并使A[0,lo)向右扩展之后，该区间内的元素皆不大于A[mi]，当然仍然不大于e。总之无论如何，每次循环之后上述不变性仍然得以延续。

![binarySearch3]({{ site.baseurl }}/images/binarySearch2.png)

循环终止时，lo=hi。考察此时的元素A[lo-1]和A[lo]:最为A[0,lo)内的最后一个元素，A[lo-1]必定不大于e；作为A[lo,n)内的第一个元素，A[lo]必定大于e，也就是说A[lo-1]即是原向量中不大于e的最后一个元素。因此，在循环结束后，无论成功与否，只需要返回lo-1即可。


### 一些值得注意的问题
#### 左右边界的更新以及避免陷入死循环
1、在版本A中更新左右边界的方法是hi=mi或lo=mi+1（因为是左开右闭区间，所以不管是更新左边界还是更新右边界都去掉了中间点mi)，所以每次循环后问题的规模至少减一，最后肯定能够达到lo==hi这一终止条件。
反之，如果更新左右边界时，将mi保留的话，则可能陷入死循环：例如在{1}中搜索。初次搜索时，mi为0，保留mi到hi，由于是[mi,hi)所以区间并没有改变，故会陷入死循环。

2.在版本B中更新左右边界的方法是hi=mi或者lo=mi。在更新右边界时去掉了mi，但是在更新左边界时却保留了mi，如果循环终止的条件是lo<hi的话，就可能会陷入死循环，例如上面再{1}中搜索2的例子。故在版本B中的循环结束条件是1<hi-lo，即当搜索区间只有一个元素时，就会终止循环。这样会避免死循环吗？答案是肯定的。分析如下：当元素大于等于两个时，每次循环的mi不会与左边界重合，故每次循环一定会使规模减少，只有当hi-lo==1时，即只有一个元素时（左闭右开区间），这时的mi等于lo，可能导致死循环，但是这时却已经不满足我们的循环条件了，循环终止，故不会陷入死循环。

3.版本C与版本A更新左右边界的方法一样。故同样不会陷入死循环。

## LeetCode相关题目
### LeetCode——35. Search Insert Position
#### 题目描述
Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You may assume no duplicates in the array.

Example 1:

```
Input: [1,3,5,6], 5
Output: 2
```
Example 2:
```
Input: [1,3,5,6], 2
Output: 1
```

Example 3:
```
Input: [1,3,5,6], 7
Output: 4
```

Example 4:
```
Input: [1,3,5,6], 0
Output: 0
```

#### 分析
解法一：
这道题与版本C很像，但是不同的是，当查找失败时返回的位置不同，版本C返回的是不大于该元素的最后一个元素的位置，而题目要求是返回该元素的插入位置，即不大于该元素的最后一个位置的后面一个位置。故我们可以在最后返回时，判断查找是否成功来选择不同的返回结果，代码如下：

```c++
    int searchInsert(vector<int>& nums, int target) {
       
        int lo=0,hi=nums.size();
        while(lo<hi){
            int mi=(lo+hi)>>1;
            if(target<nums[mi])
                hi=mi;
            else
                lo=mi+1;
        }
        --lo;
        return (0<=lo && lo<nums.size() && nums[lo]==target)?lo:lo+1;
    }
```

解法二：
其实该题也可采用第一种方法，当查找成功时，返回当前下标mi，当查找失败时必定有lo==hi，并且根据循环不显示，[0,lo)中的元素必定小于target，[lo,n)中的元素必定大于target，故lo即为要target要插入的位置。

```c++
    int searchInsert(vector<int>& nums, int target) {
        int lo = 0, hi = nums.size();
		while (lo < hi){
			int mi = (lo + hi) >> 1;//右移运算比除法运算速度要快
			if (target < nums[mi])
				hi = mi;
			else if (nums[mi] < target)
				lo = mi + 1;
			else
				return mi;
		}
		return lo;
    }
```

**注：若是要查找含有重复元素的最后一个元素的话，只能用版本C。**

### 剑指offer——旋转数组的最小数字
#### 题目描述
把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

#### 分析
我们注意到旋转之后的数组可以划分为两个排序的子数组，而且前面数组的元素都大于或者等于后面子数组的元素。我们还注意到，我们要找的最小的元素恰好是是这两个数组的分界线。故可以尝试采用二分查找的思路来找到这个最小的元素。
思路：我们用两个指针分别指向数组的第一个元素和最后一个元素，按照题目要求，第一个数组元素应该要大于或者等于最后一个元素（这其实并完全对，还有特例，后面再加以讨论）。接着我们可以找到数组的中间元素，如果中间元素大于或等于第一个指针指向的元素，说明他位于全面的子数组，我们要找的最小值应该在后半部分，故将第一个指针指向中间元素。同理，若是中间元素小于或者等于第二个指针指向的元素，说明中间元素位于后面的子数组中，故将第二个指针指向中间元素。
按照上述的思路，第一个指针总是指向前面的递增数组中的元素，第二个指针总是指向后面的递增数组中的元素。最终，第一个指针会指向前面子数组的最后一个元素，第二个指针会指向后面数组的第一个元素。也就是她们终会指向相邻的元素，此时，第二个指针指向的元素即是我们要找的最小的元素。这就是循环终止的条件。
但是按照定义，还有一个特例：即将排序数组的前面的0个元素搬到后面，即排序数组本身，这仍然是数组的一个旋转。需要考虑到这种情况。
另外，当index1，index2，indexMid三个指针指向的元素值相同时，例如数组{1,0,1,1,1,1}和数组{1,1,1,0,1}这样的情况，在这两组数组中，第一个数字、最后一个数字和中间数字都是1，我们无法确定中间的数字1时属于前面的子数组还是属于后面的子数组，在这种情况下，只能进行顺序查找了，，，，

```c++
class Solution {
public:
    int minNumberInRotateArray(vector<int> rotateArray) {
       if(rotateArray.empty())//数组为空的情况
           return 0;
        int index1=0,index2=rotateArray.size()-1;
        int indexMid=index1;//初始时将indexMid指向第一个元素，是考虑到数组旋转了0个元素的情况
        while(rotateArray[index1]>=rotateArray[index2]){//在这里数组没有经过旋转的情况可以排除，但仍然包含了没有经过旋转，但是前后元素相等的情况
            if(1==index2-index1){//循环终止
                indexMid=index2;
                break;
            }
            indexMid=(index1+index2)>>1;
            if(rotateArray[index1]==rotateArray[index2] && rotateArray[index1]==rotateArray[indexMid])//如果三个指针指向的元素值相同时只能进行顺序查找，这个判断条件也包含了只有一个数组的情况
                return minInorder(rotateArray,index1,index2);
            if(rotateArray[indexMid]>=rotateArray[index1])
                index1=indexMid;
            else if(rotateArray[indexMid]<=rotateArray[index2])
                index2=indexMid;
        }
        return rotateArray[indexMid];
    }
    
    int minInorder(vector<int> &rotateArray,int index1,int index2){
        int res=rotateArray[index1];
        for(int i=index1+1;i<=index2;i++){
            if(res>rotateArray[i]){
                res=rotateArray[i];
                break;
            }
        }
        return res;
    }
};     
```

### LeetCode——33. Search in Rotated Sorted Array
#### 题目描述
Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., 0 1 2 4 5 6 7 might become 4 5 6 7 0 1 2).

You are given a target value to search. If found in the array return its index, otherwise return -1.

You may assume no duplicate exists in the array.

#### 分析
一般情况下，我们采用二分法，直接判断中点和目标的关系，就可知道目标是在左半部分还是在右半部分，这背后其实隐含了这是一段有序序列这一假设。
而本题中，这个假设不存在了，因为起点到终点之间有可能有个断片。不过旋转有序数组有个特点，假设本身是个升序序列，从左向向右。如果左边的点比右边的点小，则说明这两个点之间是有序的，不存在旋转点。如果左边的点比右边的点大，说明这两个点之间存在一个旋转点，导致不再有序。
因为只有一个旋转点，所以一分为二之后，肯定有一半是有序的。所以我们还是可以用二分法，不过要先判断左边有序还是右边有序。
如果左边有序，则直接将目标和左半边的边界进行比较，就知道目标点在不在左半边，若不在左半边，则转向右半边。对于右半边有序的处理类似。

代码如下：

```c++
class Solution {
public:
	int search(vector<int>& nums, int target) {
		if (nums.empty())
			return -1;
		int left = 0, right = nums.size() - 1;
		while (left <= right){
			int mid = (left + right) >> 1;
			if (nums[mid] == target)//命中目标，则返回
				return mid;
			if (nums[left] <= nums[mid]){//数组在左半部分有序
				if (nums[left] <= target && target < nums[mid])
					right = mid - 1;
				else
					left = mid + 1;
			}
			else{//数组在右半部分有序
				if (nums[mid] < target && target <= nums[right])
					left = mid + 1;
				else
					right = mid - 1;
			}
		}
		return -1;
	}
};
```

### LeetCode——81. Search in Rotated Sorted Array II
#### 题目描述
Follow up for "Search in Rotated Sorted Array":
What if duplicates are allowed?

Would this affect the run-time complexity? How and why?

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., 0 1 2 4 5 6 7 might become 4 5 6 7 0 1 2).

Write a function to determine if a given target is in the array.

The array may contain duplicates.

#### 分析
这道题与上一道题类似，不同之处在于改题允许元素重复，造成的主要问题是可能无法通过中间元素与两端元素值的比较去判断该段区间是否是递增有序区间。例如序列{1,1,0,1,1,1,1,1,1,1}。
解决方法是当left，right，mid三者的值相同时进行特殊处理。想到的方法有两种，一是当出现三者值相同的时候，就进行顺序查找，这种方法比较笨，但也通过了oj；二是每当发现三者的值相同时，则去掉头尾元素，**再进行下一轮循环**，即left++，right--。
代码如下；

```c++
bool search2(vector<int>& nums, int target) {
		if (nums.empty())
			return false;
		int left = 0, right = nums.size() - 1;
		while (left <= right){
			int mid = (left + right) >> 1;
			if (target == nums[mid])
				return true;
			if (nums[left] == nums[right] && nums[left] == nums[mid]){
				left++;
				right--;
			}
			else if (nums[left] <= nums[mid]){//左边有序，注意这里用的是else if
				if (nums[left] <= target && target<nums[mid])
					right = mid - 1;
				else
					left = mid + 1;
			}
			else{//右边有序
				if (nums[mid]<target && target <= nums[right])
					left = mid + 1;
				else
					right = mid - 1;
			}

		}
		return false;
	}
```

##  参考
[[Leetcode] Search in Rotated Sorted Array 搜索旋转有序数组](https://segmentfault.com/a/1190000003811864)





