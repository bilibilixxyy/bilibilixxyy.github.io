---
layout: post
title: 863. Sum of Distances in Tree
---
### Question
An undirected, connected tree with `N` nodes labelled `0...N-1` and `N-1`
`edges` are given.

The `i`th edge connects nodes `edges[i][0] `and` edges[i][1]` together.

Return a list `ans`, where `ans[i]` is the sum of the distances between node
`i` and all other nodes.

 **Example 1:**

    
    
     **Input:** N = 6, edges = [[0,1],[0,2],[2,3],[2,4],[2,5]]
    **Output:** [8,12,6,10,10,10]
    **Explanation:**
    Here is a diagram of the given tree:
      0
     / \
    1   2
       /|\
      3 4 5
    We can see that dist(0,1) + dist(0,2) + dist(0,3) + dist(0,4) + dist(0,5)
    equals 1 + 1 + 2 + 2 + 2 = 8.  Hence, answer[0] = 8, and so on.
    

Note: `1 <= N <= 10000`

### Solution 1
Well, another long solution.  
what I am always trying is to:

  1. let you understand my solution (with my poor explanation)
  2. prevent from reading my codes

 **Intuition** :  
What if given a tree, with a certain root `0`?  
In `O(N)` we can find sum of distances in tree from root and all other nodes.  
Now for all `N` nodes?  
Of course, we can do it `N` times and solve it in `O(N^2)`.  
C++ and Java may get accepted luckily, but it's not what we want.

When we move our root from one node to its connected node,  
one part of nodes get closer, one the other part get further.

If we know exactly how many nodes in both parts, we can solve this problem.

With one single traversal in tree, we should get enough information for it and  
don't need to do it again and again.

 **Explanation** :  
0\. Let's solve it with node `0` as root.

  1. Initial an array of hashset `tree`, `tree[i]` contains all connected nodes to `i`.  
Initial an array `count`, `count[i]` counts all nodes in the subtree `i`.  
Initial an array of `res`, `res[i]` counts sum of distance in subtree `i`.

  2. Post order dfs traversal, update `count` and `res`:  
`count[root] = sum(count[i]) + 1`  
`res[root] = sum(res[i]) + sum(count[i])`

  3. Pre order dfs traversal, update `res`:  
When we move our root from parent to its child `i`, `count[i]` points get 1
closer to root, `n - count[i]` nodes get 1 futhur to root.  
`res[i] = res[root] - count[i] + N - count[i]`

  4. return res, done.

 **Time Complexity** :  
dfs: `O(N)`  
dfs2: `O(N)`

 **Java:**

    
    
         int[] res, count;
        ArrayList<HashSet<Integer>> tree;
        public int[] sumOfDistancesInTree(int N, int[][] edges) {
            tree = new ArrayList<HashSet<Integer>>();
            res = new int[N];
            count = new int[N];
            for (int i = 0; i < N ; ++i)
                tree.add(new HashSet<Integer>());
            for (int[] e : edges) {
                tree.get(e[0]).add(e[1]);
                tree.get(e[1]).add(e[0]);
            }
            dfs(0, -1);
            dfs2(0, -1);
            return res;
        }
    
        public void dfs(int root, int pre) {
            for (int i : tree.get(root)) {
                if (i == pre) continue;
                dfs(i, root);
                count[root] += count[i];
                res[root] += res[i] + count[i];
            }
            count[root]++;
        }
    
    
        public void dfs2(int root, int pre) {
            for (int i : tree.get(root)) {
                if (i == pre) continue;
                res[i] = res[root] - count[i] + count.length - count[i];
                dfs2(i, root);
            }
        }
    

**C++:**

    
    
         vector<unordered_set<int>> tree;
        vector<int> res, count;
    
        vector<int> sumOfDistancesInTree(int N, vector<vector<int>>& edges) {
            tree.resize(N);
            res.assign(N, 0);
            count.assign(N, 1);
            for (auto e : edges) {
                tree[e[0]].insert(e[1]);
                tree[e[1]].insert(e[0]);
            }
            dfs(0, -1);
            dfs2(0, -1);
            return res;
    
        }
    
        void dfs(int root, int pre) {
            for (auto i : tree[root]) {
                if (i == pre) continue;
                dfs(i, root);
                count[root] += count[i];
                res[root] += res[i] + count[i];
            }
        }
    
        void dfs2(int root, int pre) {
            for (auto i : tree[root]) {
                if (i == pre) continue;
                res[i] = res[root] - count[i] + count.size() - count[i];
                dfs2(i, root);
            }
        }
    

**Python:**

    
    
        def sumOfDistancesInTree(self,  N, edges):
            tree = collections.defaultdict(set)
            res = [0] * N
            count = [1] * N
            for i, j in edges:
                tree[i].add(j)
                tree[j].add(i)
    
            def dfs(root, pre):
                for i in tree[root]:
                    if i != pre:
                        dfs(i, root)
                        count[root] += count[i]
                        res[root] += res[i] + count[i]
    
            def dfs2(root, pre):
                for i in tree[root]:
                    if i != pre:
                        res[i] = res[root] - count[i] + N - count[i]
                        dfs2(i, root)
            dfs(0, -1)
            dfs2(0, -1)
            return res
    


### Solution 2
Use two traversals on the tree, we can root at any node.

  * The first step is to calculate the size of each subtree and the sum distance for the root of each subtree. Also, the sum distance for our chosen root is the correct sum distance for the entire tree.
  * The second step is to calculte the sum distance in the whole tree for each node, using the size of each subtree.

![image](https://s3-lc-
upload.s3.amazonaws.com/users/luckypants/image_1526180451.png)

    
    
    class Solution:
        def sumOfDistancesInTree(self, N, edges):
            dic1 = collections.defaultdict(list)
            for e in edges:
                dic1[e[0]].append(e[1])
                dic1[e[1]].append(e[0])
            
            exclude = {0}
            
            # eachItem subtreeDist[n]=[a, b] means subtree rooted at n has totally a nodes, 
            # and sum of distance in the subtree for n is b
            subtreeDist = [[0, 0] for _ in range(N)]
            
            ans = [0]*N
            
            def sumSubtreeDist(n, exclude):
                cnt, ret = 0, 0
                exclude.add(n)
                for x in dic1[n]:
                    if x in exclude:
                        continue
                    res = sumSubtreeDist(x, exclude)
                    cnt += res[0]
                    ret += (res[0]+res[1])
                subtreeDist[n][0] = cnt+1
                subtreeDist[n][1] = ret
                return cnt+1, ret
                
            # recursively calculate the sumDist for all subtrees 
            # 0 can be replaced with any other number in [0, N-1]
            # and the chosen root has its correct sum distance in the whole tree
            sumSubtreeDist(0, set())
            
            # visit and calculates the sum distance in the whole tree
            def visit(n, pre, exclude):
                if pre==-1:
                    ans[n] = subtreeDist[n][1]
                else:
                    ans[n] = ans[pre]-2*subtreeDist[n][0]+N
                exclude.add(n)
                for x in dic1[n]:
                    if x not in exclude:
                        visit(x, n, exclude)
                    
            visit(0, -1, set())
            return ans
    


### Solution 3
My solution is in principle the same with the O(n) solutions posted by other
people. But their explain is more or less based on directed tree while my
intuition is based directly on undirected tree, as mentioned in the original
problem.

In an undirected, every node is simply "connected" with each other, and their
roles are equivalent. It is wired to call any of them as parent/children/root
nodes. Although you can select an arbitrary node as root and create a directed
tree, you don't need to.

**Explanation** :

  * Disconnecting any edge, say [i, j], would break the tree into two subtrees, one with node i and another with node j
  * Use count[i,j] to represent the number of nodes in subtree with node i if edge [i, j] was disconnected. Then count[j,i] = N - count[i,j]
  * For any adjacent node pair i and j: **sum[j] = sum[i] - count[j,i] + count[i,j]**
  * Calculate sum of distance for one arbitrary node, and use the fomula to calculate the others.

**Steps**

  * Create node representation of tree from given edge list, **O(n) time & space**
  * Calculate count[i,j], **dp, O(n) time & space**
  * Calculate sum[0], **bfs, O(n) time & space**
  * Calculate sum for all other nodes, **bfs, O(n) time & space**

**O(n) time & space** in total.



