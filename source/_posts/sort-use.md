---
title: 由字符串拼接看排序的应用
date: 2018.4.24
tag: algorithms
---

Given a collection of number segments, you are supposed to recover the smallest number from them. For example, given {32, 321, 3214, 0229, 87}, we can recover many numbers such like 32-321-3214-0229-87 or 0229-32-87-321-3214 with respect to different orders of combinations of these segments, and the smallest number is 0229-321-3214-32-87.

<!--more-->

显然为使得拼接后的数字最小，可以考虑将每个数字以字符串的方式读入，按字典序排序。

但这同时这会带来一个问题：具有相同前缀的字符串该如何确定先后次序以期达到总体最小。

为了解决这个问题，我们需要换一种思路：现在有两个数字{32, 321}，有两种拼接方式，即32-321和321-32，显然321-32为题目所要求的最小字符串。

因此我们只需更换排序方式即可解决具有相同前缀的字符串排序问题，判断（a + b）和（b + a）的字典序即可。

```c++
int comp(string a, string b) {
    return a + b < b + a;
}
```
