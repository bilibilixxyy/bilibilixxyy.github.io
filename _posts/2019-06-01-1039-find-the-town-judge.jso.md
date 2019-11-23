---
layout: post
title: 1039. Find the Town Judge
---
### Question
In a town, there are `N` people labelled from `1` to `N`.  There is a rumor
that one of these people is secretly the town judge.

If the town judge exists, then:

  1. The town judge trusts nobody.
  2. Everybody (except for the town judge) trusts the town judge.
  3. There is exactly one person that satisfies properties 1 and 2.

You are given `trust`, an array of pairs `trust[i] = [a, b]` representing that
the person labelled `a` trusts the person labelled `b`.

If the town judge exists and can be identified, return the label of the town
judge.  Otherwise, return `-1`.



 **Example 1:**

    
    
     **Input:** N = 2, trust = [[1,2]]
    **Output:** 2
    

**Example 2:**

    
    
    **Input:** N = 3, trust = [[1,3],[2,3]]
    **Output:** 3
    

**Example 3:**

    
    
    **Input:** N = 3, trust = [[1,3],[2,3],[3,1]]
    **Output:** -1
    

**Example 4:**

    
    
    **Input:** N = 3, trust = [[1,2],[2,3]]
    **Output:** -1
    

**Example 5:**

    
    
    **Input:** N = 4, trust = [[1,3],[1,4],[2,3],[2,4],[4,3]]
    **Output:** 3



 **Note:**

  1. `1 <= N <= 1000`
  2. `trust.length <= 10000`
  3. `trust[i]` are all different
  4. `trust[i][0] != trust[i][1]`
  5. `1 <= trust[i][0], trust[i][1] <= N`

### Solution 1
 **Intuition** :  
Consider `trust` as a graph, all pairs are directed edge.  
The point with `in-degree - out-degree = N - 1` become the judge.

 **Explanation** :  
Count the degree, and check at the end.

 **Time Complexity** :  
Time `O(T + N)`, space `O(N)`

  

 **Java:**

    
    
         public int findJudge(int N, int[][] trust) {
            int[] count = new int[N+1];
            for (int[] t: trust) {
                count[t[0]]--;
                count[t[1]]++;
            }
            for (int i = 1; i <= N; ++i) {
                if (count[i] == N - 1) return i;
            }
            return -1;
        }
    

**C++:**

    
    
         int findJudge(int N, vector<vector<int>>& trust) {
            vector<int> count(N + 1, 0);
            for (auto& t : trust)
                count[t[0]]--, count[t[1]]++;
            for (int i = 1; i <= N; ++i) {
                if (count[i] == N - 1) return i;
            }
            return -1;
        }
    

**Python:**

    
    
        def findJudge(self, N, trust):
            count = [ 0] * (N + 1)
            for i, j in trust:
                count[i] -= 1
                count[j] += 1
            for i in range(1, N + 1):
                if count[i] == N - 1:
                    return i
            return -1
    


### Solution 2
This is the same as [Find the Celebrity](https://leetcode.com/problems/find-
the-celebrity/) (Medium), but here we can access all connections directly (so
this problem is Easy).

# Brute-Force Solution

Since we have all connections, we can count people that `i`-th person`trusts`,
and people that `i`-th person is `trusted` by. Then, we just search for a
judge.

    
    
    int findJudge(int N, vector<vector<int>>& trust) {
      vector<int> trusts(N + 1), trusted(N + 1);
      for (auto &t : trust) ++trusts[t[0]], ++trusted[t[1]];
      for (auto i = 1; i <= N; ++i) if (trusts[i] == 0 && trusted[i] == N - 1) return i;
      return -1;
    }
    

We can further simplify this by using a single vector and tracking the trust
`balance`. Only the person that has `N - 1` trust balance is trusted by
everyone and trusts none.

    
    
    int findJudge(int N, vector<vector<int>>& trust) {
      vector<int> balance(N + 1);
      for (auto &t : trust) --balance[t[0]], ++balance[t[1]];
      for (auto i = 1; i <= N; ++i) if (balance[i] == N - 1) return i;
      return -1;
    }
    

## Complexity Analysis

Time: _O(m)_ , where _m_ is the number of trust pairs.  
Memory: _O(N)_.

# Find the Celebrity

If we are given trust connections as an adjacency matrix (or a hash map), we
can use the same algorithm as in the Find the Celebrity problem. Here is
[solution and explanations](https://leetcode.com/problems/find-the-
celebrity/discuss/243605/C++-4-lines) to that problem. This cool technique to
quickly find a potential celebrity helps reduce the runtime and memory
complexity.

    
    
    int findJudge(int N, vector<vector<int>>& trust) {
      vector<vector<int>> knows(N + 1, vector<int>(N + 1));
      for (auto &t : trust) knows[t[0]][t[1]] = 1;
      return findCelebrity(N, knows);
    }
    int findCelebrity(int n, vector<vector<int>>& knows, int i = 1) {
      for (auto j = i + 1; j <= n; ++j) if (knows[i][j]) i = j;
      for (auto j = 1; j < i; ++j) if (knows[i][j]) return -1;
      for (auto j = 1; j <= n; ++j) if (i != j && !knows[j][i]) return -1;
      return i;
    }
    

## Complexity Analysis

> Note that we analyze the complexity of `findCelebrity` (without preparation
steps).

Time: _O(N)_.  
Memory: _O(1)_.


### Solution 3
Keep track of the cumulative score of each person: if person a trusts person
b, we decrement a's score and increment b's score.  
 **The judge is the only person that ends up with a score of N-1.**

I initialize the trusted list with N+1 items to make indexing easier, since
the villagers are named 1 thorugh N.

 **Time complexity** O(N + T): T=len(trust). We iterate through the trust list
once and through all villagers once, so the time complexity is linear in
these. This is equivalent to |Vertices| + |Edges| in graph terms, if we
consider each person as a vertex and each trust relationship as a directed
edge.

 **Space complexity** O(N): We create a trusted list with a size of N+1 to
store the cumulative scores.

    
    
    def findJudge(self, N, trust):
    	trusted = [ 0] * (N+1)
    	for a, b in trust:
    		trusted[a] -= 1
    		trusted[b] += 1
    
    	for i in range(1, N+1):
    		if trusted[i] == N-1:
    			return i
    	return -1
    



