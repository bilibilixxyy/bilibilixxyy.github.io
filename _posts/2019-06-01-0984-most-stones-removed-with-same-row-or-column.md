---
layout: post
title: 984. Most Stones Removed with Same Row or Column
---
### Question
On a 2D plane, we place stones at some integer coordinate points.  Each
coordinate point may have at most one stone.

Now, a _move_ consists of removing a stone  that shares a column or row with
another stone on the grid.

What is the largest possible number of moves we can make?



 **Example 1:**

    
    
     **Input:** stones = [[0,0],[0,1],[1,0],[1,2],[2,1],[2,2]]
    **Output:** 5
    

**Example 2:**

    
    
    **Input:** stones = [[0,0],[0,2],[1,1],[2,0],[2,2]]
    **Output:** 3
    

**Example 3:**

    
    
    **Input:** stones = [[0,0]]
    **Output:** 0
    



 **Note:**

  1. `1 <= stones.length <= 1000`
  2. `0 <= stones[i][j] < 10000`

### Solution 1
##  **Problem:**

we can remove a stone if and only if,  
there is another stone in the same column OR row.  
We try to remove as many as stones as possible.

Find more details in chinese on the
[jianshu](https://www.jianshu.com/p/30d2058db7f7)

##  **One sentence to solve:**

Connected stones can be reduced to 1 stone,  
the maximum stones can be removed = stones number - islands number.  
so just count the number of "islands".

##  **1\. Connected stones**

Two stones are connected if they are in the same row or same col.  
Connected stones will build a connected graph.  
It's obvious that in one connected graph,  
we can't remove all stones.

We have to have one stone left.  
An intuition is that, in the best strategy, we can remove until 1 stone.

I guess you may reach this step when solving the problem.  
But the important question is, how?

##  **2\. A failed strategy**

Try to remove the least degree stone  
Like a tree, we try to remove leaves first.  
Some new leaf generated.  
We continue this process until the root node left.

However, there can be no leaf.  
When you try to remove the least in-degree stone,  
it won't work on this "8" like graph:  
[[1, 1, 0, 0, 0],  
[1, 1, 0, 0, 0],  
[0, 1, 1, 0, 0],  
[0, 0, 1, 1, 1],  
[0, 0, 0, 1, 1]]

The stone in the center has least degree = 2.  
But if you remove this stone first,  
the whole connected stones split into 2 parts,  
and you will finish with 2 stones left.

##  **3\. A good strategy**

In fact, the proof is really straightforward.  
You probably apply a `DFS`, from one stone to next connected stone.  
You can remove stones in reversed order.  
In this way, all stones can be removed but the stone that you start your DFS.

One more step of explanation:  
In the view of DFS, a graph is explored in the structure of a tree.  
As we discussed previously,  
a tree can be removed in topological order,  
from leaves to root.

##  **4\. Count the number of islands**

We call a connected graph as an island.  
One island must have at least one stone left.  
The maximum stones can be removed = stones number - islands number

The whole problem is transferred to:  
What is the number of islands?

You can show all your skills on a DFS implementation,  
and solve this problem as a normal one.

##  **5\. Unify index**

Struggle between rows and cols?  
You may duplicate your codes when you try to the same thing on rows and cols.  
In fact, no logical difference between col index and rows index.

An easy trick is that, add 10000 to col index.  
So we use 0 ~ 9999 for row index and 10000 ~ 19999 for col.

##  **6\. Search on the index, not the points**

When we search on points,  
we alternately change our view on a row and on a col.

We think:  
a row index, connect two stones on this row  
a col index, connect two stones on this col.

In another view：  
A stone, connect a row index and col.

Have this idea in mind, the solution can be much simpler.  
The number of islands of _points_ ,  
is the same as the number of islands of _indexes_.

##  **7\. Union-Find**

I use union find to solve this problem.  
As I mentioned, the elements are not the points, but the indexes.

  1. for each point, union two indexes.
  2. return points number - union number

Copy a template of union-find,  
write 2 lines above,  
you can solve this problem in several minutes.

##  **Complexity**

union and find functions have worst case `O(N)`, amortize `O(1)`  
The whole union-find solution with path compression,  
has `O(N)` Time, `O(N)` Space

If you have any doubts on time complexity,  
please refer to [wikipedia](https://en.wikipedia.org/wiki/Disjoint-
set_data_structure) first.

 **C++:**

    
    
        int removeStones(vector<vector<int>>& stones) {
             for (int i = 0; i < stones.size(); ++i)
                uni(stones[i][0], ~stones[i][1]);
            return stones.size() - islands;
        }
    
        unordered_map<int, int> f;
        int islands = 0;
    
        int find(int x) {
            if (!f.count(x)) f[x] = x, islands++;
            if (x != f[x]) f[x] = find(f[x]);
            return f[x];
        }
    
        void uni(int x, int y) {
            x = find(x), y = find(y);
            if (x != y) f[x] = y, islands--;
        }
    

**Java:**

    
    
        Map<Integer, Integer> f =  new HashMap<>();
        int islands = 0;
    
        public int removeStones(int[][] stones) {
            for (int i = 0; i < stones.length; ++i)
                union(stones[i][0], ~stones[i][1]);
            return stones.length - islands;
        }
    
        public int find(int x) {
            if (f.putIfAbsent(x, x) == null)
                islands++;
            if (x != f.get(x))
                f.put(x, find(f.get(x)));
            return f.get(x);
        }
    
        public void union(int x, int y) {
            x = find(x);
            y = find(y);
            if (x != y) {
                f.put(x, y);
                islands--;
            }
        }
    

**Python:**

    
    
         def removeStones(self, points):
            UF = {}
            def find(x):
                if x != UF[x]:
                    UF[x] = find(UF[x])
                return UF[x]
            def union(x, y):
                UF.setdefault(x, x)
                UF.setdefault(y, y)
                UF[find(x)] = find(y)
    
            for i, j in points:
                union(i, ~j)
            return len(points) - len({find(x) for x in UF})
    


### Solution 2
  1. If stone `a` and stone `b` are in the same column/row, we connect them as a component
  2. One component can be removed to one stone left at least.
  3. The largest possible number of moves we can make = Total stones count - count of stones left = Total stones count - count of components.

    
    
    class Solution {
        int count = 0;
        public int removeStones(int[][] stones) {
            Map<String, String> parent = new HashMap<>();
            count = stones.length;
            // init Union Find
            for (int[] stone : stones) {
                String s = stone[0] + " " + stone[1];
                parent.put(s, s);
            }
            for (int[] s1 : stones) {
                String ss1 = s1[0] + " " + s1[1];
                for (int[] s2 : stones) {
                    if (s1[0] == s2[0] || s1[1] == s2[1]) { // in the same column or row
                        String ss2 = s2[0] + " " + s2[1];
                        union(parent, ss1, ss2);
                    }
                }
            }
            return stones.length - count;
        }
        private void union(Map<String, String> parent, String s1, String s2) {
            String r1 = find(parent, s1), r2 = find(parent, s2);
            if (r1.equals(r2)) {
                return;
            }
            parent.put(r1, r2);
            count--;
        }
        private String find(Map<String, String> parent, String s) {
            if (!parent.get(s).equals(s)) {
                parent.put(s, find(parent, parent.get(s)));
            }
            return parent.get(s);
        }
    }
    


### Solution 3
What is the inputs stands for? Is it stands for the cordinate's of the stone?
Also What does remove and moves means here? And what's the goal?



