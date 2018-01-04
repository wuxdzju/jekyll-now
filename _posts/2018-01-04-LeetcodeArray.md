---
layout: post
title: LeetCode中一些关于Array的题
---

##  130. Surrounded Regions
### **题目：**
Given a 2D board containing 'X' and 'O' (the letter O), capture all regions surrounded by 'X'.
A region is captured by flipping all 'O's into 'X's in that surrounded region.
For example,
```
X X X X
X O O X
X X O X
X O X X
```
After running your function, the board should be:

```
X X X X
X X X X
X X X X
X O X X
```
### **思路：**
这道题目与围棋类似，被包围的O都会被转换成X，与围棋不同的是，位于边缘的O不会被转换。故一种解题的思路是：DFS遍历位于边缘的O，将其都变为中间变量T，然后，将剩下的O全部转换成X，之后，再将中间变量T转换成O.

### **代码：**
```
class Solution {
public:
    void solve(vector<vector<char>> &board) {
        for(int i=0;i<board.size();i++){//对位于边缘的O进行DFS搜索
            for(int j=0;j<board[i].size();j++){
                if((i==0 || i==board.size()-1 || j==0 || j==board[i].size()-1) && board[i][j]=='O')
                    srDFS(board,i,j);
            }
        } 
        for(int i=0;i<board.size();i++){//将被包围的O转换成X
            for(int j=0;j<board[i].size();j++){
                if(board[i][j]=='O')
                    board[i][j]='X';
            }            
        }
        for(int i=0;i<board.size();i++){//将中间变量T转换成O
            for(int j=0;j<board[i].size();j++){
                if(board[i][j]=='T')
                    board[i][j]='O';
            }            
        }
    }
    
    void srDFS(vector<vector<char> > &board,int i,int j){//深度优先搜索
        board[i][j]='T';
        if(i>0 && board[i-1][j]=='O')
            srDFS(board,i-1,j);
        if(i<board.size()-1 &&board[i+1][j]=='O')
            srDFS(board,i+1,j);
        if(j>0 && board[i][j-1]=='O')
            srDFS(board,i,j-1);
        if(j<board[i].size()-1 &&board[i][j+1]=='O')
            srDFS(board,i,j+1);
    }
};
```

##  75. Sort Colors
### **题目：**
Given an array with n objects colored red, white or blue, sort them so that objects of the same color are adjacent, with the colors in the order red, white and blue.
Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.
Note:
You are not suppose to use the library's sort function for this problem.
Follow up:
A rather straight forward solution is a two-pass algorithm using counting sort.
First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.
Could you come up with an one-pass algorithm using only constant space?

### **思路：**
一种思路是利用基数排序，进行两趟比较即可实现该功能。但题目要求只用一趟比较，并且只用一个常量的空间。所以，可以参考快速排序中的思想，与快排中不同的是该解法中使用了两个指针，一个指针f从头开始，并指向比1小的最后一个元素，一个指针e从尾部开始，并指向比1大的第一个元素，将每个元素与1进行比较，小于1的话交换到小于1的区域，并将当前考察指针向后移一位，比1大的话交换到大于1的区域，等于1的话，当前考察指针向后移一位，当当指针i等于e时，结束循环。

### **代码：**
```
class Solution {
public:
    void sortColors(int A[], int n) {
        //if(n<=0) return; 这句可以去掉，因为size=0的情况在后面的循环中已经考虑过了
        int f=-1,e=n,i=0;
        while(i<e){
            if(A[i]==1)
                i++;
            else if(A[i]<1){
                f++;
                swap(A[i],A[f]);
                i++;
            }
            else{
                e--;
                swap(A[i],A[e]);
            }
        }
    }
};
```