---
layout: post
title: "Kth Element of Two Sorted Arrays"
description: "Given two sorted arrays A, B of size m and n respectively. Find the k-th smallest element in the union of A and B. You can assume that there are no duplicate elements"
categories: 
- Algorithms
tags:
- leetcode
- interview
---
###1. 题目描述
这道题目也是[leetcode](http://leetcode.com/2011/01/find-k-th-smallest-element-in-union-of.html)上的一道题目，不过没有online judege。题目中文意思为有两个排好(升)序的数组A和B，长度分别为m和n，请找出A和B合并之后的数组中第k大的数字。你可以假设A和B中没有重复出现的数字。举个例子：

A[] = {1, 3, 5, 7, 9}, m = 5

B[] = {2, 4, 6, 8}, n = 4

则第1大的数为1, 第9大的数为9。

根据上篇文章[median of two sorted arrays](http://zixiaojindao.github.com/Algorithms/2013/03/27/median-of-two-sorted-arrays/)， 很容易写出下面的代码：
	class Solution {
	public:
		double FindKthInternal(int a[], int b[], int na, int nb, int left, int right, int k)
		{
			if(left > right)
				return FindKthInternal(b, a, nb, na, max(0, k - 1 - na), min(nb, k - 1), k);
			int i = (left + right) / 2;
			int j = k - 1 - i - 1;
			if(j >= 0 && j < nb && a[i] < b[j])
				return FindKthInternal(a, b, na, nb, i + 1, right, k);
			else if(j + 1 >= 0 && j + 1 < nb && a[i] > b[j+1])
				return FindKthInternal(a, b, na, nb, left, i - 1, k);
			else
				return a[i];
		}
		double findKthSortedArrays(int A[], int m, int B[], int n, int k) {
			// Start typing your C/C++ solution below
			// DO NOT write int main() function
			return FindKthInternal(A, B, m, n, max(0, k - 1 - n), min(m - 1, k - 1), k);
		}
	};
median of two sorted arrars中我们实际上找的是第(m+n)/2 + 1大的数字，然后顺带着把第(m+n)/2大的数字找出来了而已。
	

