---
title: "Count of Smaller Nums After Self"
date: 2020-07-14T20:57:55+08:00
draft: false
categories:
 - Algorithm
tags:
 - Leetcode
 - BST
---

I use modified-BST to solve this problem. Each time inserting a new node takes `O(logn)` and also updates the value of count, and there are n nodes; therefore, I think the time complexity of my solution is `O(nlogn)`.

## Writeup
{{< youtube jpGzPwd0YqQ >}}

The programming language in video is C++

For the source code in video: [Github](https://github.com/shinmao/algorithm/blob/main/leetcode/leetcode315.cpp)

## Conclusion
* Order of input: left to right or right to left?
* Definition of node: what member should we define for each nodes in tree?
* Two condition to be considered for this problem: whether there are left childs of root node or not?