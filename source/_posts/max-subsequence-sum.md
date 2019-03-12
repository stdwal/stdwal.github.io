---
title: 关于分治法求最大子序列和问题
date: 2019.3.10
tags: algorithms
---

求解最大子序列和问题有大致四个算法，效率分别为O(n^3)、O(n^2)、O(logn)和O(n)。

显然O(n)是最完美的线性联机算法，但这里主要讨论使用分治法递归求解。

<!--more-->

分治策略是将问题分成两个大致相等的子问题，然后递归地对它们求解。因此我们可将整个序列分成两部分求解。

例如序列： 4 -3 5 -2 -1 2 6 -2

|前半部分 |后半部分|
|-----|------|
|4 -3 5 -2| -1 2 6 -2|

最大子序列的和可能在三处位置出现：或者整个出现在输入数据的左半边，或者整个数据的右半边，或者跨越输入数据的中部从而占据左右两半部分。

因此三者中的最大值即为最大子序列和。在本例中，前半部分的最大子序列和为6，后半部分的子序列和为8，而跨越两个部分的最大子序列和为11，因此该例的答案为11.

```c
int MaxSubSum(int A[], int left, int right) {
    int MaxLeftSum, MaxRightSum;
    int MaxLeftBorderSum, MaxRightBorderSum;
    int LeftBorderSum, RightBoarderSum;
    int Center, i;

    if (left == right) {
        if (A[left] > 0) {
            return A[left];
        } else {
            return 0;
        }
    }
    Center = (left + right) / 2;
    MaxLeftSum = MaxSubSum(A, left, Center);
    MaxRightSum = MaxSubSum(A, Center + 1, right);

    MaxLeftBorderSum = LeftBorderSum = 0;
    for (i = Center; i >= left; i--) {
        LeftBorderSum += A[i];
        if (LeftBorderSum > MaxLeftBorderSum) {
            MaxLeftBorderSum = LeftBorderSum;
        }
    }
    MaxRightBorderSum = RightBoarderSum = 0;
    for (i = Center + 1; i <= right; i++) {
        RightBoarderSum += A[i];
        if (RightBoarderSum > MaxRightBorderSum) {
            MaxRightBorderSum = RightBoarderSum;
        }
    }
    return Max3(MaxLeftSum, MaxRightSum,
         MaxLeftBorderSum + MaxRightBorderSum);
}

```

代码第7-13行处理基准情况，即当序列中只有一个元素时，如果该元素大于0，则该元素的值就是最大子序列之和，否则答案为0。

代码第18-31行分别处理左右两部分的最大子序列和问题。

最后将三者的最大值返回即可。

----
参考文献：《数据结构与算法分析（C语言描述）》
