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

LCS有O(n<sup>2</sup>)的动态规划解法，故而可以解之。

###3. 动态规划
动态规划一开始要定义问题，然后由子问题的最优解构造规模更大的问题的解。问题定义很重要，我一开始想到的是f(i)定义为S[0]...S[i]的LIS长度,
如果S[i]比f(i-1)的LIS最后一个元素还大，那么f(i)=f(i-1) + 1，否则f(i) = f(i-1)。但是这样必须要求我们存储f(i-1)的所有LIS,非常不好实现。

如果把f(i)定义为以S[i]结尾的LIS长度,由于LIS(S)必定以S[0],S[1]...S[size(S)-1]中的某个元素结尾，所以LIS(S)=max(f(0),f(1),...,f(n-1))。
递归方程为:

<div align="center">f(i)=max(f(j) + 1) for all j&lt;i&&S[j]&lt;S[i]</div>

算法如下:
    int LIS(int a[], int size)
    {
        if(size <= 0)
          return 0;
        int* solutions = new int[size];
        solutions[0] = 1;
        int maxSol = solutions[0];
        for(int i = 1; i < size; ++i)
        {
            solutions[i] = 0;
            for(int j = 0; j < i; ++j)
            {
                if(a[i] > a[j])
                  solutions[i] = max(solutions[i], solutions[j] + 1)
            }
            maxSol = max(maxSol, solutions[i]);
        }
        delete []solutions;
        return maxSol;
    }
很容易看出，该算法的时间复杂度为O(n<sup>2</sup>)
###3. O(nlog(n))的算法
令S<sub>i</sub>={S[0],S[1],...S[i]},R<sub>i,j</sub>为S<sub>i</sub>中长度
为j的上升子序列集合。令m<sub>i,j</sub>表示R<sub>i,j</sub>中每个序列最后一个元素
组成的集合中最小的那个元素。

观察一:
m<sub>i,1</sub>&lt;=m<sub>i,2</sub>&lt;=...&lt;=m<sub>i,j</sub>

因此，如果我们想找以S[i+1]结尾的最长递增子序列，则只要找到最大的k，使得m<sub>i,k</sub>&lt;=S[i+1]，这样S[i+1]并在m<sub>i,k</sub>后面构成了长度为k+1的上升子序列,并且由于k的最大性使得m<sub>i+1,k+1</sub>=S[i+1]。对于这个搜索过程，利用上述观察一，可以使用二分法搜索 (binary search)。

观察二:
m<sub>i+1,k+1</sub> = S[i+1] , 且对于所有t不等于k+1, m<sub>i+1,t</sub> = m<sub>i,t</sub>。

上面的描述参考了[这里](http://blog.csdn.net/linulysses/article/details/5559262)。
实现算法时，如果最后还要返回一个最长上升序列的话，m<sub>i,j</sub>可以用它在S中的下标来表示，并用一个额外的数组P来记录最长上升序列的下标。
    int LIS(int a[], int size)
    {
        if(size <= 0)
          return 0;
        int* m = new int[size + 1];
        int* p = new int[size + 1];
        m[1] = 0;
        int L = 1;
        for(int i = 1; i < size; ++i)
        {
            //binary search for the largest positive j<=L
            //such that a[m[j]] < a[i] (or set j = 0 if no such value exists)
            int j = BinarySearchLeftMost(m, 1, L, a[i]);
            P[i] = j;
            if(j == L || a[i] < a[m[j+1]])
            {
                m[j+1] = i;
                L = max(L, j+1);
            }
        }
        //print reverse of(a[m[L]], a[p[m[L]]],...) for an instance of LIS
        delete []m;
        delete []p;
        return L;
    }

    int BinarySearchLeftmost(int a[], int left, int right, int value)
    {
        while(left < right)
        {
            int mid = (left + right) / 2;
            if(a[mid] < value)
              left = mid + 1;
            else
              right = mid;
        }
        if(a[left] >= value)
            return left - 1;
        return left;
    }
这段算法参考于[Wikipedia](http://en.wikipedia.org/wiki/Longest\_increasing\_subsequence),
我自己不是很懂在LIS中为什么要加入<code class="code">if(j==L || a[i] < a[m[j+1]])</code>,觉得
去了也是可以的。数组m和p都是从1开始算起，这样显得直观一些。

