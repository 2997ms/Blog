---
title: Kickstart Practice Round 2017
date: 2017-03-03 14:25:28
tags: [Google]
categories: [题记]
---

又是好久没写blog了。。。这周末要做kickstart，拿一个practice round练一练，发现这三道题还是挺简单的，主要很想记录一下B题Vote的，这个题和spoj上的[INS14G - Kill them All](http://www.spoj.com/problems/INS14G/)是一样的思路，当初好不容易想明白，结果自己做的时候又有些忘记了。。。

> 好累啊，一年又一年，没有活成我想要的样子，没有去我想去的地方。

今年希望将自己的理想可以实现～

<!--more-->

### A.Country Leader

Problem

The Constitution of a certain country states that the leader is the person with the name containing the greatest number of different alphabet letters. (The country uses the uppercase English alphabet from A through Z.) For example, the name GOOGLE has four different alphabet letters: E, G, L, and O. The name APAC CODE JAM has eight different letters. If the country only consists of these 2 persons, APAC CODE JAM would be the leader.

If there is a tie, the person whose name comes earliest in alphabetical order is the leader.

Given a list of names of the citizens of the country, can you determine who the leader is?
Input

The first line of the input gives the number of test cases, T. T test cases follow. Each test case starts with a line with an interger N, the number of people in the country. Then N lines follow. The i-th line represents the name of the i-th person. Each name contains at most 20 characters and contains at least one alphabet letter.

Output

For each test case, output one line containing Case #x: y, where x is the test case number (starting from 1) and y is the name of the leader.
Limits

1 ≤ T ≤ 100.
1 ≤ N ≤ 100.
Small dataset

Each name consists of at most 20 characters and only consists of the uppercase English letters A through Z.

Large dataset

Each name consists of at most 20 characters and only consists of the uppercase English letters A through Z and ' '(space).
All names start and end with alphabet letters.
Sample

Input 
2
3
ADAM
BOB
JOHNSON
2
A AB C
DEF

Output 

Case #1: JOHNSON
Case #2: A AB C


In sample case #1, JOHNSON contains 5 different alphabet letters('H', 'J', 'N', 'O', 'S'), so he is the leader.

Sample case #2 would only appear in Large data set. The name DEF contains 3 different alphabet letters, the name A AB C also contains 3 different alphabet letters. A AB C comes alphabetically earlier so he is the leader.

> 题意就是找字符串中字符种类最多的那一个，如果同样多，那么选择字典序最少的那一个。

直接模拟即可。主要就是getline(cin,t)。

```c++
void solve()
{
	int n;
	sa(n);
	string res = "";
	string res_nxt = "";
	int cnt = 0;
	char tmp;
	scanf("%c", &tmp);
	repp(i, 1, n)
	{
		string t;
		getline(cin, t);
		map<char, int>hax;
		string nxt;
		rep(i, 0, t.length())
		{
			if (t[i] == ' ')continue;
			hax[t[i]]++;
			nxt.push_back(t[i]);
		}
		if (hax.size() > cnt)
		{
			cnt = hax.size();
			res = t;
			res_nxt = nxt;
		}
		else if (hax.size() == cnt)
		{
			res = min(res, t);
		}
	}
	cout << res << endl;
}

int main()
{
#ifndef ONLINE_JUDGE
	freopen("i.txt", "r", stdin);
	freopen("o.txt", "w", stdout);
#endif
	int t;
	sa(t);
	repp(i, 1, t)
	{
		printf("Case #%d: ", i);
		solve();
	}//system("pause");
	return 0;
}
```

### B.Vote

Problem

A and B are the only two candidates competing in a certain election. We know from polls that exactly N voters support A, and exactly M voters support B. We also know that N is greater than M, so A will win.

Voters will show up at the polling place one at a time, in an order chosen uniformly at random from all possible (N + M)! orders. After each voter casts their vote, the polling place worker will update the results and note which candidate (if any) is winning so far. (If the votes are tied, neither candidate is considered to be winning.)

What is the probability that A stays in the lead the entire time -- that is, that A will always be winning after every vote?

Input

The input starts with one line containing one integer T, which is the number of test cases. Each test case consists of one line with two integers N and M: the numbers of voters supporting A and B, respectively.

Output

For each test case, output one line containing Case #x: y, where x is the test case number (starting from 1) and y is the probability that A will always be winning after every vote.

y will be considered correct if y is within an absolute or relative error of 10-6 of the correct answer. See the FAQ for an explanation of what that means, and what formats of real numbers we accept.

Limits

1 ≤ T ≤ 100.
Small dataset

0 ≤ M < N ≤ 10.
Large dataset

0 ≤ M < N ≤ 2000.
Sample

Input 

2
2 1
1 0

Output 

Case #1: 0.33333333
Case #2: 1.00000000

In sample case #1, there are 3 voters. Two of them support A -- we will call them A1 and A2 -- and one of them supports B. They can come to vote in six possible orders: A1 A2 B, A2 A1 B, A1 B A2, A2 B A1, B A1 A2, B A2 A1. Only the first two of those orders guarantee that Candidate A is winning after every vote. (For example, if the order is A1 B A2, then Candidate A is winning after the first vote but tied after the second vote.) So the answer is 2/6 = 0.333333...

In sample case #2, there is only 1 voter, and that voter supports A. There is only one possible order of arrival, and A will be winning after the one and only vote.

> 题意是给出有n个1，m个0，要求这个字符串里面任意一个位置x，字符串[1..x]里面1都比0多，问有多少种方案。

和每日一题是一样的思路，你假设从[1,1]走到[n+m,n-m]，里面向上走n-1次，向下走m次，不能碰到x坐标轴，正常的方案是
$$
C\begin{pmatrix}
n+m-1\\
n-1
\end{pmatrix}
$$
然后减去不合法的方案数，就是相当于你这条走过的线碰到了x轴，那么以x轴为对称轴，将碰到x轴之前的那段路径做一个对称，会发现这部分不合法的方案就是从[1,-1]到[n+m,n-m]的方案数，这部分为
$$
C\begin{pmatrix}
n+m-1\\
n
\end{pmatrix}
$$
然后乘以$m!*n!$除以$(m+n)!$。

得到结果即是
$$
\frac{n-m}{n+m}
$$

```C++
int m, n;
void solve()
{
	sa(n), sa(m);
	printf("%.7lf\n", (1.0*n - m) / (n + m));
}

int main()
{
#ifndef ONLINE_JUDGE
	freopen("i.txt", "r", stdin);
	freopen("o.txt", "w", stdout);
#endif
	int t;
	sa(t);
	repp(i, 1, t)
	{
		printf("Case #%d: ", i);
		solve();
	}//system("pause");
	return 0;
}
```

### C. Sherlock and Parentheses

Problem

Sherlock and Watson have recently enrolled in a computer programming course. Today, the tutor taught them about the balanced parentheses problem. A string S consisting only of characters ( and/or ) is balanced if:
It is the empty string, or:
It has the form (S), where S is a balanced string, or:
It has the form S1S2, where S1 is a balanced string and S2 is a balanced string.

Sherlock coded up the solution very quickly and started bragging about how good he is, so Watson gave him a problem to test his knowledge. He asked Sherlock to generate a string S of L + R characters, in which there are a total of L left parentheses ( and a total of R right parentheses ). Moreover, the string must have as many different balanced non-empty substrings as possible. (Two substrings are considered different as long as they start or end at different indexes of the string, even if their content happens to be the same). Note that S itself does not have to be balanced.

Sherlock is sure that once he knows the maximum possible number of balanced non-empty substrings, he will be able to solve the problem. Can you help him find that maximum number?
Input

The first line of the input gives the number of test cases, T. T test cases follow. Each test case consists of one line with two integers: L and R.

Output

For each test case, output one line containing Case #x: y, where x is the test case number (starting from 1) and y is the answer, as described above.

Limits

1 ≤ T ≤ 100.
Small dataset

0 ≤ L ≤ 20.
0 ≤ R ≤ 20.
1 ≤ L + R ≤ 20.
Large dataset

0 ≤ L ≤ 105.
0 ≤ R ≤ 105.
1 ≤ L + R ≤ 105.
Sample


Input 
 3
1 0
1 1
3 2
Output 

Case #1: 0
Case #2: 1
Case #3: 3

In Case 1, the only possible string is (. There are no balanced non-empty substrings.
In Case 2, the optimal string is (). There is only one balanced non-empty substring: the entire string itself.
In Case 3, both strings ()()( and (()() give the same optimal answer.
For the case ()()(, for example, the three balanced substrings are () from indexes 1 to 2, () from indexes 3 to 4, and ()() from indexes 1 to 4.

> 题意是给出左括号与右括号的数量，问最多可以组成多少合法括号的子串。

求出左括号与右括号的值n，然后发现就是()()()…这样的，那即是
$$
\frac{n*(n+1)}{2}
$$

```C++
ll m, n;
void solve()
{
	cin >> n >> m;
	ll minn = min(n, m);
	ll res = (minn + 1)*minn / 2;
	cout << res << endl;
}

int main()
{
#ifndef ONLINE_JUDGE
	freopen("i.txt", "r", stdin);
	freopen("o.txt", "w", stdout);
#endif
	int t;
	sa(t);
	repp(i, 1, t)
	{
		printf("Case #%d: ", i);
		solve();
	}
  	//system("pause");
	return 0;
}
```



