---
layout: post
title: leetcode：Third Maximum Number其中一种解法中关于数值越界的一些思考
---

## **题目**
Given a non-empty array of integers, return the third maximum number in this array. If it does not exist, return the maximum number. The time complexity must be in O(n).

Example 1:

    
    Input: [3, 2, 1]
    Output: 1
    Explanation: The third maximum is 1.


Example 2:

    Input: [1, 2]
    Output: 2
    Explanation: The third maximum does not exist, so the maximum (2) is returned instead.


Example 3:

    Input: [2, 2, 3, 1]
    Output: 1
    Explanation: Note that the third maximum here means the third maximum distinct number.
    Both numbers with value 2 are both considered as second maximum.


## **解法：**

**主要思想：**分别定义三个long long型变量记录整个数组中的最大值、第二大值、第三大值。然后依次考察数组中的每个元素，考察方法如以下算法所示。最后若第三大值不为LLONG_MIN的话，返回第三大值，否则，返回最大的值。

    int thirdMax(vector<int>& nums) {
	    long long firstMax, secondMax, thirdMax;
	    firstMax = secondMax = thirdMax = LLONG_MIN;
	    for (int i = 0; i<nums.size(); i++){
	    	if (nums[i] <= thirdMax || nums[i] == secondMax || nums[i] == firstMax)
		    	continue;
	    	thirdMax = nums[i];
		    if (thirdMax>secondMax)
		    	swap(thirdMax, secondMax);
	    	if (secondMax>firstMax)
		    	swap(secondMax, firstMax);
	    }
	    return (thirdMax == LLONG_MIN) ? firstMax : thirdMax;
	}
	


----------
**注意：**
1). LLONG_MIN代表的是long long类型中的最小值，类似地，INT_MIN表示int类型中的最小值。（在vs2013中，int是32位，其值为-2^<sup>31</sup>）,如下所示：

    void test_int_min(){
	    cout << INT_MIN << endl;
	    cout <<fixed<< -pow(2, 31) << endl；//不用科学计数法
	    cout << LLONG_MIN << endl;
	    cout << fixed << -pow(2, 63) << endl;
	}

输出的结果如下：
 ![_config.yml]({{ site.baseurl }}/images/int-min.png)
	

2). 注意在本例中用来做记录的三个值类型采用long long，而不是int，这是考虑到可能会出现类似于以下的情况:如果改为firstMin=secondMin=thirdMin=INT_MIN,对于一个输入为[1,2,INT_MIN]的数组，理论上应该输出INT_MIN才对，但是这时程序会误以为没有第三大的数值，因此会输出firstMax,即输出2.





