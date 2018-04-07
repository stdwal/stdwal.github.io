---
title: 寻找两个有序数列的中间值问题
date: 2018.4.4
tags: algorithms
---

以升序给定两个数列，求两个数列中的中间值问题。

<!--more-->

例如：

11 12 13 14
9 10 15 16 17

这两个序列，它们的中间值为13.

最常见的做法是将这两个序列放入同一个数组中排序，然后输出第(a+b)/2个元素.

但即使是O(logn)的快排在大规模数据里也难以在一秒内返回答案，我们需要利用数组有序的特点制定一种O(logn)的算法策略。

我们把这个中值问题扩展到寻找第K小的值问题，等价于在两个数组中挑选出最小的K个元素。由于给定的数组已经按从小到大排序，通过比较两个数组各自的第K/2个元素可以将问题规模减小K/2。

*注：假设a[i]为a中第i个元素。*

定义函数
```
findkth(int a[], int b[], int k)
```
表示寻找到并返回数组a，b中第K小的元素。


```c
if (a[k/2] < b[k/2]) {
        return findkth(a + k / 2, b, k - k / 2);
} else if (a[k/2] == b[k/2]) {
        return a[k/2];
} else {
        return findkth(a, b + k / 2, k - k / 2);
}
```
第一种情况是a的第k/2个元素小于b的第k/2个元素，那么将a中的前k/2个元素挑选出来，并在剩下的数组a，b中挑选出后k/2个元素。

如果数组a，b各自的第k/2个元素相等，则将a中前k/2个元素挑出，b中前k/2个元素也挑出，这样第k小的元素即是a[k/2]（或b[k/2]）。

再考虑基本情况：
* 当数组a为空时，b[k]为第k小的元素。

* 当k为1时选择数组a，b中最小的一个，即min(a[0], b[0])。

```c
int findkth(int a[], int b[], int n1, int n2, int k) {
    if (n1 > n2) {
        return findkth(b, a, n2, n1, k);
    }
    if (n1 == 0) {
        return b[k-1];
    }
    if (k == 1) {
        return min(a[0], b[0]);
    }
    int ath = min(k / 2, n1);
    int bth = k - ath;
    if (a[ath-1] < b[bth-1]) {
        return findkth(a + ath, b, n1 - ath, n2, k - ath);
    } else if (a[ath-1] == b[bth-1]) {
        return a[ath-1];
    } else {
        return findkth(a, b + bth, n1, n2 - bth, k - bth);
    }
}
```
