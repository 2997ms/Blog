---
title: LeetCode 31-40
date: 2016-10-24 23:34:16
tags: [LeetCode]
categories: [题记]
---



...<!--more-->

### 31 Next Permutation

> 求一个序列的下一个字典序。

这个题直接next_permutation()函数就可以，在[discuss](https://discuss.leetcode.com/topic/8508/9-lines-of-c-code-with-comments/2)里面看到一个二分解法，很赞。

```c++
class Solution 
{
public:
    void nextPermutation(vector<int>& nums)
    {
    	int sz = nums.size();
    	if(sz<=1)return;
    	for(int i=sz-2;i>=0;i--)
    	{
    		if(nums[i]<nums[i+1])
    		{
    			break;
    		}
    	}        
    	reverse(begin(nums) + i + 1,end(nums));
    	if(i==-1)return;
    	int pos = upper_bound(begin(nums)+i+1,end(nums),nums[i]);
    	swap(nums[pos],nums[i]);
    }
};
```

### 32 Longest Valid Parentheses

> 给出一个括号序列，求里面最长合法括号序列。

使用栈来记录不合法的位置。求出最长的区间长度。

```C++
class Solution
{
public:
	int longestValidParentheses(string s) 
	{
		stack<int>st;
		int res = 0;
		int len = s.length();

		for (int i = 0; i < len; i++)
		{
			if (s[i] == ')')
			{
				if (!st.empty() && s[st.top()] == '(')
				{
					st.pop();
				}
				else
				{
					st.push(i);
				}
			}
			else
			{
				st.push(i);
			}
		}
		if (st.empty())
		{
			res = len;
		}
		else
		{
			int ri = len, le = 0;
			while (!st.empty())
			{
				int k = st.top();
				st.pop();
				res = max(res, ri - k - 1);
				ri = k;
			}
			res = max(res, ri);
		}
		return res;
	}
};
```



### 33 Search in Rotated Sorted Array

> 在一个循环的有序数组上找target值。

$O(logn)$二分找。我先是找最小值，然后在此基础上取模找target。

```C++
class Solution
{
public:
	int search(vector<int>& nums, int target) 
	{
		int n = nums.size();
		int ri = n - 1;
		int le = 0;
		while (le < ri)
		{
			int mid = (le + ri) >> 1;
			if (nums[mid]>nums[ri])
			{
				le = mid + 1;
			}
			else
			{
				ri = mid;
			}
		}
		int t = le;
		le = 0, ri = n - 1;
		while (le < ri)
		{
			int mid = (le + ri + 1) >> 1;
			int p = (t + mid) % n;
			if (nums[p]>target)
			{
				ri = mid - 1;
			}
			else
			{
				le = mid;
			}
		}
		if (nums[(le + t) % n] == target)return (le + t) % n;
		else return -1;
	}
};
```

这个题也可以直接二分找target，判断各段的情况，[discuss](https://discuss.leetcode.com/topic/7711/revised-binary-search)。

```C++
public class Solution {
public int search(int[] A, int target) {
    int lo = 0;
    int hi = A.length - 1;
    while (lo < hi) {
        int mid = (lo + hi) / 2;
        if (A[mid] == target) return mid;
        
        if (A[lo] <= A[mid]) {
            if (target >= A[lo] && target < A[mid]) {
                hi = mid - 1;
            } else {
                lo = mid + 1;
            }
        } else {
            if (target > A[mid] && target <= A[hi]) {
                lo = mid + 1;
            } else {
                hi = mid - 1;
            }
        }
    }
    return A[lo] == target ? lo : -1;
}
```



### 37 Sudoku Solver 

> 解数独。

结合LeetCode 36判断数独是否有效，注意先判断后递归，把判断放左边。。。

```C++
bool isValidSudoku(vector< vector < char> >& board,int x,int y)
{
	for (int i = 0; i < 9; i++)
	{
		if (i == x)continue;
		if (board[i][y] == board[x][y])return false;
	}
	for (int i = 0; i < 9; i++)
	{
		if (i == y)continue;
		if (board[x][i] == board[x][y])return false;
	}
	int k = (x / 3)* 3 + y / 3;
	for (int i = 0; i < 9; i++)
	{
		for (int j = 0; j < 9; j++)
		{
			if (i == x&&j == y)continue;
			int k2 = (i / 3) * 3 + j / 3;
			if (k == k2)
			{
				if (board[x][y] == board[i][j])
				{
					return false;
				}
			}
		}
	}
	return true;
}

class Solution 
{
public:
	bool solveSudoku(vector<vector<char>>& board)
	{
		for (int i = 0; i < 9; i++)
		{
			for (int j = 0; j < 9; j++)
			{
				if (board[i][j] == '.')
				{
					for (int k = 1; k <= 9; k++)
					{
						board[i][j] = '0' + k;
						if (isValidSudoku(board,i,j) && solveSudoku(board))
						{
							return true;
						}
						board[i][j] = '.';
					}
					return false;
				}
			}
		}
		return true;
	}
};

```



### 39 Combination Sum

> 给出一个数组，给出一个target，求数组中的数的各种组合，使得其和等于target。数组中的数字可以无限次数。

```C++
void dfs(vector<vector<int>>&res, vector<int>& val, vector<int> &cur, int tar, int idx)
{
	if (tar == 0)
	{
		res.push_back(cur);
		return;
	}
	int sz = val.size();
	for (int i = idx; i < sz; i++)
	{
		if (tar < val[i])break;
		cur.push_back(val[i]);
		dfs(res, val, cur, tar - val[i], i);
		cur.pop_back();
	}
}

class Solution 
{
public:
	vector<vector<int>> combinationSum(vector<int>& candidates, int target)
	{
		vector<vector<int>>res;
		vector<int> cur;

		if (candidates.size() == 0)return res;
		sort(candidates.begin(), candidates.end());

		dfs(res, candidates, cur, target, 0);

		return res;
	}
};
```



### 40 Combination Sum II

> 这里与上一题不同的是数组中的数字只能出现一次。

注意使用begin判断。这里其实就是算在begin开始的target值，那么只要重复的，一律不管。因为重复的都在begin之前与begin这里重复了。

```C++


void dfs(vector<vector<int>>&res, vector<int>& val, vector<int> &cur, int tar, int begin)
{
	if (tar == 0)
	{
		res.push_back(cur);
		return;
	}
	int sz = val.size();
	for (int i = begin; i < sz; i++)
	{
		if (tar < val[i])break;
		if(i!=begin&&val[i]==val[i-1])continue;
		
		cur.push_back(val[i]);
		dfs(res, val, cur, tar - val[i], i+1);
		cur.pop_back();
	}
}

class Solution 
{
public:
	vector<vector<int>> combinationSum2(vector<int>& candidates, int target)
	{
		vector<vector<int>>res;
		vector<int> cur;

		if (candidates.size() == 0)return res;
		sort(candidates.begin(), candidates.end());

		dfs(res, candidates, cur, target, 0);

		return res;
	}
};
```