---
layout: post
title: 数据结构之栈
---

### 1、剑指offer——22.栈的压入、弹出序列
#### 题目描述
输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4，5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

#### 分析
解决这个问题很直观的想法就是建立一个辅助栈，把输入的第一个序列中的数字一次压入该辅助栈，并按照第二个序列的顺序依次从该栈中弹出数字。

```c++
class Solution {
public:
    bool IsPopOrder(vector<int> pushV,vector<int> popV) {
        bool res = false;
		if (pushV.empty() || popV.empty() || pushV.size() != popV.size())//注意当两个向量的size不相同时，应该返回false，否则有可能会导致后面的pop或top越界
			return res;
		stack<int> S;		
		int iPop = 0, iPush = 0;
		for (; iPop < popV.size(); iPop++){
			while (S.empty() || S.top() != popV[iPop]){//当栈为空或者栈顶元素不是当前ipop指向的popV元素时，则向栈中补充元素
				if (iPush == pushV.size())//pushV的下标越界的话，则退出循环
					break;
				S.push(pushV[iPush]);
				iPush++;
			}
			if (S.top() != popV[iPop])//因为前面的while循环中有该条件，所以满足此条件的唯一原因是此时的iPush已经越界，隐含pushV中已经没有可补充的元素，但是此时的栈顶元素却不与输出序列当前考察元素相同
				break;
			S.pop();//因为两个向量的大小相同（前面已经排除掉了两个size不同的情形），由于若s为空的话，前面肯定会执行push操作，所以push的操作肯定大于或等于pop
		}
		if (S.empty() && iPop == popV.size())//最后栈为空，并且pushV中的元素都已经考察完毕
			res = true;
		return res;
    }
};
```

### 2、LeetCode——150. Evaluate Reverse Polish Notation（计算逆波兰表达式）
#### 逆波兰表达式（RPN)
逆波兰表达式是数学表达式的一种，其语法概括为：操作符紧邻于对应的（最后一个）操作数。比如“12+”即为通常习惯的“1+2”.
RPN表达式也称作后缀表达式（postfix），原表达式称作中缀表达式（infix）。RPN表达式中运算符的执行次序与其在RPN表达式中出现的次序完全吻合。
例：“12+34^\*”表示“（1+2）\*3^4"


#### 题目描述
Evaluate the value of an arithmetic expression in Reverse Polish Notation.

Valid operators are +, -, *, /. Each operand may be an integer or another expression.

Some examples:
```
  ["2", "1", "+", "3", "*"] -> ((2 + 1) * 3) -> 9
  ["4", "13", "5", "/", "+"] -> (4 + (13 / 5)) -> 6
```

#### 分析
借助辅助栈，存放操作数。对于每个string，若其为数字，则将其压入栈中，若为操作数，则从栈中取出相应数量的操作数并执行相应的操作，然后将操作结果压入栈中。当扫描完表达式，栈中唯一存储的操作数即为最终结果（假设所给的逆波兰表达式正确）。

```c++
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
		stack<int> S;
		for (vector<string>::iterator it = tokens.begin(); it != tokens.end(); it++){
			string s = *it;
			if (s == "+" || s == "-" || s == "*" || s == "/"){
				int tmp2 = S.top(); S.pop();//注意第二个参数先出栈
				int tmp1 = S.top(); S.pop();				
				switch (s[0])
				{
				case '+':
					S.push(tmp1 + tmp2);
					break;
				case '-':
					S.push(tmp1 - tmp2);
					break;
				case '*':
					S.push(tmp1 * tmp2);
					break;
				case '/':
					S.push(tmp1 / tmp2);
					break;
				default:
					break;
				}
			}
			else{
				S.push(stringToInt(s));
			}
		}
		return S.top();
	}

	int stringToInt(string s){
		int res = 0;
		int isnegative = false;//注意考虑负数的情况。
		for (int i = 0; i < s.size(); i++){
			if (i==0 && s[i] == '-'){
				isnegative = true;
				continue;
			}
			res = res * 10 + static_cast<int>(s[i] - '0');
		}
		return isnegative ? res*(-1) : res;
	}
};
```

**值得注意的几点：**

**1.注意从栈中取操作数时，先弹出来的是第二操作数，后弹出的才是第一操作数。**

**2.从string转换为int时，要注意考虑负数的情况。**

**3.string类型的判等：string s="hello",s=="hello",返回的是true。**

### 3、LeetCode——84. Largest Rectangle in Histogram（求直方图中的最大矩形面积）

#### 题目描述：
Given n non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.

![描述]({{ site.baseurl }}/images/stack_rectangle1.png)

Above is a histogram where width of each bar is 1, given height = [2,1,5,6,2,3].

![描述]({{ site.baseurl }}/images/stack_rectangle2.png)

The largest rectangle is shown in the shaded area, which has area = 10 unit.

For example,
Given heights = [2,1,5,6,2,3],
return 10.



### 分析

[http://blog.csdn.net/yutianzuijin/article/details/52072427](http://blog.csdn.net/yutianzuijin/article/details/52072427)
**解法一**
暴力解法：遍历所有的起始和结束位置，然后计算该区域内的面积，找到最大的。具体实现过程：从第0个位置开始遍历起点，选定起点之后，从起点到末尾都可以当做终点，所以有O(n2)种可能。在固定了起点之后，后续对终点的遍历有一个特点：构成长方形的高度都不高于之前所构造长方形的高度，所以长方形的高度即是到当前终点为止的最小值，宽度即是终点位置减去起点位置加1。

```c++
class Solution {
public:
	int largestRectangleArea1(vector<int>& heights) {//暴力解法
		if (heights.empty())
			return NULL;
		int maxArea = -1;
		for (int i = 0; i < heights.size(); i++){
			int minHeight = heights[i];
			for (int j = i; j < heights.size(); j++){
				if (heights[j] < minHeight)
					minHeight = heights[j];
				int tmpArea = (j - i + 1)*minHeight;
				if (tmpArea > maxArea)
					maxArea = tmpArea;
			}
		}
		return maxArea;
	}
};
```

注：在LeetCode中提示Time Limit Exceeded超时错误。

**解法二**

![描述]({{ site.baseurl }}/images/stack_large_rectangle.png)

基本思路（如上图所示）：从第一个柱子（编号1）开始，找出所有的后一个比前一个上升的柱子（从编号1到编号13），直到遇到一个高度下降的柱子（编号14）。而且把下降之前的编号（从1到13）推入到一个堆栈中，然后计算栈顶编号与下降编号的前一个编号围成的面积（即到13），然后弹出栈顶元素，继续按照之前的方法继续计算栈顶元素的面积，直到栈顶元素的高度不再大于下降元素的高度或者栈为空，并保留最大的矩形面积。从栈顶开始，比如，13，12-13，...，8-13，7-13，...，直到3-13。但是14能参与比它矮的较远的编号2（2-14，就是B1B2）和编号1（1-14，就是C1C2）组成矩形。以看出，堆栈中保留了到当前为止，所有将要参与可能矩形计算的所有柱子的编号。有了这些编号，我们可以计算所有可能的矩形的面积，从而求出最大的。注意了，堆栈中的编号的柱子高度是非递减的！
**值得注意的是：1.当栈为空时，计算矩形面积时，宽是从下标0开始的，因为栈中保存的index的高度是非递减的，所以，若栈为空时，证明之前的高度都要大于当前高度。故以当前高度肯定能和前面的所有高度构成矩形。**

**2.对于循环结束的处理：可以采用两种方法，一种是在原数组最后添一个负数，这样，就会清空前面栈中的所有元素，但是这样会改变原来的数组。另一种方法是，对index=heights.size()做特殊处理。**

**3.注意当输入为空时，面积应当为0**

在原数组后面添-1
```c++
int largestRectangleArea2(vector<int>& heights) {
		heights.push_back(-1);
		int maxArea = 0;
		int index = 0;
		stack<int> S;
		while (index < heights.size()){
			if (S.empty() || heights[S.top()] < heights[index]){
				S.push(index);
				index++;
			}
			else{
				int cur = S.top();
				S.pop();
				int tmpArea = 0;
				if (S.empty()){
					tmpArea = heights[cur] * index;
				}
				else{
					tmpArea = heights[cur] * (index - S.top() - 1);
				}
				if (tmpArea > maxArea)
					maxArea = tmpArea;
			}

		}
		return maxArea;
	}
```

不改变原数组

```c++
int largestRectangleArea3(vector<int>& heights) {		
		int maxArea = 0;
		int index = 0;
		stack<int> S;
		while (index <= heights.size()){
			if (S.empty()  || (index!=heights.size() && heights[S.top()] < heights[index] )){//当heights为空时，满足该条件，然后index++，退出循环，最后的结果还是0，满足要求的
				S.push(index);
				index++;
			}
			else{
				int cur = S.top();
				S.pop();
				int tmpArea = 0;
				if (S.empty()){
					tmpArea = heights[cur] * index;
				}
				else{
					tmpArea = heights[cur] * (index - S.top() - 1);
				}
				if (tmpArea > maxArea)
					maxArea = tmpArea;
			}

		}
		return maxArea;
	}
```

### 4、trapping-rain-water(积水问题)

#### 题目描述：
Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

For example, 
Given[0,1,0,2,1,0,1,3,2,1,2,1], return6.

![描述]({{ site.baseurl }}/images/stack_water.png)

The above elevation map is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped. Thanks Marcos for contributing this image!

### 分析

解法一：
利用栈。我们的做法是，遍历高度，如果此时的栈为空，或者当前的高度小于等于栈顶的高度，则把当前高度的坐标压入栈，注意我们不直接把高度压入栈，而是把坐标压入栈，这样做的方便我们后面计算水平距离。当我们遇到比栈顶高度大的时候，就说明可能会有坑存在，可以装雨水。此时我们的栈里至少有一个高度，如果只有一个的话，那么就不能形成坑，我们直接跳过，如果多于一个的话，那么我们此时把栈顶元素取出来当做坑，新的栈顶元素就是左边界，当前高度是右边界，只要取二者的较小者，减去坑的高度，长度就是右边边界的坐标减去左边边界的坐标再减一，二者相乘就是盛水量。

```c++
class Solution {
public:
	int trap(vector<int>& height) {
		int i = 0, water = 0;
		stack<int> S;
		while (i < height.size()){
			if (S.empty() || height[i] <= height[S.top()]){
				S.push(i);
				i++;
			}
			else{
				int low = S.top();
				S.pop();
				if (S.empty())
					continue;
				int h = (height[i] < height[S.top()]) ? height[i] : height[S.top()];
				water += (h - height[low])*(i - S.top() - 1);
			}
		}
		return water;
	}
};
```

解法二：
双指针。我们使用两个指针，一个从左向右遍历，一个从右向左遍历。从左向右遍历时，记录下上次左边的峰值，如果右边一直没有比这个峰值高的，就已经加上这些差值。从右向左遍历时，记录下上次右边的峰值，如果左边一直没有比这个峰值高的，就加上这些差值。难点在于，当两个指针遍历到相邻的峰时，我们要选取较小的那个峰值来计算差值。所以，我们在遍历左指针或者右指针之前，要先判断左右两个峰值的大小。
注：移动左指针或者右指针时，要先判断left < right

```c++
int trap2(vector<int>& height) {
		if (height.size() < 3)
			return 0;
		int left = 0, right = height.size() - 1;
		int sum = 0;
		//找到左边的第一个峰值
		while (left < right && height[left] <= height[left + 1]) left++;
		//找到右边的第一个峰值
		while (left < right && height[right] <= height[right - 1]) right--;
		while (left < right){
			int leftVal = height[left];
			int rightVal = height[right];
			if (leftVal < rightVal){	//如果左边峰值小，则先计算左边			
				while (left<right && height[++left] <= leftVal){
					sum += leftVal - height[left];
					
				}				
			}
			else{//如果右边峰值小，则先计算右边
				while (left < right && height[--right]<=rightVal){
					sum += rightVal - height[right];					
				}
			}
		}
		return sum;
	}
```


##  参考

[leetcode之Largest Rectangle in Histogram](http://blog.csdn.net/yutianzuijin/article/details/52072427)

[很神奇的解法！怎么求柱状图中的最大矩形？](http://chuansong.me/n/390896436960)




