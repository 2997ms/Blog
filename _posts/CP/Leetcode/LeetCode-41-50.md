---
title: LeetCode 41-50
date: 2016-12-12 20:43:11
tags: [LeetCode, DP]
categories: [题记]
---

再不写blog估计这里就要长草了。。。最近自己在弄程序静态分析这块，提取相关的特征，IDA python 提取程序的操作码以及立即数。最近有面试通知，想了想还是要做LeetCode，于是在赶LeetCode的进度。。。LeetCode现在做到了200+左右，后面会陆续写LeetCode的总结。51nod四级题还差十几个，做完然后好好理一理思路。(都快忘了怎么写blog了。。。)

<!--more-->

### 41 First Missing Positive

> 给定一个数组，找最小的没有出现的正数。要求时间复杂度$O(n)$，空间复杂度是常数。

考虑让数组的第$i$位是$i+1$，对每个位置不断地swap，然后在扫一遍第一个不为$i+1$的数。

```C++
class Solution 
{
public:
	int firstMissingPositive(vector<int>& nums) 
	{
		int sz = nums.size();
		if (sz == 0)return 1;
		for (int i = 0; i < sz; )
		{
			if (nums[i] != i + 1 && nums[i] <= sz&&nums[i] >= 1&&nums[i]!=nums[nums[i]-1])
			{
				swap(nums[i], nums[nums[i] - 1]);
			}
			else
			{
				i++;
			}
		}
		for (int i = 0; i < sz; i++)
		{
			if (nums[i] != i + 1)
			{
				return i + 1;
			}
		}
		return sz + 1;
	}
};
```

### 42 Trapping Rain Water

>题意是给出各个位置的高度，求最终盛水的容量。

![](https://raw.githubusercontent.com/2997ms/My_Algorithm/master/source_pic/LeetCode41_50/1.png)

发现对于每一个位置来说，盛水的量是两边最大值取个最小，再减去自己的高。

```c++
class Solution
{
public:
	int trap(vector<int>& height)
	{
		if (height.size() < 3)return 0;
		int i, j, k;
		int sz = height.size();
		vector<int>le(sz, 0);
		vector<int>ri(sz, 0);

		le[0] = height[0], ri[sz - 1] = height[sz - 1];
		for (i = 1; i <= sz - 2;i++)
		{
			le[i] = max(le[i - 1], height[i]);
		}
		for (i = sz - 2; i >= 1; i--)
		{
			ri[i] = max(ri[i + 1], height[i]);
		}
		int res = 0;
		for (i = 1; i <= sz - 2; i++)
		{
			int val = min(le[i - 1], ri[i + 1]);
			if (height[i] >= val)continue;
			res += (val - height[i]);
		}
		return res;
	}
};
```

### 43 Multiply Strings

> 两个大数相乘。

```C++
class Solution
{
public:
	string multiply(string num1, string num2)
	{
		int len1 = num1.length();
		int len2 = num2.length();
		string res(len1 + len2, '\0');

		for (int i = len1 - 1; i >= 0; i--)
		{
			int c = 0;
			for (int j = len2 - 1; j >= 0; j--)
			{
				int k = i + j + 1;
				int v1 = num1[i] - '0';
				int v2 = num2[j] - '0';
				res[k] = res[k] + v1*v2 + c;
				c = res[k] / 10;
				res[k] = res[k] % 10;
			}
			res[i] += c;
		}

		size_t pos = res.find_first_not_of('\0');
		if (string::npos != pos)
		{
			for (int i = pos; i < res.length(); i++)
			{
				res[i] = res[i] + '0';
			}
			return res.substr(pos);
		}
		return "0";
	}
};
```

### 44 Wildcard Matching

> ?可以匹配任意字符，*可以匹配任意字符序列，问后者能否匹配前者。

DP。开一个二维数组。

```C++
class Solution
{
public:
	bool isMatch(string s, string p)
	{
		int len1 = s.length();
		int len2 = p.length();
		vector<vector<bool>>dp(len2 + 1, vector<bool>(len1 + 1, 0));
		s = ' ' + s;
		p = ' ' + p;
		dp[0][0] = 1;
		for (int i = 1; i <= len2; i++)
		{
			if (dp[i - 1][0])
			{
				if (p[i] == '*')
				{
					dp[i][0] = 1;
				}
			}
		}
		for (int i = 1; i <= len2; i++)
		{
			for (int j = 1; j <= len1; j++)
			{
				if (p[i] == '?')
				{
					if (dp[i - 1][j - 1])
					{
						dp[i][j] = 1;
					}
				}
				else if (p[i] == '*')
				{
					if (dp[i - 1][j - 1])
					{
						dp[i][j] = 1;
					}
					if (dp[i - 1][j])dp[i][j] = 1;
					if (dp[i][j - 1])dp[i][j] = 1;
				}
				else
				{
					if (p[i] == s[j])
					{
						if (dp[i - 1][j - 1])
						{
							dp[i][j] = 1;
						}
					}
				}
			}
		}
		return dp[len2][len1];
	}
};
```

时间实在感人，把二维化成一维。发现不同的情况，循环用到的值会有变化，所以for循环会不一样。

```c++
class Solution
{
public:
	bool isMatch(string s, string p)
	{
		int len1 = s.length();
		int len2 = p.length();
		vector<bool>dp(len1 + 1,0);
		s = ' ' + s;
		p = ' ' + p;
		dp[0] = 1;
		for (int i = 1; i <= len2; i++)
		{
			if (p[i] == '*')
			{
				for (int j = 1; j <= len1; j++)
				{
					dp[j] = dp[j] | dp[j - 1];
				}
			}
			else
			{
				for (int j = len1; j >= 1; j--)
				{
					if ((p[i] == '?') ||((p[i] == s[j])))
					{
						dp[j] = dp[j - 1];
					}
					else
					{
						dp[j] = false;
					}
				}
			}
			dp[0] = dp[0] & (p[i] == '*');
		}
		return dp[len1];
	}
}wc;
```

### 45 Jump Game II 

> 给每个位置能够跳的最远距离，问从起始节点到终点最少要跳多少次。

正常来讲要对每一个位置记录前面num[i]+i，即能够跳的最远位置，当能够跳的位置不够当前位置时，从前面中选择最远的距离来跳。

```C++
class Solution 
{
public:
	int jump(vector<int>& nums)
	{
		int res = 0;
		int curMax = 0;
		int curDis = 0;

		for (int i = 0; i < nums.size(); i++)
		{
			if (curDis < i)
			{
				res++;
				curDis = max(curDis, curMax);
			}
			curMax = max(curMax, nums[i] + i);
		}
		return res;
	}
};
```

### 47 Permutations II

> 数组中有重复元素，求所有排列的组合。

和之前求和的组合没有太多不同，都是i!=begin&&num[i]==num[i-1] continue.

这次不断交换所有元素的值，回溯。

```C++
void perm(vector<vector<int>>& res, vector<int> nums, int k)
{
	if (k == nums.size())
	{
		res.push_back(nums);
		return;
	}
	for (int i = k; i < nums.size(); ++i)
	{
		if (i != k && nums[i] == nums[k]) continue;
		swap(nums[i], nums[k]);
		perm(res, nums, k + 1);
	}
}

class Solution {
public:
	vector<vector<int>> permuteUnique(vector<int>& nums)
	{
		vector<vector<int>>res;
		sort(nums.begin(),nums.end());
		perm(res,nums,0);
		return res;
	}
};
```



### 48 Rotate Image

> 顺时针将n*n的矩阵旋转90度。

从外向里不断对四个点交换它们的值。

```C++
class Solution
{
public:
	void rotate(vector<vector<int>>& matrix)
	{
		int n = matrix.size();
		if (n == 0)return;

		int sz = n;
		int cur = 0;
		while (sz > 1)
		{

			for (int i = cur; i < sz - 1; i++)
			{
				int tmp = matrix[cur][i];
				matrix[cur][i] = matrix[n - 1 - i][cur];
				matrix[n - 1 - i][cur] = matrix[n - 1 - cur][n - 1 - i];
				matrix[n - 1 - cur][n - 1 - i] = matrix[i][n - 1 - cur];
				matrix[i][n - 1 - cur] = tmp;
			}
			cur++;
			sz--;
		}

	}
}wc;
```



### 50 Pow(x, n)

> 求x的n次方。

记一下这个题只是为了提醒自己，x和n都可能是各种数，各个情况要考虑到。

```C++
class Solution {
public:
    double myPow(double x, int n) 
    {
    	double res=1;
    	int f=0;
    	long long tmp=n;
    	if(tmp<0)
    	{
    		f=1;
    		tmp=-tmp;
    	}
    	while(tmp)
    	{
    		if(tmp&1)
    		{
    			res=res*x;
    		}
    		x=x*x;
    		tmp>>=1;
    	}  
    	if(f)res=1.0/res;  
    	return res;
    }
};
```