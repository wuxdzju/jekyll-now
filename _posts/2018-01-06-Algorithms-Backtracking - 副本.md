---
layout: post
title: 算法学习——回溯法（backtracking)
---

[TOC]


##  1、概述——什么是回溯
要求解一个问题，如果没有思路，一种可靠的方法是**暴力穷举（brute force）**：列出所有的候选解，然后逐个检查，在检查所有或部分候选解之后，便可找到所需要的解。不过在实际中，这种方法很少用，因为候选解的数量通常很大（指数级甚至是阶乘）。
与暴力穷举有点类似，回溯法是对候选解进行系统检查的一种方法。

用爬山来比喻回溯，好比从山脚下找一条爬上山顶的路,起初有好几条道可走,当选择一条道走到某处时,又有几条岔道可供选择,只能选择其中一条道往前走,若能这样子顺利爬上山顶则罢了,否则走到一条绝路上时,只好返回到最近的一个路口,重新选择另一条没走过的道往前走。如果该路口的所有路都走不通,只得从该路口继续回返。照此规则走下去,要么找到一条到达山顶的路,要么最终试过所有可能的道,无法到达山顶。
回溯是一种穷举，但与brute force有一些区别，回溯带了两点脑子的，并不多，brute force一点也没带。
第一点脑子是回溯知道回头；相反如果是brute force,发现走不通立刻跳下山摔死，换第二条命从头换一条路走。
第二点脑子是回溯知道剪枝；如果有一条岔路上放了一坨屎，那这条路我们不走，就可以少走很多不必要走的路。

还有一些爱混淆的概念：递归，回溯，DFS。
回溯是一种找路方法，搜索的时候走不通就回头换路接着走，直到走通了或者发现此山根本不通。
DFS是一种开路策略，就是一条道先走到头，再往回走一步换一条路走到头，这也是回溯用到的策略。在树和图上回溯时人们叫它DFS。
递归是一种行为，回溯和递归如出一辙，都是一言不合就回到来时的路，所以一般回溯用递归实现；当然也可以不用，用栈。
以下以回溯统称，因为这个词听上去很文雅。

##  2、算法思想
回溯法（backtracking）是搜索问题的一种系统方法。
1）回溯法求解首先需要定义问题的一个解空间（solution space）。这个空间至少包含问题的一个（最优的）解。
2）回溯法求解的下一步是组织解空间，使得解空间便于搜索。典型的组织方法是图或树。
3) 一旦确定了解空间的组织方法，这个空间即可按深度优先搜索方式（DFS）从开始节点进行搜索。开始节点既是一个**活动节点（live node）**，又是一个**扩展节点（expansion node）**。从扩展节点我们试着找到一个新节点。如果从当前扩展节点移到一个新节点，那么这个新节点就变成一个活动节点和新的扩展节点。而原来的扩展节点仍然是一个活动节点。如果不能移到一个新的节点，那么当前的扩展节“死掉”，即不再是活动节点。然后我们回到最近的活动节点。这个活动节点变成新的扩展节点。当我们已经找到答案或者是不再有活动节点时，搜索过程结束。

回溯法的步骤如下：
1） 定义一个解空间，它包含对问题实例的解。
2） 用适合于搜索的方式组织解空间。
3） 用深度优先方式搜索解空间，利用界定函数避免进入无解的子空间。

回溯法的实现有一个有意义的特性：在进行搜索的同时产生解空间。在搜索过程的任何时刻，仅保留从开始节点到当前扩展节点的路径。**因此，回溯法的空间需求为O(从开始节点起到最长路径的长度）。** 这个特性很重要，因为解空间的大小通常是最长路长度的指数或阶乘。所以，要存储全部解空间，那么再对的空间也不够用。


##  3、怎么使用回溯
判断回溯很简单，拿到一个问题，你感觉如果不穷举一下就没法知道答案，那就可以开始回溯了。
一般回溯的问题有三种：
```
1） Find a path to success 有没有解

2） Find all paths to success 求所有解

        + 求所有解的个数
        
        + 求所有解的具体信息

3） Find the best path to success 求最优解
```

理解回溯：给一堆选择, 必须从里面选一个. 选完之后我又有了新的一组选择. This procedure is repeated over and over until you reach a final state. If you made a good sequence of choices, your final state is a goal state; if you didn't, it isn't.

回溯可以抽象为一棵树，我们的目标可以是找这个树有没有good leaf，也可以是问有多少个good leaf，也可以是找这些good leaf都在哪，也可以问哪个good leaf最好，分别对应上面所说回溯的问题分类。
good leaf都在leaf上。good leaf是我们的goal state，leaf node是final state，是解空间的边界。

对于第一类问题(问有没有解)，基本都是长着个样子的，理解了它，其他类别迎刃而解：

```
boolean solve(Node n) {
    if n is a leaf node {
        if the leaf is a goal node, return true
        else return false
    } else {
        for each child c of n {
            if solve(c) succeeds, return true
        }
        return false
    }
}
```

请读以下这段话以加深理解：
Notice that the algorithm is expressed as a boolean function. This is essential to understanding the algorithm. If solve(n) is true, that means node n is part of a solution--that is, node n is one of the nodes on a path from the root to some goal node. We say that n is solvable. If solve(n) is false, then there is no path that includes n to any goal node.

不懂的话请参考：[Backtracking - David Matuszek](https://www.cis.upenn.edu/~matuszek/cit594-2012/Pages/backtracking.html)

关于回溯的三种问题，模板略有不同，
第一种，返回值是true/false。
第二种，求个数，设全局counter，返回值是void；求所有解信息，设result，返回值void。
第三种，设个全局变量best，返回值是void。

第一种
```
boolean solve(Node n) {
    if n is a leaf node {
        if the leaf is a goal node, return true
        else return false
    } else {
        for each child c of n {
            if solve(c) succeeds, return true
        }
        return false
    }
}
```

第二种
```
void solve(Node n) {
    if n is a leaf node {
        if the leaf is a goal node, count++, return;
        else return
    } else {
        for each child c of n {
            solve(c)
        }
    }
}
```

第三种
```
void solve(Node n) {
    if n is a leaf node {
        if the leaf is a goal node, update best result, return;
        else return
    } else {
        for each child c of n {
            solve(c)
        }
    }
}
```



##  题目

### 八皇后 N-Queens

#### **问题描述**

![_config.yml]({{ site.baseurl }}/images/queens.png)

如图所示，国际象棋中皇后的势力范围覆盖其所在的水平线、垂直线以及两条对角线。现考察如下问题：在nXn的棋盘上放置n个皇后，如何使得她们彼此互不攻击——此时称她们构成一个可行棋局。对于任何整数n>=4，这就是n皇后问题。
由鸽巢原理可知，在n行n列的棋盘上至多只能放置n个皇后。反之，n个皇后在nXn的棋盘上的可行棋局通常也存在。

#### **问题**
1.给个n，问有没有解；
2.给个n，有几种解；(Leetcode N-Queens II)
3.给个n，给出所有解；(Leetcode N-Queens I)

#### **解答**
**1. 有没有解**

**怎么做：**  一行一行地放queen，每行尝试n个可能，有一个可达，返回true；都不可达，返回false。

**边界条件leaf：** 放完第n行 或者 该放第n+1行（出界，返回）

**目标条件goal：** n行放满且isValid，即目标一定在leaf上。

**helper函数：**
    bool solve(int i, int[][] matrix)
    在进来的一瞬间，满足property：第i行还没有被放置，前i-1行放置完毕且valid
    solve要在给定的matrix上试图给第i行每个位置放queen。
    

```c++
    bool hasNQueen(int n){//n皇后问题有没有解(理论上应该n>=4的时候，才称为n皇后问题)
		if (n == 0) return false;
		vector<int> matrix;//用一个vector存放皇后的坐标，matrix的下标值表示行，用matrix的元素值表示列
		if (solve1(0, matrix, n))
			return true;//当有一个解时，则返回true
		return false;//没有一个解，返回false
	}
	
	bool solve1(int i, vector<int>& matrix, int n){
		if (n == i){//当到达叶子节点，即第n+1行（这里以i=0为第一行），判断前面生成的有n个queen的matrix是否合法
			if (isValid(matrix))
				return true;
			return false;
		}
		else{//未到达叶子节点，则依次试探该行的位置
			for (int j = 0; j < n; j++){
				matrix.push_back(j);//先把该行压入matrix中
				//剪枝
				if (isValid(matrix)){//如果更改后的matrix合法
					if (solve1(i + 1, matrix, n))//如果求解下一行的结果为true的话，返回true，注意这里不能写成"return solve1(i + 1, matrix, n);",
												//因为，如果求解失败的话，不是返回false，而是，执行后面的pop，然后继续考察下一列
						return true;
				}
				matrix.pop_back();//pop出当前考察的列（即j）
			}
			return false;//当考察完所有的列后，依然没有解得话，则返回false
		}
	}
```
**2. 求解的个数**


**怎么做：** 一行一行的放queen，每行尝试n个可能。这回要找所有，返回值就没有了意义，用void即可。在搜索时，如果有一个可达，仍然要尝试继续进行；每个子选项都试完了，返回。
    
 **边界条件leaf：** 放完第n行 或者 该放第n+1行（出界，返回）
    
**目标条件goal：** n行放完且isValid，即目标一定在leaf上
    
**helper函数：**
    bool solve(int i, int[][] matrix)
    在进来的一瞬间，满足property：第i行还没有被放置，前i-1行放置完毕且valid
    solve要在给定的matrix上试图给第i行每个位置放queen。
    这里为了记录解的个数，设置一个全局变量(static)int是比较efficient的做法。

```c++
    int totalNQueens(int n) {//判断有多少个解
		if (n <= 0) return  0;
		vector<int> matrix;
		int count=0;
		solve2(0, matrix, n, count);
		return count;
	}
	
	void solve2(int i, vector<int> &matrix, int n, int &count){
		if (i == n){
			if (isValid(matrix))
				count++;
			return;
		}
		else{
			for (int j = 0; j < n; j++){
				matrix.push_back(j);
				if (isValid(matrix))//剪枝
					solve2(i + 1, matrix, n, count);
				matrix.pop_back();
			}
		}
	}
```

**3. 求所有解的具体信息**


 **怎么做：** 一行一行地放queen，每行尝试n个可能。返回值同样用void即可。在搜索时，如果有一个可达，仍要继续尝试；每个子选项都试完了，返回。
    
**边界条件leaf：** 放完第n行 或者 该放第n+1出界，返回）
    
**目标条件goal：** n行放满且isValid，即目标一定在leaf上
    
**helper函数：** 
    bool solve(int i, int[][] matrix)
    在进来的一瞬间，满足property：第i行还没有被放置，前i-1行放置完毕且valid
    solve要在给定的matrix上试图给第i行每个位置放queen。
    这里为了记录解的具体情况，设置一个全局变量(static)集合是比较efficient的做法。
    当然也可以把结果集合作为参数传来传去。

```c++
    vector<vector<string>> solveNQueens(int n) {
		vector<vector<string> > res;
		if (n <= 0) return res;
		vector<vector<int> > resInt;
		vector<int> matrix;
		solve3(0, matrix, n, resInt);
		for (int i = 0; i < resInt.size(); i++){			
			vector<string> strs(n, string(n, '.'));
			vector<int> tmp = resInt[i];
			for (int j = 0; j < n; j++)
				strs[j][tmp[j]] = 'Q';
			res.push_back(strs);
		}
		return res;
	}
		
		void solve3(int i, vector<int> &matrix, int n, vector<vector<int> >& resInt){
		if (i == n){
			if (isValid(matrix))
				resInt.push_back(matrix);
			return;
		}
		else{
			for (int j = 0; j < n; j++){
				matrix.push_back(j);
				if (isValid(matrix)){//剪枝
					solve3(i + 1, matrix, n, resInt);
				}
				matrix.pop_back();
			}
		}
	}
	
```
上面的例子用了省空间的方法。
由于每行只能放一个，一共n行的话，用一个大小为n的数组，数组的第i个元素表示第i行放在了第几列上。

其中判断matrix合法的函数

```c++
bool isValid(vector<int> &matrix){//判断当前矩阵是否合法，
		//应该要分别判断行、列、两条对角线是否重合，但是用vector的下标表示行，则row默认不会重合
		int row = matrix.size()-1;
		int col = matrix[row];
		for (int i = 0; i < row; i++){
			int row1 = i;
			int col1 = matrix[row1];
			if (col1 == col)
				return false;
			if (row - col == row1 - col1)//判断沿正向的对角线
				return false;
			if (row + col == row1 + col1)//判断沿反向的对角线 
				return false;
		}
		return true;
	}
```

##  LeetCode——Combinations(组合)

#### **问题描述**

Given two integers n and k, return all possible combinations of k numbers out of 1 ... n.

For example,
If n = 4 and k = 2, a solution is:

```
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```
#### **思路**

这道题让求1到n共n个数字里k个数的组合数的所有情况，还是要用深度优先搜索DFS来解，根据以往的经验，像这种要求出所有结果的集合，一般都是用DFS调用递归来解。那么我们建立一个保存最终结果的大集合res，还要定义一个保存每一个组合的小集合out，每次放一个数到out里，定义一个level来记录当前递归层数（从1开始），终止条件为level==k+1，即超出了边界。（也可以用out.size（）来进行判断：如果out里数个数到了k个，则把out保存到最终结果中，否则在下一层中继续调用递归。）

```c++
class Solution {
public:
    vector<vector<int>> combine(int n, int k) {
        vector<vector<int> > res;
        if(n<k) return res;
        vector<int> out;
        solve(1,1,n,k,out,res);
        return res;
    }
    
    void solve(int level,int first,int n,int k,vector<int> &out,vector<vector<int> > &res){//level表示当前层数，first表示开始元素
        if(level==k+1){
            res.push_back(out);
        }
        else{
            for(int i=first;i<=n;i++){
                out.push_back(i);
                solve(level+1,i+1,n,k,out,res);
                out.pop_back();
            }
        }
    }
};
```


##  LeetCode——Subsets（求子集）

#### **问题描述**
Given a set of distinct integers, nums, return all possible subsets (the power set).

Note: The solution set must not contain duplicate subsets.

For example,
If nums = [1,2,3], a solution is:

#### **思路**
这道与之前求combination的题目类似，只不过这里的k不是固定的，而是从0变化到n，其余一样，代码如下：

```c++
class Solution {
public:
    vector<vector<int> > subsets(vector<int>& nums) {
		vector<vector<int> > res;
		vector<int> out;
		for (int k = 0; k <= nums.size(); k++){//k从0变化到n
			solve(0, k, nums, out, res);
		}
		return res;
	}

	void solve(int first, int k, vector<int> &nums,vector<int> &out, vector<vector<int> > &res){
		if (out.size() == k)
			res.push_back(out);
		else{
			for (int i = first; i < nums.size(); i++){
				out.push_back(nums[i]);
				solve(i + 1, k, nums, out, res);
				out.pop_back();
			}
		}
	}
};
```

##  LeetCode——Subsets II（求子集）

#### **问题描述**

Given a collection of integers that might contain duplicates, nums, return all possible subsets (the power set).
Note: The solution set must not contain duplicate subsets.
For example,
If nums = [1,2,2], a solution is:
···
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]
···

#### **思路**
可以参考LeetCode——Permutations II(全排列II 含有重复元素)的解法，用一个visited数组记录每一个元素的访问状态，当一个元素与上一个元素相同，并且上一个元素处于未访问状态时，则可以跳过该元素。

```c++
class Solution {
public:
	vector<vector<int>> subsetsWithDup(vector<int>& nums) {
		vector<vector<int> > res;
		vector<int> out;
		vector<bool> visited(nums.size(), false);
		sort(nums.begin(), nums.end());
		for (int k = 0; k <= nums.size(); k++){			
			solve(0, k, nums, visited, out, res);
		}
		return res;
	}

	void solve(int first, int k, vector<int> &nums, vector<bool> &visited, vector<int> &out, vector<vector<int> > &res){
		if (out.size() == k)
			res.push_back(out);
		else{
			for (int i = first; i<nums.size(); i++){
				if (k == 3 && first==0 && i==1)
					cout << endl;
				if (i>0 && !visited[i - 1] && nums[i - 1] == nums[i])
					continue;
				out.push_back(nums[i]);
				visited[i] = true;
				solve(i + 1, k, nums, visited, out, res);//注意是i+1，而不是first+1
				visited[i] = false;
				out.pop_back();
			}
		}
	}
};

```


##  LeetCode——Permutations(全排列)


#### **问题描述**
Given a collection of distinct numbers, return all possible permutations.

For example,
[1,2,3] have the following permutations:

```
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```
#### **思路**
这道题是求全排列问题，给的输入数组没有重复项，这跟之前的那道 Combinations 组合项 和类似，解法基本相同，但是不同点在于那道不同的数字顺序只算一种，是一道典型的组合题，而此题是求全排列问题，还是用递归DFS来求解。这里我们需要用到一个visited数组来标记某个数字是否访问过，然后在DFS递归函数从的循环应从头开始，而不是从level开始，这是和 Combinations 组合项 不同的地方，其余思路大体相同，代码如下：

```c++
class Solution {////原来的数组中不包含重复元素
public:
	vector<vector<int>> permute(vector<int>& nums) {
		vector<vector<int> > res;
		vector<int> out;
		vector<bool> visited(nums.size(), false);//该数组用来记录相应下标的元素有没有被访问，初始时为false		
		solve(0, out, nums,visited, res);
		return res;
	}

	void solve(int level, vector<int> &out, vector<int> &nums,vector<bool> &visited,vector<vector<int> > &res){
		if (level == nums.size()){
			res.push_back(out);
			return;
		}
		else{
			for (int i = 0; i < nums.size(); i++){				
				if (!visited[i]){
					out.push_back(nums[i]);
					visited[i] = true;
					solve(level + 1, out, nums, visited, res);
					visited[i] = false;
					out.pop_back();
				}				
			}
		}
	}
};
```

##  LeetCode——Permutations II(全排列II 含有重复元素)

#### **问题描述**
Given a collection of numbers that might contain duplicates, return all possible unique permutations.

For example,
[1,1,2] have the following unique permutations:

#### **思路**
这道题是之前那道 Permutations 全排列的延伸，由于输入数组有可能出现重复数字，如果按照之前的算法运算，会有重复排列产生，我们要避免重复的产生，**在递归函数中要判断前面一个数和当前的数是否相等，如果相等，前面的数必须已经使用了，即对应的visited中的值为1，当前的数字才能使用，否则需要跳过**，这样就不会产生重复排列了，代码如下：

```c++
class Solution2 {//原来的数组中包含重复元素
public:
	vector<vector<int>> permuteUnique(vector<int>& nums) {
		vector<vector<int> > res;
		vector<int> out;
		vector<bool> visited(nums.size(), false);
		sort(nums.begin(), nums.end());//将nums进行排序
		solve(0, out, nums, res, visited);
		return res;
	}

	void solve(int level, vector<int> &out, vector<int> &nums, vector<vector<int> > &res, vector<bool> &visited){
		if (level == nums.size()){
			res.push_back(out);			
		}
		else{
			for (int i = 0; i < nums.size(); i++){				
				if (!visited[i]){
					if (i>0 && nums[i] == nums[i - 1] && !visited[i - 1])
						continue;
					out.push_back(nums[i]);
					visited[i] = true;					
					solve(level + 1, out, nums, res, visited);
					visited[i] = false;
					out.pop_back();
				}
			}
		}
	}
};
```

##  LeetCode——Palindrome Partitioning（拆分回文串）

#### **问题描述**

Given a string s, partition s such that every substring of the partition is a palindrome.

Return all possible palindrome partitioning of s.

For example, given s = "aab",
Return

```
[
  ["aa","b"],
  ["a","a","b"]
]
```

#### **思路**
这又是一道需要用DFS来解的题目，既然题目要求找到所有可能拆分成回文数的情况，那么肯定是所有的情况都要遍历到，对于每一个子字符串都要分别判断一次是不是回文数，那么肯定有一个判断回文数的子函数，还需要一个DFS函数用来递归，再加上原本的这个函数，总共需要三个函数来求解。代码如下：


```c++
class Solution {
public:
	vector<vector<string>> partition(string s) {
		vector<vector<string> > res;
		vector<string> out;
		solve(0, s.size(), s, out, res);
		return res;
	}

	void solve(int start, int n, string s,vector<string> &out, vector<vector<string> > &res){
		if (n == 0){
			res.push_back(out);
		}
		else{
			for (int count = 1; count <= n; count++){//子串的长度从1到n(n为从start到字符结尾的长度）
				string s_tmp = s.substr(start, count);
				if (isPalindrome(s_tmp)){
					out.push_back(s_tmp);
					solve(start + count, n - count, s, out, res);//考察去掉该子串后剩余的子串
					out.pop_back();
				}
			}
		}
	}

	bool isPalindrome(string s){
		if (s.empty())
			return false;
		int beg = 0, end = s.size() - 1;
		while (beg < end){
			if (s[beg] != s[end])
				return false;
			beg++;
			end--;
		}
		return true;
	}
};
```
 
 


那么，对原字符串的所有子字符串的访问顺序是什么呢，如果原字符串是 abcd, 那么访问顺序为: a -> b -> c -> d -> cd -> bc -> bcd-> ab -> abc -> abcd, 这是对于没有两个或两个以上子回文串的情况。那么假如原字符串是 aabc，那么访问顺序为：a -> a -> b -> c -> bc -> ab -> abc -> aa -> b -> c -> bc -> aab -> aabc，中间当检测到aa时候，发现是回文串，那么对于剩下的bc当做一个新串来检测，于是有 b -> c -> bc，这样扫描了所有情况，即可得出最终答案。

##  LeetCode——gray code（格雷码）
#### **问题描述**
The gray code is a binary numeral system where two successive values differ in only one bit.

Given a non-negative integer n representing the total number of bits in the code, print the sequence of gray code. A gray code sequence must begin with 0.

For example, given n = 2, return [0,1,3,2]. Its gray code sequence is:

```
00 - 0
01 - 1
11 - 3
10 - 2
```
Note:
For a given n, a gray code sequence is not uniquely defined.

For example, [0,2,3,1] is also a valid gray code sequence according to the above definition.

For now, the judge is able to judge based on one instance of gray code sequence. Sorry about that.

#### **思路**
**第一种解法** 
（参考网上的，不是太理解）
代码如下：
```c++
    vector<int> grayCode(int n) {
		vector<int> res;
		int out = 0;
		solve(0, n, out, res);
		return res;
	}

	void solve(int k, int n,int &out, vector<int> &res){
		if (k == n){
			res.push_back(out);
			return;
		}
		solve(k + 1, n, out, res);
		out = out ^ (1 << (n - k - 1));
		solve(k + 1, n, out, res);
	}
```

**第二种解法**
![_config.yml]({{ site.baseurl }}/images/gray_code.png)
如上图所示，可以发现格雷码的规律：
1位的格雷码就是0，1
2位的格雷码是在一位的格雷码前面加上0或1.
并由上图可以发现，2位的格雷码后一位是镜像对称的；3位的格雷码的后两位是镜像对称的；4位的格雷码后3位是镜像对称的。
规律就是n位格雷码是n-1位格雷码的基础上，先将n-1位镜像对称，然后前一半首位加0，后一半首位添1得到。如果要输出n位格雷码，就得先生成n-1位格雷码，这样自然会想到用回溯的方法来编程。
具体实现要先考虑基本的case，也就是n=1的情况，应该先在vector中添加两个数0,1，之后n=2的时候倒着读加上1。

```c++
 vector<int> grayCode(int n) {
		if (n == 0){
			vector<int> res = { 0 };
			return res;
		}
		vector<int> res = grayCode(n - 1);
		int orgSize = res.size();
		for (int i = orgSize - 1; i >= 0; i--){
			res.push_back((1 << (n - 1)) + res[i]);
		}
		return res;
    }
```


##  参考
[[Leetcode] Backtracking回溯法(又称DFS,递归)全解](https://segmentfault.com/a/1190000006121957)
[[LeetCode] Permutations 全排列](http://www.cnblogs.com/grandyang/p/4358848.html)
[[LeetCode] Permutations II 全排列之二](http://www.cnblogs.com/grandyang/p/4359825.html)
[[LeetCode] Palindrome Partitioning 拆分回文串](https://www.cnblogs.com/grandyang/p/4270008.html)


