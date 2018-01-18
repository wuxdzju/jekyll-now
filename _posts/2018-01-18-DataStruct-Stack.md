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
		if (pushV.empty() || popV.empty())
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
			S.pop();//
		}
		if (S.empty() && iPop == popV.size())
			res = true;
		return res;
    }
};
```


##  参考




