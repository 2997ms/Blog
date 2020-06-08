---
title: LeetCode 11-20
date: 2016-09-23 10:31:39
tags: [LeetCode]
categories: [题记]
---



### LeetCode11 Container With Most Water

> 题意是给出$N$个整数$a\_1,a\_2...a_n$，代表在对应位置有一条$(i,0)$到$(i,a\_i)$的边。问这里面两条边加x轴所围成的面积最大值。

之后也有一道类似的题，左右贪心，每次将较小的边去掉，时间复杂度$O(n)$。

```C++
class Solution 
{
public:
    int maxArea(vector<int>& height) 
    {
        int sz=height.size();
        if(sz==0||sz==1)return 0;

        int le=0,ri=sz-1;
        int res=0;
        int weight=sz-1;
        while(le<ri)
        {
        	res=max(res,min(height[le],height[ri])*weight);
        	int h1=height[le];
        	int h2=height[ri];
        	if(h1<h2)
        	{
        		le++;
        	}
        	else
        	{
        		ri--;
        	}
        	weight--;
        }
        return res;
    }
}t;
```

### LeetCode12 Integer to Roman

> 题意是将整形数字转成罗马数字(字符串)。

考虑每一位，0到3是一种，4是一种，5到8是一种，9是一种。

```c++
class Solution
{
public:
	string intToRoman(int num)
	{
		int n[4] = { 1000,100,10,1 };
		char r5[4] = { ' ','D','L','V' };
		char r1[4] = { 'M','C','X','I' };

		string res = "";
		for (int i = 0; i < 4; i++)
		{
			int k = num / n[i];

			if (k == 9)
			{
				res = res + r1[i] + r1[i - 1];
			}
			else if (k == 4)
			{
				res = res + r1[i] + r5[i];
			}
			else if (k >= 5 && k <= 8)
			{
				res = res + r5[i];
				for (int j = 0; j < k - 5; j++)
				{
					res = res + r1[i];
				}
			}
			else
			{
				for (int j = 0; j < k; j++)
				{
					res = res + r1[i];
				}
			}
			num = num%n[i];
		}
		return res;
	}
};
```

### LeetCode19 Remove Nth Node From End of List

> 移除链表结尾的第n个节点。

一前一后距离为n-1，当后一个节点到了结尾的时候，那么前一个节点就是要删除的节点。

```c++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n)
    {
    	if(head==NULL)
    	{
    		return NULL;
    	}
    	ListNode *pre = NULL;
    	ListNode *p = head;
    	ListNode *q = head;
    	ListNode *res = head;
    	for(int i=0;i<n-1;i++)
    	{
    		q=q->next;
    	}

    	while(q->next)
    	{
    		pre=p;
    		p=p->next;
    		q=q->next;
    	}
    	if(pre)
    	{
    	     pre->next=p->next;   
    	}
    	else
    	{
    	     res=head->next;
    	}
    	return res;
	}
};
```