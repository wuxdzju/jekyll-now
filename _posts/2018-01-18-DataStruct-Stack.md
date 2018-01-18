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


##  参考




