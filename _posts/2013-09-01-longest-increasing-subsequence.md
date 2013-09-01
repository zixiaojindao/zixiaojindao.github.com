---
layout: post
title: "Longest Increasing Subsequence"
description: "Longest Increasing Subsequnce(最长上升子序列/LIS)的解法有很多种，本文回顾了几种常见的解法，以及目前最优算法O(nlog(n))时间复杂度的算法。"
categories: 
- Algorithms
tags:
- LIS 
---
###1. 问题描述
给定一个无序数组，请找出该数组中最长的上升子序列的长度。上升表示是升序，子序列不要求连续，但下标必须是升序。

举个例子:

a[] = {0, 8, 4, 12, 2, 10, 6, 14, 1, 9, 5, 13, 3, 11, 7, 15}

那么{0, 2, 6, 9, 13, 15}是它的一个最长上升子序列。{0, 4, 6, 9, 11, 15}也是它的一个最长上升子序列。所以最长上升序列并不唯一。

###2. 规约为Longest Common Subsequences(最长公共子序列/LCS)
对于序列S，LIS(S) = LCS(S, Sort(S))。
令T = LCS(S, Sort(S))，T显然是S的子序列，并且由于T是Sort(S)的子序列，所以T是S的上升子序列。如果有一个更长的T' = LIS(S), 那么Len(T') > Len(T),矛盾。所以T是S的LIS。

LCS有O(n^2)的动态规划解法，故而可以解之。
