---
title: "Data Structures and Algorithms"
date: 2026-09-24
summary: "Data Structures and Algorithms Notes"
tags: ["dsa"]
---

## Fenwick Trees

Fenwick Trees are a data structure for computing a group operation on an array $A$ of size $N$. For simplicity let us assume addition of integers (which forms a group). Fenwick Trees allows us to:

 - for a function $f$ and a range $[l,r]$, calculate $A[l] + A[l+1] + \ldots + A[r]$ in $O(\log{N})$ time.
 - change the value of of an element $A[i]$ in $O(\log{N})$ time.

 This allows us to calculate the sum of any interval in an array in $O(\log{N})$ time, while allowing us to update values as we please.

Below is the complete implementation (in C++).

```cpp
class FenwickTree
{
public:
    FenwickTree(int n);
    FenwickTree(std::vector<int> const &d);
    int sum(int r);
    int sum(int l, int r);
    void add(int idx, int delta);

private:
    std::vector<int> bit;
};
```

```cpp
FenwickTree::FenwickTree(int n)
{
    for (int i = 0; i < n; ++i)
        bit.push_back(0);
}

FenwickTree::FenwickTree(std::vector<int> const &d)
{
    for (int i = 0; i < d.size(); ++i)
        bit.push_back(0);
    for (int i = 0; i < d.size(); ++i)
        add(i, d.at(i));
}

int FenwickTree::sum(int r)
{
    int res = 0;
    for (; r >= 0; r = (r & (r + 1)) - 1) {
        res += bit.at(r);
    }
    return res;
}

int FenwickTree::sum(int l, int r)
{
    return sum(r) - sum(l - 1);
}

void FenwickTree::add(int idx, int delta)
{
    for (; idx < bit.size(); idx = idx | (idx + 1))
        bit[idx] += delta;
}
```