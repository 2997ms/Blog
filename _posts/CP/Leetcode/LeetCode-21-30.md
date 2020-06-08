---
title: LeetCode 21-30
date: 2016-10-07 22:27:24
tags: [LeetCode, 链表, DP, KMP]
categories: [题记]
---

这一期10道题好难啊。。。好多要记的。

<!--more-->

### 21 Merge Two Sorted Lists

> 合并两个链表，注意为空的情况。

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
	ListNode* mergeTwoLists(ListNode* l1, ListNode* l2)
	{
		ListNode node(INT_MIN);
		ListNode *head = &node;

		while (l1&&l2)
		{
			if (l1->val < l2->val)
			{
				head->next = l1;
				l1 = l1->next;
			}
			else
			{
				head->next = l2;
				l2 = l2->next;
			}
			head = head->next;
		}
		head->next = l1 ? l1 : l2;
		return node.next;
	}
};
```



### 22 Generate Parentheses

> 产生长度为$2\*n$的合法括号字符串。

这种题递归求解很容易理解，其实也可以非递归写的，这里有[DP求解](https://discuss.leetcode.com/topic/18523/a-simplified-c-dp-solution-4ms-8-lines)的。思路就是$dp[x\]$表示由x个括号组成的所有字符串，那推倒$x+1$就是在前面的长度两个加一起长度为$x$的合在一起就好了。

```C++
void gen(vector<string>&res, int le, int ri, string cur)
{
	if (le == 0 && ri == 0)
	{
		res.push_back(cur);
		return;
	}
	if (le > 0)
	{
		gen(res, le - 1, ri, cur + '(');
	}
	if (ri > le)
	{
		gen(res, le, ri - 1, cur + ')');
	}
}

class Solution 
{
public:
	vector<string> generateParenthesis(int n)
	{
		vector<string>res;
		gen(res, n, n, "");
		return res;
	}
};
```



### 23 Merge k Sorted Lists

> 把k的链表合在一起。

归并排序的思想。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2)
{
	ListNode node(INT_MIN);
	ListNode *head = &node;

	while (l1&&l2)
	{
		if (l1->val < l2->val)
		{
			head->next = l1;
			l1 = l1->next;
		}
		else
		{
			head->next = l2;
			l2 = l2->next;
		}
		head = head->next;
	}
	head->next = l1 ? l1 : l2;
	return node.next;
}

class Solution 
{
public:
	ListNode* mergeKLists(vector<ListNode*>& lists) 
	{
		int sz = lists.size();
		if (sz == 0)
		{
			return NULL;
		}
		else if (sz == 1)
		{
			return lists[0];
		}
		vector<ListNode*>left(lists.begin(), lists.begin() + sz / 2);
		vector<ListNode*>right(lists.begin() + sz / 2, lists.end());
		return mergeTwoLists(mergeKLists(left), mergeKLists(right));
	}
};
```



### 24 Swap Nodes in Pairs

> 交换链表中相邻的两个节点。

后面相邻k个的简单版。(链表的题目永远不要嫌多。。。)

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
    ListNode* swapPairs(ListNode* head) 
    {
    	ListNode no = ListNode(0);
    	no.next = head;
    	ListNode *pre=&no;

    	while(head&&head->next)
    	{
    		ListNode *tmp=head->next->next;

    		pre->next=head->next;
    		head->next->next=head;
    		head->next=tmp;

    		pre = head;
    		head = tmp;
    	}

    	return no.next;   
    }
};
```



### ********25 Reverse Nodes in k-Group

> 对链表，旋转相邻k个节点。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
	ListNode* reverseKGroup(ListNode* head, int k)
	{
		if (head == NULL || k == 1)return head;
		ListNode *res = new ListNode(-1);
		res->next = head;
		ListNode *pre = res;
		ListNode *cur = res;
		ListNode *nxt = res;

		int num = 0;
		while (cur->next)
		{
			cur = cur->next;
			num++;
		}
		while (num >= k)
		{
			cur = pre->next;
			nxt = cur->next;

			for (int i = 1; i < k; i++)
			{
				cur->next = nxt->next;
				nxt->next = pre->next;
				pre->next = nxt;
				nxt = cur->next;
			}
			num -= k;
			pre = cur;
		}
		return res->next;
	}
};
```



### 27 Remove Element

> 要求在数组内删除值为val的元素。不要另开空间。

我的做法还是麻烦了很多，这个人的[解法](https://discuss.leetcode.com/topic/61161/c-solution-0-ms)是0ms的。

```C++
class Solution
{
public:
	int removeElement(vector<int>& nums, int val)
	{
		int sz = nums.size();
		if (sz == 0)
		{
			return 0;
		}
		int idx = sz - 1;
		for (int i = sz - 1; i >= 0; i--)
		{
			if (nums[i] == val)
			{
				swap(nums[i], nums[idx]);
				idx--;
				sz--;
			}
		}
		nums.resize(sz);
		return sz;
	}
};
```



### 28  Implement strStr()

> 返回子串在原串中出现的位置。

裸kmp。

```C++
void getnext(string x, vector<int>&nxt)
{
	int k = -1;
	int len = x.length();
	nxt[0] = -1;
	for (int i = 0; i < len; )
	{
		if (k == -1 || x[k] == x[i])
		{
			k++;
			i++;
			nxt[i] = k;
		}
		else
		{
			k = nxt[k];
		}
	}
}

class Solution 
{
public:
	int strStr(string haystack, string needle) 
	{
		int len1 = haystack.length();
		int len2 = needle.length();
		
		vector<int>nxt(needle.length() + 1, 0);
		getnext(needle, nxt);

		int i = 0, j = 0;
		while (i < len1 && j < len2)
		{
			if (j == -1 || haystack[i] == needle[j])
			{
				i++;
				j++;
			}
			else
			{
				j = nxt[j];
			}
		}
		if (j == len2)
		{
			return i - len2;
		}
		else
		{
			return -1;
		}
	}
};
```



### 29 Divide Two Integers

> 算两数相除，不能使用乘除模操作。

使用移位操作，化成二进制。

```C++
long long cal(long long dividend, long long divisor)
{
	long long res = 0;
	long long d = abs(dividend);
	long long t = abs(divisor);

	while (d >= t)
	{
		long long cnt = 0;
		while ((t << cnt) <= d)
		{
			cnt++;
		}
		cnt--;
		d -= (t << cnt);
		res += (1LL << cnt);
	}
	int s1 = dividend < 0;
	int s2 = divisor < 0;
	if (s1^s2)
	{
		return -res;
	}
	else
	{
		return res;
	}
}

class Solution 
{
public:
	int divide(int dividend, int divisor) 
	{
		int s1 = dividend < 0;
		int s2 = divisor < 0;
		if (s1^s2)
		{
			if (abs(dividend) == INT_MIN&&abs(divisor) == 1)
				return INT_MIN;
		}
		else
		{
			if (abs(dividend) == INT_MIN&&abs(divisor) == 1)
				return INT_MAX;
		}
		long long res = cal(dividend, divisor);
		if (res > INT_MAX || res < INT_MIN)
		{
			return INT_MAX;
		}
		else
		{
			return res;
		}
	}
};
```



### 30 Substring with Concatenation of All Words

> 给出一个字符串集合，然后给出了原串，要求给出在原串里所有字符串集合中都出现一次的位置。

记录一个map，算字符串出现的次数，然后每一个位置去对照，看是否超出或者没有找到。

耗时好多啊，[这里](https://msreekan.com/2016/06/27/substring-with-concatenation-of-all-words/)有一篇使用trie的。看完之后回来改进一下。

```C++
class Solution
{
public:
	vector<int> findSubstring(string s, vector<string>& words) 
	{
		map<string, int>num;
		int sz = words.size();
		int len = 0;
		vector<int>res;
		if (sz == 0)return res;
		int one = words[0].length();
		for (int i = 0; i < sz; i++)
		{
			num[words[i]]++;
			len += words[i].length();
		}
		int lens = s.length();
		for (int i = 0; i + len <= lens; i++)
		{
			map<string, int>tmp;
			int f = 0;
			for (int k = 0; k < sz; k++)
			{
				string t = s.substr(i + k*one, one);
				if (num.find(t) != num.end())
				{
					tmp[t]++;
					if (tmp[t] > num[t])
					{
						f = 1;
						break;
					}
				}
				else
				{
					f = 1;
					break;
				}
			}
			if (f == 0)
			{
				res.push_back(i);
			}
		}
		return res;
	}
};
```