---
layout: post
title: "BinarySearch"
description: "二分搜索是经典的在有序数组中log(n)时间复杂度的搜索算法。本文回顾了经典二分搜索算法，并增加了查找最左边，和最右边位置的算法"
categories: 
- Algorithms
tags:
- BinarySearch
---
###1. 二分搜索问题(binary search)
给定一个有序数组和一个数字，请找出该数字在数组中出现的位置，如果没有出现返回-1。

举个例子:

a[] = {1,3,7,8,9}

如果给定value=7,则返回2;如果给定value=10, 则返回-1。

算法如下:
    int BinarySearch(int a[], int size, int value)
    {
        int left = 0;
        int right = size - 1;
        while(left <= right)
        {
            int mid = (left + right) / 2;
            if(a[mid] == value)
              return mid;
            else if(a[mid] > value)
              left = mid + 1;
            else
              right = mid -1;
        }
        return -1;
    }
###2. 二分最左搜索问题(binary search leftmost)
给定一个有序数组和一个数字，请找出该数字在数组中出现的最左/小位置，如果没有出现返回-1。
举个例子:

a[] = {1,2,3,3,3,4,5}

如果给定value=3,则返回2。如果给定value=7,则返回-1。

算法如下:
    int BinarySearchLeftmost(int a[], int size, int value)
    {
        if(size <= 0)
          return -1;
        int left = 0;
        int right = size - 1;
        while(left < right)
        {
            int mid = (left + right) / 2;
            if(a[mid] < value)
              left = mid + 1;
            else
              right = mid;
        }
        if(a[left] == value)
          return left;
        return -1;
    }
###3. 二分最右搜索问题(binary search rightmost)
给定一个有序数组和一个数字，请找出该数字在数组中出现的最右/大位置，如果没有出现返回-1。
举个例子:

a[] = {1,2,3,3,3,4,5}

如果给定value=3,则返回4。如果给定value=7,则返回-1。

算法如下:
    int BinarySearchRightmost(int a[], int size, int value)
    {
        if(size <= 0)
          return -1;
        int left = 0;
        int right = size - 1;
        while(left < right)
        {
            int mid = (left + right + 1) / 2;
            if(a[mid] > value)
              right = mid - 1;
            else
              left = mid;
        }
        if(a[left] == value)
          return left;
        return -1;
    }
注意在计算mid的时候<code class="code">mid = (left + right + 1)</code>，是为了防止无限循环。
