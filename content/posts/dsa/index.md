---
title: "Data Structures and Algorithms"
date: 2026-10-07
summary: "Data Structures and Algorithms Notes"
tags: ["DSA"]
---

## Fenwick Trees

Fenwick Trees are a data structure for computing a group operation on an array $A$ of size $N$. For simplicity let us assume addition of integers (which forms a group). Fenwick Trees allows us to:

 - for a function $f$ and a range $[l,r]$, calculate $A[l] + A[l+1] + \ldots + A[r]$ in $O(\log{N})$ time.
 - change the value of of an element $A[i]$ in $O(\log{N})$ time.

 This allows us to calculate the sum of any interval in an array in $O(\log{N})$ time, while allowing us to update values as we please.

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

```cpp
class DSU {
private:
    int N;
    std::vector<int> rep;
    std::vector<int> sz;
public:
    DSU(int n) {
        N = n;
        for (int i = 0; i < n; ++i) {
            sz.push_back(1);
            rep.push_back(i);
        }
    }
    int fnd(int a) {
        if (rep[a] == a)
            return a;
        return rep[a] = fnd(rep[a]);
    }
    bool dounion(int a, int b) {
        a = find(a);
        b = find(b);
        if (a == b)
            return false;
        if (sz[a] > sz[b]) {
            rep[b] = rep[a];
            sz[a] += sz[b];
        } else {
            rep[a] = rep[b];
            sz[b] += sz[a];
        }
        N--;
        return true;
    }
}
```

## Manacher's algorithm

[Manacher's algorithm](https://en.wikipedia.org/wiki/Longest_palindromic_substring#Manacher's_algorithm) is used to solve the [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/description/) problem in $O(n)$ time. The traditional DP solution is $O(n^2)$, but by being clever we can actually achieve $O(n)$.

## Breadth-First Search

Breadth-first search is a graph traversal method that explores closer vertices first. A simple implementation is below:

```cpp
void bfs(std::vector<std::vector<int>>& adj, int s, int n) {
    std::vector<bool> v(n,0);
    std::queue<int> q;
    q.push(s);
    v[s] = true;
    while (!q.empty()) {
        int n = q.front();
        q.pop();
        for (int u : adj[n]) {
            if (!v[u]) {
                v[n] = true;
                q.push(u);
            }
        }
    }
}
```

## Depth-First Search

Depth-first search is a graph traversal method that explores farthest vertices first. A simple implementation is below:

```cpp
void dfs(std::vector<std::vector<int>>& adj, int s, int n) {
    std::vector<bool> v(n,0);
    for (int u = 0; u < n; ++u) {
        if (!v[u]) {
            dfsrec(u, adj, v);
        }
    }
}

void dfsrec(int u, std::vector<std::vector<int>>& adj, std::vector<bool>& v) {
    v[u] = true;
    for (int n : adj[u]) {
        if (!v[n]) {
            dfsrec(n, adj, v);
        }
    }
}
```

## Dijkstra

Suppose we have a graph (undirected or directed) with positive weights associated with every edge that we call the cost of that edge. Dijkstra's algorithm finds the shortest-path between a starting node denoted $s$ and any other node in the graph, where by shortest path we mean a path in the graph such that the sum of all the edges of the path is minimized.

```cpp
void dijkstras(std::vector<std::vector<std::pair<int,int>>>& adj, int n, int s) {
    std::vector<int> dist(n,INT_MAX);
    std::vector<int> par(n,0);
    std::priority_queue<std::pair<int,int>, std::vector<std::pair<int,int>>, std::greater<std::pair<int,int>>> pq;
    pq.push({0,s});
    while (!pq.empty()) {
        auto [d,n] = pq.top();
        pq.pop();
        if (d <= dist[n]) {
            for (auto& u : adj[n]) {
                int on = u.first;
                int w = u.second;
                if (d + w < dist[on]) {
                    dist[on] = d + w;
                    par[on] = n;
                    pq.push({dist[on], on});
                }
            }
        }
    }
}
```

## Bellman-Ford

Now suppose we have a directed graph. Bellman-Ford again finds the shortest-path, but allows negative weights. Note that Dijkstra is not suited for this purpose because it would run infinitely (any negative-weight cycle would cause nodes to keep getting added to the priority queue). Bellman-Ford is able to sidestep this, though at a performance cost.

```cpp
struct Edge {
    int from;
    int to;
    int cost;
};

void bellmanford(std::vector<Edge>& edges, int s, int n) {
    std::vector<int> dist(n,INT_MAX);
    std::vector<int> p(n,-1);
    dist[s] = 0;
    int x;
    for (int i = 0; i < n; ++i) {
        x = -1;
        for (Edge e : edges) {
            if (dist[e.from] < INT_MAX) {
                if (dist[e.to] > dist[e.from] + e.cost) {
                    dist[e.to] = std::max(INT_MIN, dist[e.from] + e.cost);
                    p[e.to] = e.from;
                    x = e.to;
                }
            }
        }
    }
    if (x == -1) {
        std::cout << "No negative cycle\n";
    } else {
        int y = x;
        for (int i = 0; i < n; ++i)
            y = p[y];
        std::vector<int> path;
        for (int c = y;; c = p[c]) {
            path.push_back(c);
            if (c == y && path.size() > 1)
                break;
        }
        std::reverse(path.begin(), path.end());
        std::cout << "Negative cycle: ";
        for (int u : path)
            std::cout << u << " ";
        std::cout << "\n";
    }
}
```

## Bipartite

We are given an undirected graph. Our task is to find out whether or not the graph is bipartite: i.e. whether we can partition the graph into two sets of vertices such that there are no edges between any two vertices in the same partition.

```cpp
bool bipartite(std::vector<std::vector<int>>& adj) {
    int n = adj.size();
    bool b;
    std::vector<int> s(n,-1);
    std::queue<int> q;
    for (int i = 0; i < n; ++i) {
        if (s[i] == -1) {
            q.push(i);
            s[i] = 0;
            while (!q.empty()) {
                int n = q.front();
                q.pop();
                for (int u : adj[n]) {
                    if (s[u] == -1) {
                        s[u] = s[n] ^ 1;
                        q.push(u);
                    } else {
                        b &= s[u] != s[n];
                    }
                }
            }
        }
    }
    return b;
}
```

## Kruskal

Kruskal's algorithm is an algorithm to find a [Minimum Spanning Tree(MST)](https://en.wikipedia.org/wiki/Minimum_spanning_tree).

## Floyd-Warshall

Floyd-Warshall is an algorithm for finding the shortest path in a (directed or undirected) graph that allows negative weights but no negative weight cycles in $O(n^3)$ time.

## Kuhn

Kuhn's algorithm finds the maximum bipartite matching for a bipartite graph $G$. A matching of a bipartite graph is a set of edges that are non-adjacent, that is only one edge is incident on a vertex for all vertices. Our task is to find the maximum matching, that is, the matching that contains the largest number of edges. Note that there is also a notion of a maximal matching that is defined as a matching that is not properly contained in another matching. A maximum matching is easily seen to be maximal, but the other direction is not true.

The key definition to find this maximum matching is that of an augmenting path. An augmenting path is an alternating path (i.e. each edge sends a vertex from one side to the other side in a bipartite graph) such that the beginning and ending are unsaturated, that is they do not already belong in the matching. It can be shown that if there exists no augmenting path then the matching is maximum (note that it is maximum, not just maximal).

```cpp
bool rec(int n, vector<vector<int>>& g, vector<bool>& v, vector<int>& m) {
    if (v[n])
        return false;
    v[n] = true;
    for (int u : g[n]) {
        if (m[u] == -1 || rec(u,g,v,m)) {
            m[u] = n;
            return true;
        }
    }
    return false;
}

void kuhn(int N, int K, vector<vector<int>>& g) {
    vector<int> m(K,-1);
    vector<bool> v;
    for (int i = 0; i < N; ++i) {
        v.assign(N,false);
        rec(i,g,v,m);
    }
}
```

We can improve the above approach by the following: before we try and find any augmenting path, simply loop through the set of N vertices on one side and try and assign them to another vertex on the other side. This will create an initial matching that will not in general be maximum before we run the main algorithm, but will in general by faster than simply running Kuhn from the start.

## Ford-Fulkerson/Edmonds-Karp

Ford-Fulkerson is a general method for determining the maximum flow in a flow network. A flow network can be roughly thought of as a directed graph with a flow function associated with each edge representing the capacity of that edge. A flow through this network is again a function from the edges of this graph to a number such that the inflow equals the outflow for every vertex except for two distinguished vertices, the source and the target, where flow must come out of the source and flow must go into the target.

We first define what is known as the residual network that represents the residual capacity given some flow. For example, the residual capacity of a flow with 0 for every edge is simply the flow network itself. We additionally define a residual network for every reversed edge as well, defined as the flow we can take back. For example, if an edge has flow 5 from vertex $u$ to $v$, the residual network from $v$ to $u$ is 5, since we can take back 5 flow along this edge.

Ford-Fulkerson finds the augmenting path by means of an augmenting path. Using our definition of a residual network, an augmenting path is simply a path from $s$ to $t$ such that every edge has the same flow along it. Notice that in our residual network there are actually two directed edges between any two vertices, so we can travel forwards or backwards.

```cpp
int bfs(int s, int t, vector<int>& p, vector<vector<int>>& g, vector<vector<int>>& cap) {
    fill(p.begin(),p.end());
    p[s] = -2;
    queue<pair<int,int>> q;
    q.push({s,INT_MAX});
    while (!q.empty()) {
        auto [n,f] = q.front();
        q.pop();
        for (int u : g[n]) {
            if (p[u] == -1 && cap[n][u]) {
                p[u] = n;
                int nf = min(f,cap[n][u]);
                if (u == t)
                    return nf;
                q.push({u,nf});
            }
        }
    }
    return 0;
}

int fordfulkerson(int N, vector<vector<int>>& g, vector<vector<int>>& cap, int s, int t) {
    int f = 0;
    int nf;
    vector<int> p;
    while (nf = bfs(s,t,p,g,cap)) {
        f += nf;
        int c = t;
        while (c != s) {
            int pv = p[c];
            cap[pv][c] -= nf;
            cap[c][pv] += nf;
            c = pv;
        }
    }
    return f;
}
```

## String Hashing

We want to compare strings efficiently. We do this by hashing the strings into integers and comparing the integers instead. The idea is as follows:

\[ h(s) = \sum_{i=0}^{n-1}{s[i] \cdot p^i}  \qquad \mathrm{mod} \quad m\]

```cpp
long long h(string const& s) {
    const int p = 31;
    const int m = 1e9 + 9;
    long long v = 0;
    long long pp = 1;
    for (char c : s) {
        v = (v + (c-'a'+1) * pp) % m;
        pp = (pp*p) % m;
    }
    return v;
}
```

## Robin-Karp

## Trie

A Trie (or prefix tree) is a string data structure used to store a dictionary of strings. It allows for fast generation of autocomplete lists.

```cpp
class TrieNode {
public:
    TrieNode *child[26];
    bool isWord;
    TrieNode() {
        for (auto& a : child)
            a = nullptr;
        isWord = false;
    }
};
class Trie {
    TrieNode* root;
public:
    Trie() {
        root = new TrieNode();
    }
    bool search(string key, bool prefix = false) {
        TrieNode* p = root;
        for (auto& c : key) {
            int i = c-'a';
            if (!p->child[i])
                return false;
            p = p->child[i];
        }
        if (!prefix)
            return p->isWord;
        return true;
    }
    void insert(std::string s) {
        TrieNode* p = root;
        for (auto& c : s) {
            int i = c-'a';
            if (!p->child[i])
                p->child[i] = new TrieNode();
            p = p->child[i];
        }
        p->isWord = true;
    }
    bool startsWith(string prefix) {
        return search(prefix,true);
    }
};
```