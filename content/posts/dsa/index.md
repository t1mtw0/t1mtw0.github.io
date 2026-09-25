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

## Disjoint Set Union

The Disjoint Set Union (DSU) data structure consists of a set of elements, each in some set in which each set is djsoint from the other. Each set in this structure consists of an element called the parent which is distinguished and allows us to uniquely identify a set. The operations include:

 - ```make_set(v)```: make a set consisting of the single elment ```v```.
 - ```union_sets(a,b)```: Combine the sets that consist of the elements ```a``` and ```b```.
 - ```find_set(v)```: returns the parent of the set consisting of the element ```v```.

Each one of these operations can be accomplished in essentially $O(1)$ time (this is not exactly right, but we do not care about that here).

The idea is to construct a tree for each set where the root of the tree is the parent element. To combine sets we attach one of the trees to the other, choosing arbitrarily one of the parent elements to be the new combined parent. To find the parent of an element, we simply traverse the tree upwards until we reach the root.

The naive implementation is slow in the worst case (details will not be provided here). Thus we implement two optimizations: path compression and union by rank/size. Path compression relies on the following simple observation: when we traverse upwards when finding the parent, we also find the parent for all elements along the way. Thus, in path compression, we attach every element along the way directly to the parent. For union by rank/size, we rely on the again simple observation that we want to attach the "smaller" tree to the "larger" tree when combining. Thus we need a metric: two simple ones are size (number of elements in tree) or rank (depth of the tree). Both work equally well.

The full implementation in C++ is found below:

```cpp
struct Node
{
    int value;
    int rank;
    Node *parent;

    Node(int v) : value{v}, rank{0}, parent{nullptr} {};
};

void make_set(Node *v)
{
    v->parent = v;
    v->rank = 0;
}

Node *find_set(Node *v)
{
    if (v->parent == v)
        return v;
    return v->parent = find_set(v->parent);
}

void union_sets(Node *a, Node *b)
{
    a = find_set(a);
    b = find_set(b);
    if (a != b)
    {
        if (a->rank < b->rank)
        {
            auto temp = a;
            a = b;
            b = temp;
        }
        b->parent = a;
        if (b->rank == a->rank)
            a->rank++;
    }
}
```