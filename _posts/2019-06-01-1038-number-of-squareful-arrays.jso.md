---
layout: post
title: 1038. Number of Squareful Arrays
---
### Question
Given an array `A` of non-negative integers, the array is _squareful_ if for
every pair of adjacent elements, their sum is a perfect square.

Return the number of permutations of A that are squareful.  Two permutations
`A1` and `A2` differ if and only if there is some index `i` such that `A1[i]
!= A2[i]`.



 **Example 1:**

    
    
     **Input:** [1,17,8]
    **Output:** 2
    **Explanation:**
    [1,8,17] and [17,8,1] are the valid permutations.
    

**Example 2:**

    
    
    **Input:** [2,2,2]
    **Output:** 1
    



 **Note:**

  1. `1 <= A.length <= 12`
  2. `0 <= A[i] <= 1e9`

### Solution 1
 **Explanation** :

  1. Count numbers ocuurrence.
  2. For each number `i`, find all possible next number `j` that `i + j` is square.
  3. Backtracking using dfs.

 **Time Complexity**  
It's `O(N^N)` if we have N different numbers and any pair sum is square.  
We can easily make case for N = 3 like [51,70,30].

Seems that no hard cases for this problem and int this way it reduces to
O(N^2).

 **C++:**

    
    
         unordered_map<int, int> count;
        unordered_map<int, unordered_set<int>> cand;
        int res = 0;
        int numSquarefulPerms(vector<int>& A) {
            for (int &a : A) count[a]++;
            for (auto &i : count) {
                for (auto &j : count) {
                    int x = i.first, y = j.first, s = sqrt(x + y);
                    if (s * s == x + y)
                        cand[x].insert(y);
                }
            }
            for (auto e : count)
                dfs(e.first, A.size() - 1);
            return res;
        }
    
        void dfs(int x, int left) {
            count[x]--;
            if (!left) res++;
            for (int y : cand[x])
                if (count[y] > 0)
                    dfs(y, left - 1);
            count[x]++;
        }
    

**Python:**

    
    
        def numSquarefulPerms( self, A):
            c = collections.Counter(A)
            cand = {i: {j for j in c if int((i + j)**0.5) ** 2 == i + j} for i in c}
            self.res = 0
            def dfs(x, left=len(A) - 1):
                c[x] -= 1
                if left == 0: self.res += 1
                for y in cand[x]:
                    if c[y]: dfs(y, left - 1)
                c[x] += 1
            for x in c: dfs(x)
            return self.res
    


### Solution 2
  1. Avoid duplicate numbers in recursion by sorting `Arrays.sort(A);`  
and use the `A[i]==A[i-1]` trick while iterating

  2. Before entering another recursion level check to make sure you are forming a square <\-- this part I figured out post-contest

    
    
    if (lastNumber!=-1){
                        if (isSquare(A[i],lastNumber)==false)
                            continue;
                    }
    

Full code below  
For you:

    
    
    class Solution {
        private boolean isSquare(int a, int b){
            double sqr = Math.sqrt(a+b);
            boolean res = (sqr - Math.floor(sqr)) == 0;
            return res;
        }
        
        private int count = 0;
        private void helper(List<Integer> temp, int[] A, boolean[] used, int lastNumber){
            if (temp.size()==A.length){
                count++;
            } else {
                for (int i = 0; i<A.length;i++){
                    if (used[i] || (i>0 && A[i]==A[i-1] && !used[i-1]))continue;
                    if (lastNumber!=-1){
    				// if we cant form a square we can not proceed to form a squareful array
                        if (isSquare(A[i],lastNumber)==false)
                            continue;
                    }
                    used[i] = true;
                    temp.add(A[i]);
                    helper(temp,A,used,A[i]);
                    temp.remove(temp.size()-1);
                    used[i] = false;
                }
            }
            
        }
        public int numSquarefulPerms(int[] A) {
            Arrays.sort(A);
            helper(new ArrayList(),A,new boolean[A.length],-1);
            return count;
        }
    }
    


### Solution 3
My solution is based on the idea of
<https://leetcode.com/problems/permutations-
ii/discuss/18596/A-simple-C%2B%2B-solution-in-only-20-lines>

The only difference is that by calling the recursion every time, the program
would check whether the sum of the number on current index and the previous is
a square number or not, to make sure the sub array from 0 to current index
satisfys the Squareful definition.

    
    
    class Solution {
    public:
        int numSquarefulPerms(vector<int>& A) {
            sort(A.begin(), A.end());
            int ans = 0;
            pmt(A, 0, ans);
            return ans;
        }
        void pmt(vector<int> A, int idx, int& ans) {
            if (idx >= A.size()) {
                ++ans;
            }
            for (int i = idx; i < A.size(); ++i) {
                if (i > idx && A[i] == A[idx]) continue;
                swap(A[i], A[idx]);
                if ((idx == 0) || (idx > 0 && isSquare(A[idx] + A[idx - 1]))) {
                    pmt(A, idx + 1, ans);
                }
            }
        }
        bool isSquare(int v) {
            int r = sqrt(v);
            return r * r == v;
        }
    };
    



