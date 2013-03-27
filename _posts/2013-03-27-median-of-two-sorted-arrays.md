---
layout: post
title: "Median of Two Sorted Arrays"
description: "There are two sorted arrays A and B of size m and n respectively. Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n))"
categories:
- Algorithms
tags:
- leetcode
- interview
---
###1. 题目描述
这道题目是[leetcode](leetcode.com)上的一道经典题目，英文的题目见上面的描述。中文意思为有两个排好(升)序数组A和B，长度分别为m和n，请在O(log(m+n))的时间复杂度内找出两个数组合并之后的中位数。这里需要强调一下题目中要求的中位数在数组个数为偶数的情况下，需要返回中间两个数字的平均值。举个例子:

A[] = {1, 2, 3, 5}, m = 4

B[] = {4, 6, 7}, n = 3

则中位数为4

A[] = {1, 2, 3, 5}, m = 5

B[] = {4, 6, 7, 8}, n = 4

则中位数为 (4 + 5) / 2 = 4.5

###2.解题报告
我在google上搜索各种关于这道题目的解法，各种五花八门的解法都有，充斥了存在各种bug的代码，也有很多只是处理了m==n的特殊情况。最终在leetcode的讨论区找到一篇靠谱的[解题报告](http://leetcode.com/2011/03/median-of-two-sorted-arrays.html)。作者首先推荐了[MIT solution](http://www2.myoops.org/course_material/mit/NR/rdonlyres/Electrical-Engineering-and-Computer-Science/6-046JFall-2005/30C68118-E436-4FE3-8C79-6BAFBB07D935/0/ps9sol.pdf), 然后对其进行稍加修改得到了这道题目的非常巧妙的解法。我这里的内容没有任何创新，只是把我对这个精妙的算法的思考记录下来与大家分享。

前提假设数组下标从0开始,两个升序数组A[0...m-1], B[0...n-1]，合并排序之后的数组为C[0...m+n-1], 数组C不需要实际计算出来，这里只是为了讲解方便。C中排在中位数前面的数字为(m+n)/2个,实际上m+n为偶数时，上中位数的前面有(m+n)/2个数。假设median在A中的位置为i，也就是A中0...i-1共i个数字比median小。在C中排在median前面的数字总共有(m+n)/2个，也就是说在B中恰好有(m+n)/2 - i个数字比A[i]小。恰好的意思是如果令j=(m+n)/2-i-1,那么A[i] >= B[j] && A[i] <= B[j+1]。如果A[i] < B[j], 那么median在i的右边；如果A[i] > B[j+1],那么median在i的左边；这样如果使用二分的方法查找，就会省去一半的搜索空间，如果在A中找不到，就在B中找。算法的复杂度为O(log(m) + log(n)) = O(log(m+n))。

具体实现的代码如下
	class Solution {
	public:
		double FindMedianInternal(int a[], int b[], int na, int nb, int left, int right)
		{
			if(left > right)
			{
				return FindMedianInternal(b, a, nb, na, max(0, (na + nb) / 2 - na), min(nb - 1, (na + nb) / 2));
			}
			int i = (left + right) / 2;
			int j = (na + nb) / 2 - i - 1;
			if(j >= 0 && j < nb && a[i] < b[j])
				return FindMedianInternal(a, b, na, nb, i + 1, right);
			else if(j + 1 >= 0 && j + 1 < nb && a[i] > b[j + 1])
				return FindMedianInternal(a, b, na, nb, left, i - 1);
			else
			{
				if((na + nb) & 0x1)
					return a[i];
				else
				{
					if(i > 0)
						return (a[i] + max(a[i - 1], b[j])) / 2.0;
					else
						return (a[i] + b[j]) / 2.0;
				}
			}
		}
		double findMedianSortedArrays(int A[], int m, int B[], int n) {
			// Start typing your C/C++ solution below
			// DO NOT write int main() function
			return FindMedianInternal(A, B, m, n, max(0, (m + n) / 2 - n), min(m - 1, (m + n) / 2));
		}
	};
如果你把上面的代码直接放在leetcode中run会发现有一些测试用例没有通过，我仔细检查了下，发现leetcode上同一个测试用例的output竟然和我本地的output不一样！比如对于[], [2,3]这组测试用例本地的输出为2.5，而leetcode上竟然为10.0！如果有人知道为什么请一定留言告诉我。

如果你觉得看了上面的讲解就可以直接得出上面的code，那你一定是没有弄懂它的所有细节。代码的接口函数findMedianSortedArrays(int A[], int m, int B[], int n)只是一个helper函数，里面调用了真正的计算函数double FindMedianInternal(int A[], int B[], int m, int n, int left, int right), 这个函数维持的循环不变式是median要么在A[left...right]中，要么在B中。首先看初始调用的时候循环不变式是否成立：

left = max(0, (m+n)/2-n)

right = min(m - 1, (m+n)/2)

或许很奇怪，为什么不把left置为0, right置为m - 1呢，这不更自然，更容易理解吗，事实上这样是不行的。原因后面再讲，我们先看在这个条件下循环不变式是否成立。C中median排在(m+n)/2位置上，即比median小的数总共有(m+n)/2个数（再次强调数组从0开始！）。B中共有n个数，那么median至少要比A中的(m+n)/2 - n个数字大，也就是在A中median至少排在(m+n)/2 - n这个位置上，left至少为0，所以取max(0, (m+n)/2 -n),同理可以得出right的正确性。至此，循环不变式在初始调用成立。代码的第5-8行left > right，需要在B中寻找median，同样的方法计算left和right，保证了循环不变式的正确性。第11-12行A[i] < B[j], median在i的右侧，第13-14行A[i] > B[j+1]，median在i的左侧，都同样维持了循环不变式的正确性。第15-26行，A[i] >= B[j] && A[i] <= B[j+1], A[i]就是该数组的上中位数（因为i前面有(m+n)/2个数），如果m+n为奇数则返回A[i]，如果m+n为偶数则返回A[i]和下中位数的平均值。下中位数是A[i-1]和B[j]较大的那一个数字，如果i == 0的话，下中位数只能是B[j]。

下面我们来仔细理解代码中的11-15行中的三个if-else条件，前两个是显然的。但是进入15行的else之后为什么就一定是找到了median呢。万一在第10行计算出来的j的值为-2呢？这样可以导致代码运行到第15行的else处。j == -2 说明什么问题呢？说明i太靠后面了，只需要-1个B中的数字排在median前面就ok了，所以需要把i往前靠一些，也即是median在i的前面。同理，第15行的j如果计算出来的值为n, 说明i太靠前了，竟然需要n+1个B中的数字来填充到median的前面。无论j == -2 还是 j == n，都会使得代码进入第15行的else块中，而这两种情况下A[i]并不是所要求的median。

要解决这个问题，我们需要仔细理解<code class="code">恰好的意思是如果令j=(m+n)/2-i-1,那么A[i] >= B[j] && A[i] <= B[j+1]</code>。 事实上j可以等于-1或者n-1。j==-1的意思是，median排在A中的第i个位置，需要j+1=0个B中的数字来填充在它前面，也就是说meidian前面的i个数字刚刚好填充完median前面的位置不需要B中的数字了。而j==n-1的意思时候，B[j]已经是B的最后一个元素了，median只要大于等于它，i就是这个median了，不需要和B[j+1]比较了。

那么好了，我们只需要证明第10行j的范围在-1到n-1之间就可以了，而这个恰恰是由初始调用的

left = max(0, (m+n)/2-n)

right = min(m-1, (m+n)/2)

保证的。因为j = (m+n)/2 - i - 1, 而 (m+n)/2 -n <= i <= (m+n)/2, 所以 -1 <= j <= n - 1。

至此所以的细节讲完，希望可以讲清楚了。
