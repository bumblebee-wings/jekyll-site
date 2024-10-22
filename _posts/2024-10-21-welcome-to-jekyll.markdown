---
layout: post
title:  "Segment Tree"
date:   2024-10-22
categories: "data structures"
---

## Definition

Segment tree is a data structure that stores information of array intervals as a tree. You can understand what it means with the picture below:

It is a sum segment tree where each parent is the sum of two siblings.
What can we do with it?
As a representative example, we can compute the sum of the particular query of an array $a[l...r]$ and update queries with the segment tree in $O(\log n)$.

I will introduce other operations later.

## Implementation of Build

We should transform a common array into a segment tree first. We only use a simple array, not a structure. How?
Let 1-indexed array $a[]$ with the parameter $v$ be the current index, and the current interval be $tl$ to $tr$.
We can use recursion to go down from parent to children, and if it reaches a unit of size 1 ($tl == tr$), assign $t[v] \leftarrow a[tl]$. Since the index of the child is $(left = parent \cdot 2, right = parent \cdot 2 + 1)$, their parents should be assigned by the sum of two children ($t[v] = t[2 \cdot v] + t[2 \cdot v+1]$).
Start the function with $v = 1$ (root), $tl = 1$  and $tr = n$ (where $n$ is the number of elements):

```c++
int n, t[4*MAXN];
void build(int a[], int v, int tl, int tr) {
    if (tl == tr) { // if the size of the interval is 1
        t[v] = a[tl];
    } else {
        int tm = (tl + tr) / 2;
        build(a, v*2, tl, tm); // go down to the left child
        build(a, v*2+1, tm+1, tr); // go down to the right child
        t[v] = t[v*2] + t[v*2+1]; // after finishing operations of left and right children, compute current vertex = leftchild + rightchild
    }
}

```

### Implementation of Sum

We also define the current interval ($tl$ to $tr$). We should divide the current interval into $tl$ to $tm$ and $tm + 1$ to $tr$ ($tm = \frac{tl + tr}{2}$) with recursions until the current interval is included in $l$ to $r$. 

In the CP-algorithms article:

```c++
int sum(int v, int tl, int tr, int l, int r) {
    if (l > r)
        return 0;
    if (l == tl && r == tr) {
        return t[v];
    }
    int tm = (tl + tr) / 2;
    return sum(v*2, tl, tm, l, min(r, tm))
           + sum(v*2+1, tm+1, tr, max(l, tm+1), r);
}

```

In my case, instead of dividing $l$ and $r$, I think it can be alright to fix the if statement with **if(l > tr || r < tl)**

```c++
int sum(int v, int tl, int tr, int l, int r) {
    if (l > tr || r < tl) // when the current interval is completely out of range
        return 0;
    if (l <= tl && r >= tr) {
        return t[v];
    }
    int tm = (tl + tr) / 2;
    return sum(v*2, tl, tm, l, r)
           + sum(v*2+1, tm+1, tr, l, r);
}

```

.