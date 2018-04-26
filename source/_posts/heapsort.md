---
title: 堆排序
date: 2018.4.26
tag: algorithms
---

堆排序（Heapsort）是指利用堆这种数据结构所设计的一种排序算法。

堆积是一个近似完全二叉树的结构，并同时满足堆积的性质：即子结点的键值或索引总是小于（或者大于）它的父节点。

<!--more-->

我们可以使用一个附加数组建立二叉堆，执行数次的DeleteMin操作，将元素记录到第二个数组然后将数组拷贝回来，得到N个元素的排序。

避免使用第二个数组的聪明的做法是利用这样的事实：在每次DeleteMin之后，堆缩小了1。因此，位于堆中最后的单元可以用来存放刚刚删去的元素。

使用这种策略，在最后一个DeleteMin后，该数组将以递减的顺序包含这些元素。如果我们想要这些元素排成更典型的递增顺序，我们可以改变序的特性使得父亲的关键字的值大于儿子的关键字的值，即max堆。

*数组从0开始计数*

```c
#define LeftChild(i) (2 * (i) + 1)

void PerDown(ElementType A[i], int i, int N) {
        int Child;
        ElementType Tmp;
        for (Tmp = A[i]; LeftChild(i) < N; i = Child) {
                Child = LeftChild(i);
                if (Child != N - 1 && A[Child-1] > A[Child])
                        Child++;
                if (Tmp < A[Child])
                        A[i] = A[Child];
                else
                        break;
        }
        A[i] = Tmp;
}

void Heapsort(ElementType A[], int N) {
        for (int i = N / 2; i >= 0; i--) {
                PerDown(A, i, N);
        }
        for (int i = N - 1; i > 0; i--) {
                Swap(&A[0], &A[i]);
                PerDown(A, 0, i);
        }
}
```

PerDown函数用于判断当前元素在堆中是否满足堆序性质，即判断当前元素是否大于它的儿子。

在Heapsort函数中第一个for循环用于建立一个堆（自二叉堆的倒数第二层开始自底向上判断堆序性质），然后将堆顶的元素与最后的元素互换并重建堆，以此得到一个有序数组。

---
参考：
数据结构与算法分析（C语言描述）
