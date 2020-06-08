---
title: LeetCode 1-10
date: 2016-09-21 10:15:24
tags: [LeetCode, 指针, 中位数, 二分]
categories: [题记]
---

时维九月，序属三秋。然而，美好的九月都要过完啦。。。要做一做LeetCode上面的题啦，一直说要把这个做完，一直没有恒心做下去，马上十月了，一天三道，希望年底能把这些K完。昨天写日记的时候，又有一个日记本要写完了，然后就想起在家里的从七岁开始写下的十几个本子(好像到二十了？也不太清楚了)，突然莫名地觉得下一个本子是我人生的第二部了，希望下一部能够精彩吧。之前接触过一些acmer，其中好些人对LeetCode的态度是很鄙视的，觉得太简单。我对这些表示鄙视的人表示反鄙视，这里面的有些题过掉可能不难，但是在面试中，时常要求的是最低时间复杂度、空间复杂度，这样其实有些题(链表啊，各种STL的设计啊)很变态的，写的时候参考了很多discuss，还有csdn博客上面的内容。

<!--more-->

> 不会所有题都记，记录需要注意的地方。毕竟写这个是一个笔记，希望以后能够有新的心得。

### LeetCode1 Two Sum

>题意是给定一个数组，然后给出一个target，要求在数组中找到下标不相等的两个数之和等于target，返回这两个数的下标。

如果是数组有序的话，那么一前一后扫一遍就好了。现在数组无序，可以暴力$O(n^2)$搞。优化的话就是拿一个map记录前面出现过的元素，然后通过target-当前值 去找前面是否出现过目标值。找到返回即可。

```c++
class Solution 
{
public:
    vector<int> twoSum(vector<int>& nums, int target) 
    {
    	int sz=nums.size();
    	if(sz <= 1)return {-1,-1};
    	unordered_map<int,int>hax;
    	for(int i=0;i<sz;i++)
    	{
    		if(hax.find(target-nums[i])!=hax.end())
    		{
    			return {hax[target-nums[i]],i};
    		}
    		hax[nums[i]]=i;
    	}
    	return {-1,-1};
    }
};
```

### LeetCode2 Add Two Numbers

> 题意是给出两个链表，每个链表代表一个数字，要求计算这两个数字相加的和，同样通过指针返回结果。

之前写程序的时候有过一次指针跑飞的经历，那个bug找了很久一直找不到。所以一直对指针这块怀有恐惧心理。。。

这个题好在数字是反过来的，不用从后往前去计算，要不然还要麻烦。

这样就判断两个指针+进位值是否为0，有一个不为零就要开一个新位置计算，并且返回最开始的链表头结点。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution 
{
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) 
    {	
    	ListNode *pre = new ListNode(0);
    	ListNode *res = pre;
    	int ex = 0;
    	while(l1||l2||ex)
    	{
    		int sum = (l1?l1->val:0) + (l2?l2->val:0) + ex;
    		ListNode *a = new ListNode(sum%10);
    		ex=sum/10;
    		pre->next = a;
    		pre = a;
    		l1 = l1?l1->next:0;
    		l2 = l2?l2->next:0;
    	}   
    	return res->next;
    }
};
```

### LeetCode3 Longest Substring Without Repeating Characters

> 题意是给定一个字符串，找出里面最长不重复子串。

维护当前元素在这之前出现的最近位置，两者相减就是当前位置为最后位置的不重复子串，这里面取最大值。

```C++

class Solution 
{
public:
    int lengthOfLongestSubstring(string s) 
    {
    	int len = s.length();
    	if(len==0)return 0;
    	vector<int>dic(256,-1);

    	int res=0,begin=-1;
    	for(int i=0;i<len;i++)
    	{
    		if(dic[s[i]]>begin)
    		{
    			begin=dic[s[i]];
    		}
    		dic[s[i]]=i;
    		res=max(res,i-begin);
    	}
    	return res;
    }
};
```



### LeetCode4 Median of Two Sorted Arrays

> 题意是给出两个有序数组，要求你给出这两个有序数组合并之后数组的中位数。

这个题就很好玩在于不断二分较短长度数组的位置，因为是中位数，必然把数组里面的元素分成两部分，通过该位置和数量的关系，能够求出另一个数组被分开的位置。这样会得到4个断点，L1，L2，R1，R2。判断断点是否合理再去进行下一步的二分。很赞的一道题。

另外奇偶的部分，可以通过\*2来解决。

```C++
class Solution 
{
public:
	double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2)
	{
		int n1 = nums1.size();
		int n2 = nums2.size();

		if (n1 > n2)
		{
			return findMedianSortedArrays(nums2, nums1);
		}
		if (n2 == 0)
		{
			return (nums1[(n1 - 1) / 2] + nums1[n1 / 2]) / 2.0;
		}
		int le = 0, ri = 2 * n1;
		while (le <= ri)
		{
			int mid1 = (le + ri) >> 1;
			int mid2 = n1 + n2 - mid1;

			int L1 = mid1 == 0 ? INT_MIN : nums1[(mid1 - 1) / 2];
			int R1 = mid1 == 2 * n1 ? INT_MAX : nums1[mid1 / 2];

			int L2 = mid2 == 0 ? INT_MIN : nums2[(mid2 - 1) / 2];
			int R2 = mid2 == 2 * n2 ? INT_MAX : nums2[mid2 / 2];

			if (L1 > R2)
			{
				ri = mid1 - 1;
			}
			else if (L2 > R1)
			{
				le = mid1 + 1;
			}
			else
			{
				return (max(L1, L2) + min(R1, R2)) / 2.0;
			}
		}
		return -1;
	}
};
```



### LeetCode5 Longest Palindromic Substring

> 求最长回文子串。

$O(n^2)$暴力。$O(n)$马拉车算法。~~留坑晚上填。。。~~

相当于回顾了一下马拉车算法，[这个讲解](http://blog.csdn.net/dyx404514/article/details/42061017)我觉得就很清晰了。算每一位Len[j\]的时候都已经知道了前面的Len[i\]，算最远距离 - i，与最远距离对称点的长度，两者比较是最小的Len[j\]。

```C++
string change(string x)
{
	string res="@";
	int len=x.length();

	for(int i=0;i<len;i++)
	{
		res = res + '#';
		res = res + x[i];
	}
	res = res + '#';
	res = res + '$';
	return res;
}

class Solution 
{
public:
    string longestPalindrome(string s) 
    {
    	int len=s.length();
    	if(len==0)return "";
    	s=change(s);
    	len=s.length();
        vector<int> Len(len+1,0);
        int mx=0,ans=0,po=0,resp=0;
        for(int i=1;i<=len;i++)
        {
        	if(mx>i)
        	{
        		Len[i] = min(mx-i,Len[2*po-i]);
        	}
        	else
        	{
        		Len[i] = 1;
        	}
        	while(s[i-Len[i]]==s[i+Len[i]])
        	{
        		Len[i]++;
        	}
        	if(Len[i]+i>mx)
        	{
        		mx=Len[i]+i;
        		po=i;
        	}
        	if(Len[i]>ans)
        	{
        		ans=Len[i];
        		resp = i;
        	}
        }
        string r;
    	for(int i=resp; i<=resp+ans-1 ;i++)
    	{
    		r=r+s[i];
    	}
    	string pre=r;
    	reverse(r.begin(),r.end());
    	string all = r+pre;
    	string ret="";
    	if(s[resp]=='#')
    	{
    		for(int i=0;i<all.length();i++)
    		{
    			if(all[i]=='#')continue;
    			ret=ret+all[i];
    		}
    	}
    	else
    	{
			for(int i=0;i<all.length();i++)
    		{
    			if(i>=1&&all[i]==all[i-1])continue;
    			if(all[i]=='#')continue;
    			ret=ret+all[i];
    		}
    	}
    	return ret;
    }
};
```



### LeetCode7 Reverse Integer

> 题意是给出一个整数，要求左右翻过来。

**这个题千万注意100(末置位带0的)，负数，溢出这三种情况**。之前百度面试的时候碰到过这种特殊情况，需要你考虑到的。

```c++
class Solution
{
public:
    int reverse(int x)
    {
    	if(x==0)return 0;
    	int f=0;
    	if(x<0)
    	{
    		f=1;
    		x=-x;
    	}
        int dig[20];
        int cnt=0;
        while(x)
        {
        	cnt++;
        	dig[cnt]=x%10;
        	x/=10;
        }
        long long res=0;
        for(int i=1;i<=cnt;i++)
        {
        	res=res*10+dig[i];
        }
        if(f)
        {
        	res=-res;
        }
        if(res>2147483647||res<-2147483648)
        {
        	return 0;
        }
        else
        {
        	return res;
		}
    }
};
```



### LeetCode8 String to Integer (atoi)

> 题意是将字符串转换成整形。

注意正负号。

```c++
class Solution 
{
public:
    int myAtoi(string str)
    {  
        int i=0;
        int sign=1;
        while(str[i]==' ')i++;
        if(str[i]=='-'||str[i]=='+')
        {
            sign = 1-2*(str[i]=='-');
            i++;
        }
        int base=0;
        int len=str.length();

        while(i<len&&str[i]<='9'&&str[i]>='0')
        {
            if(base>INT_MAX/10||(base==INT_MAX/10&&str[i]>='7'))
            {

                if(sign==1)
                {
                    return INT_MAX;
                }
                else
                {
                    if(base>INT_MAX/10||(base==INT_MAX/10&&str[i]>='8'))
                    {
                        return INT_MIN;
                    }
                }
            }

            base=base*10+str[i]-'0';
            i++;
        }

        if(sign==-1)
        {
            base=-base;
        }
        return base;
    }
};
```



### LeetCode10 Regular Expression Matching

> 题意是给出了一种匹配方式，‘.’代表可以匹配任意字符，'*'代表前面的字符可以出现0次到多次。

这个题好恶心啊啊啊。首先题意那块我就绕了很久。'.*'就代表可以匹配所有字符了。我以为前面的‘.’就已经固定了。

之后肯定是dp了。dp[i\][j\]表示第i个字符最远匹配前者到第j个字符。

其余的都没啥，主要是'*'这个字符，考虑前面的字符出现0次，1次，多次三种情况。

```C++
class Solution 
{
public:
	bool isMatch(string s, string p)
	{
		int m = s.length();
		int n = p.length();
		vector<vector<int>>dp(n + 1, vector<int>(m + 1, 0));

		dp[0][0] = 1;
		for (int i = 1; i <= n; i++)
		{
			if (p[i - 1] == '*'&&dp[i - 2][0])
			{
				dp[i][0] = 1;
			}
		}
		for (int i = 1; i <= n; i++)
		{
			for (int j = 1; j <= m; j++)
			{
				if (p[i - 1] == '*')
				{
					dp[i][j] |= dp[i - 2][j];//0
					dp[i][j] |= dp[i - 2][j - 1] && (p[i - 2] == '.' || p[i - 2] == s[j - 1]);//1
					if (j >= 2)
					{
						dp[i][j] |= dp[i][j - 1] && (p[i - 2] == s[j - 1] || p[i - 2] == '.');//multi
					}
				}
				else
				{
					dp[i][j] = dp[i - 1][j - 1] && (p[i - 1] == '.' || p[i - 1] == s[j - 1]);
				}
			}
		}
		return dp[n][m];
	}
}t;
```

