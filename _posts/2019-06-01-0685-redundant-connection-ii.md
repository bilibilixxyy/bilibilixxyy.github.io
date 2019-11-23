---
layout: post
title: 685. Redundant Connection II
---
### Question
In this problem, a rooted tree is a **directed** graph such that, there is
exactly one node (the root) for which all other nodes are descendants of this
node, plus every node has exactly one parent, except for the root node which
has no parents.

The given input is a directed graph that started as a rooted tree with N nodes
(with distinct values 1, 2, ..., N), with one additional directed edge added.
The added edge has two different vertices chosen from 1 to N, and was not an
edge that already existed.

The resulting graph is given as a 2D-array of `edges`. Each element of `edges`
is a pair `[u, v]` that represents a **directed** edge connecting nodes `u`
and `v`, where `u` is a parent of child `v`.

Return an edge that can be removed so that the resulting graph is a rooted
tree of N nodes. If there are multiple answers, return the answer that occurs
last in the given 2D-array.

 **Example 1:**  

    
    
     **Input:** [[1,2], [1,3], [2,3]]
    **Output:** [2,3]
    **Explanation:** The given directed graph will be like this:
      1
     / \
    v   v
    2-- >3
    

**Example 2:**  

    
    
    **Input:** [[1,2], [2,3], [3,4], [4,1], [1,5]]
    **Output:** [4,1]
    **Explanation:** The given directed graph will be like this:
    5  <- 1 -> 2
         ^    |
         |    v
         4 <- 3
    

**Note:**  

* The size of the input 2D-array will be between 3 and 1000.
* Every integer represented in the 2D-array will be between 1 and N, where N is the size of the input array.

### Solution 1
This problem is very similar to "Redundant Connection". But the description on
the parent/child relationships is much better clarified.

    
    
    There are two cases for the tree structure to be invalid.
    1) A node having two parents;
       including corner case: e.g. [[4,2],[1,5],[5,2],[5,3],[2,4]]
    2) A circle exists
    

If we can remove exactly 1 edge to achieve the tree structure, a single node
can have at most two parents. So my solution works in two steps.

    
    
    1) Check whether there is a node having two parents. 
        If so, store them as candidates A and B, and set the second edge invalid. 
    2) Perform normal union find. 
        If the tree is now valid 
               simply return candidate B
        else if candidates not existing 
               we find a circle, return current edge; 
        else 
               remove candidate A instead of B.
    

If you like this solution, please help upvote so more people can see.

    
    
    class Solution {
    public:
        vector<int> findRedundantDirectedConnection(vector<vector<int>>& edges) {
            int n = edges.size();
            vector<int> parent(n+1, 0), candA, candB;
            // step 1, check whether there is a node with two parents
            for (auto &edge:edges) {
                if (parent[edge[1]] == 0)
                    parent[edge[1]] = edge[0]; 
                else {
                    candA = {parent[edge[1]], edge[1]};
                    candB = edge;
                    edge[1] = 0;
                }
            } 
            // step 2, union find
            for (int i = 1; i <= n; i++) parent[i] = i;
            for (auto &edge:edges) {
                if (edge[1] == 0) continue;
                int u = edge[0], v = edge[1], pu = root(parent, u);
                // Now every node only has 1 parent, so root of v is implicitly v
                if (pu == v) {
                    if (candA.empty()) return edge;
                    return candA;
                }
                parent[v] = pu;
            }
            return candB;
        }
    private:
        int root(vector<int>& parent, int k) {
            if (parent[k] != k) 
                parent[k] = root(parent, parent[k]);
            return parent[k];
        }
    };
    

Java version by MichaelLeo

    
    
    class Solution {
        public int[] findRedundantDirectedConnection(int[][] edges) {
            int[] can1 = {-1, -1};
            int[] can2 = {-1, -1};
            int[] parent = new int[edges.length + 1];
            for (int i = 0; i < edges.length; i++) {
                if (parent[edges[i][1]] == 0) {
                    parent[edges[i][1]] = edges[i][0];
                } else {
                    can2 = new int[] {edges[i][0], edges[i][1]};
                    can1 = new int[] {parent[edges[i][1]], edges[i][1]};
                    edges[i][1] = 0;
                }
            }
            for (int i = 0; i < edges.length; i++) {
                parent[i] = i;
            }
            for (int i = 0; i < edges.length; i++) {
                if (edges[i][1] == 0) {
                    continue;
                }
                int child = edges[i][1], father = edges[i][0];
                if (root(parent, father) == child) {
                    if (can1[0] == -1) {
                        return edges[i];
                    }
                    return can1;
                }
                parent[child] = father;
            }
            return can2;
        }
        
        int root(int[] parent, int i) {
            while (i != parent[i]) {
                parent[i] = parent[parent[i]];
                i = parent[i];
            }   
            return i;
        }
    }
    


### Solution 2
This problem is tricky and interesting. It took me quite a few hours to figure
it out. My first working solution was based on DFS, but I feel there might be
better solutions. By spending whole night thinking deeply, I finally cracked
it using Disjoint Set (also known as Union Find?). I want to share what I got
here.

Assumption before we start: input " **edges** " contains a directed tree with
one and only one extra edge. If we remove the extra edge, the remaining graph
should make a directed tree - a tree which has one root and from the root you
can visit all other nodes by following directed edges. It has features:

  1. one and only one root, and root does not have parent;
  2. each non-root node has exactly one parent;
  3. there is no cycle, which means any path will reach the end by moving at most (n-1) steps along the path.

By adding one edge _**(parent- >child)**_ to the tree:

  1. every node including root has exactly one parent, if _**child**_ is root;
  2. root does not have parent, one node ( _ **child**_ ) has 2 parents, and all other nodes have exactly 1 parent, if _**child**_ is not root.

Let's check cycles. By adding one edge _**(a- >b)**_ to the tree, the tree
will have:

  1. a cycle, if there exists a path from ***(b->...->a)***; in particularly, if _**b == root**_ , (in other word, add an edge from a node to root) it will make a cycle since there must be a path ***(root->...->a)***.
  2. no cycle, if there is no such a path ***(b->...->a)***.

After adding the extra edge, the graph can be generalized in 3 different
cases:  
![0_1507232871672_Screen Shot 2017-10-05 at 2.25.34
PM.png](https://discuss.leetcode.com/assets/uploads/files/1507232873325-screen-
shot-2017-10-05-at-2.25.34-pm-resized.png)

`Case 1`: "c" is the only node which has 2 parents and there is not path
(c->...->b) which means no cycle. In this case, removing either "e1" or "e2"
will make the tree valid. According to the description of the problem,
whichever edge added later is the answer.

`Case 2`: "c" is the only node which has 2 parents and there is a
path(c->...->b) which means there is a cycle. In this case, "e2" is the only
edge that should be removed. Removing "e1" will make the tree in 2 separated
groups. Note, in input `edges`, "e1" may come after "e2".

`Case 3`: this is how it looks like if edge _**(a- >root)**_ is added to the
tree. Removing any of the edges along the cycle will make the tree valid. But
according to the description of the problem, the last edge added to complete
the cycle is the answer. Note: edge "e2" (an edge pointing from a node outside
of the cycle to a node on the cycle) can never happen in this case, because
every node including root has exactly one parent. If "e2" happens, that make a
node on cycle have 2 parents. That is impossible.

As we can see from the pictures, the answer must be:

  1. one of the 2 edges that pointing to the same node in `case 1` and `case 2`; there is one and only one such node which has 2 parents.
  2. the last edge added to complete the cycle in `case 3`.

Note: both `case 2` and `case 3` have cycle, but in `case 2`, "e2" may not be
the last edge added to complete the cycle.

Now, we can apply Disjoint Set (DS) to build the tree in the order the edges
are given. We define `ds[i]` as the parent or ancestor of node `i`. It will
become the root of the whole tree eventually if `edges` does not have extra
edge. When given an edge (a->b), we find node `a`'s ancestor and assign it to
`ds[b]`. Note, in typical DS, we also need to find node `b`'s ancestor and
assign `a`'s ancestor as the ancestor of `b`'s ancestor. But in this case, we
don't have to, since we skip the second parent edge (see below), it is
guaranteed `a` is the only parent of `b`.

If we find an edge pointing to a node that already has a parent, we simply
skip it. The edge skipped can be "e1" or "e2" in `case 1` and `case 2`. In
`case 1`, removing either "e1" or "e2" will make the tree valid. In `case 3`,
removing "e2" will make the tree valid, but removing "e1" will make the tree
in 2 separated groups and one of the groups has a cycle. In `case 3`, none of
the edges will be skipped because there is no 2 edges pointing to the same
node. The result is a graph with cycle and "n" edges.

 **How to detect cycle by using Disjoint Set (Union Find)?**  
When we join 2 nodes by edge (a->b), we check `a`'s ancestor, if it is b, we
find a cycle! When we find a cycle, we don't assign `a`'s ancestor as `b`'s
ancestor. That will trap our code in endless loop. We need to save the edge
though since it might be the answer in `case 3`.

Now the code. We define two variables (`first` and `second`) to store the 2
edges that point to the same node if there is any (there may not be such
edges, see `case 3`). We skip adding `second` to tree. `first` and `second`
hold the values of the original index in input `edges` of the 2 edges
respectively. Variable `last` is the edge added to complete a cycle if there
is any (there may not be a cycle, see `case 1` and removing "e2" in `case 2`).
And it too hold the original index in input `edges`.

After adding all except at most one edges to the tree, we end up with 4
different scenario:

  1. `case 1` with either "e1" or "e2" removed. Either way, the result tree is valid. The answer is the edge being removed or skipped (a.k.a. `second`)
  2. `case 2` with "e2" removed. The result tree is valid. The answer is the edge being removed or skipped (a.k.a. `second`)
  3. `case 2` with "e1" removed. The result tree is invalid with a cycle in one of the groups. The answer is the other edge (`first`) that points to the same node as `second`.
  4. `case 3` with no edge removed. The result tree is invalid with a cycle. The answer is the `last` edge added to complete the cycle.

In the following code,  
`last == -1` means "no cycle found" which is scenario 1 or 2  
`second != -1 && last != -1` means "one edge removed and the result tree has
cycle" which is scenario 3  
`second == -1` means "no edge skipped or removed" which is scenario 4

    
    
       public int[] findRedundantDirectedConnection(int[][] edges) {
            int n = edges.length;
            int[] parent = new int[n+1], ds = new int[n+1];
            Arrays.fill(parent, -1);
            int first = -1, second = -1, last = -1;
            for(int i = 0; i < n; i++) {
                int p = edges[i][0], c = edges[i][1];
                if (parent[c] != -1) {
                    first = parent[c];
                    second = i;
                    continue;
                }
                parent[c] = i;
                
                int p1 = find(ds, p);
                if (p1 == c) last = i;
                else ds[c] = p1;
            }
    
            if (last == -1) return edges[second]; // no cycle found by removing second
            if (second == -1) return edges[last]; // no edge removed
            return edges[first];
        }
        
        private int find(int[] ds, int i) {
            return ds[i] == 0 ? i : (ds[i] = find(ds, ds[i]));
        }
    

This solution past all these test cases and ACed.  
Test case:  
[[1,2],[1,3],[2,3]]  
[[1,2], [2,3], [3,4], [4,1], [1,5]]  
[[4,2],[1,5],[5,2],[5,3],[2,4]]  
[[2,1],[3,1],[4,2],[1,4]]  
[[4,1],[1,2],[1,3],[4,5],[5,6],[6,5]]  
[[2,3], [3,4], [4,1], [1,5], [1,2]]  
[[3,1],[1,4],[3,5],[1,2],[1,5]]  
[[1,2],[2,3],[3,1]]

expected output:  
[2,3]  
[4,1]  
[4,2]  
[2,1]  
[6,5]  
[1,2]  
[1,5]  
[3,1]


### Solution 3
    
    
    public int[] findRedundantDirectedConnection(int[][] edges) {
            int[] roots = new int[edges.length];
            for (int i = 0; i < edges.length; i++) roots[i] = i;
    
            int[] candidate1 = null, candidate2 = null;
            for (int[] e : edges){
                int rootx = find(roots, e[0]-1), rooty = find(roots, e[1]-1);
                if (rootx != rooty) {
                    if (rooty != e[1]-1) candidate1 = e; // record the last edge which results in "multiple parents" issue
                    else roots[rooty] = rootx;
                }
                else candidate2 = e; // record last edge which results in "cycle" issue, if any.
            }
    
            // if there is only one issue, return this one.
            if (candidate1 == null) return candidate2; 
            if (candidate2 == null) return candidate1;
            
            // If both issues present, then the answer should be the first edge which results in "multiple parents" issue
            // Could use map to skip this pass, but will use more memory.
            for (int[] e : edges) if (e[1] == candidate1[1]) return e;
    
            return new int[2];
        }
    
        private int find(int[] roots, int i){
            while (i != roots[i]){
                roots[i] = roots[roots[i]];
                i = roots[i];
            }
            return i;
        }
    



