---
title: "AOC Day 8 [Part 1] - Union Find and Kruskal's Algorithm"
date: 2025-12-09T21:03:23+07:00
draft: false
---

In today's puzzle, we're given a list of 3d coordinates each representing the location of an electrical junction box. In part 1 we're asked to connect together the closest 1000 pairs of junction boxes and find the product of the size of the three largest circuits.
```
162,817,812
57,618,57
906,360,560
592,479,940
352,342,300
466,668,158
...
```
Note that if a pair is already in the same circuit, nothing happens. What we're basically doing in this problem is building a minimum spanning tree from a complete graph where each edge's weight is the straight line distance between 2 junction boxes.

Implementing Kruskal's algorithm with the Union Find data structure has been on my To-do list since learning about it in my algorithm design course. This is a perfect opportunity for me to do just that! First I parsed the coordinates and create a sorted list of edges between each boxes. Then I implemented the Union-Find data structure with the help of my course's slides and [Geeks for Geeks](https://www.geeksforgeeks.org/dsa/introduction-to-disjoint-set-data-structure-or-union-find-algorithm/). It's actually quite easy to implement. The parent array is used to keep track of connected components and root vertices. The subtree array is used to keep track of the size of each rooted subtree. We use recursion to implement the Find operation. The size of each subtree is compared when uniting sets and the taller tree is appointed as the new root for the new tree.
```c++
class UnionFind {
    std::vector<int> parent;
    std::vector<int> subtree;

public:
    UnionFind(int size) {
        for (int i = 0; i < size; i++) {
            parent.push_back(i);
            subtree.push_back(1);
        }
    }
    
    int find(int v) {
        if (parent[v] == v) {
            return v;
        }
        return find(parent[v]);
    }

    bool unite(int u, int v) {
        int repu = find(u);
        int repv = find(v);
        if (repu == repv) return false;
        if (subtree[u] >= subtree[v]) {
            parent[repv] = repu;
            subtree[repu] += subtree[repv];
        } else {
            parent[repu] = repv;
            subtree[repv] += subtree[repu];
        }
        return true;
    }

```
For part 1 we just have to perform Kruskal's algorithm on the first 1000 smallest edges, then find the 3 biggest circuits and multiply their sizes. For part 2, we do full Kruskal's algorithm to create the minimum spanning tree with n - 1 edges. We then look at the last edge and multiply the x coordinates of the two boxes connected by that edge.

---
## Things I've learnt

### Padding output
This is how you pad integers when printing to standard output. Look into `<iomanip>` header to learn more about standard output formatting.

```c++
#include <iomanip>

int main() {
    std::cout << std::setfill(' ') << std::setw(3) << 25;
    return 0;
}
```
