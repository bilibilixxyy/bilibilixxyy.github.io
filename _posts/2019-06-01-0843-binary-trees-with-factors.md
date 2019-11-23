---
layout: post
title: 843. Binary Trees With Factors
---
### Question
Given an array of unique integers, each integer is strictly greater than 1.

We make a binary tree using these integers and each number may be used for any
number of times.

Each non-leaf node's value should be equal to the product of the values of
it's children.

How many binary trees can we make?  Return the answer **modulo 10 ** 9 + 7**.

 **Example 1:**

    
    
     **Input:** A = [2, 4]
    **Output:** 3
    **Explanation:** We can make these trees: [2], [4], [4, 2, 2]

**Example 2:**

    
    
    **Input:** A = [2, 4, 5, 10]
    **Output:** 7
    **Explanation:** We can make these trees: [2], [4], [5], [10], [4, 2, 2], [10, 2, 5], [10, 5, 2].



 **Note:**

  1. `1 <= A.length <= 1000`.
  2. `2 <= A[i] <= 10 ^ 9`.

### Solution 1
Sort the list `A` at first.  
DP equation:

`dp[i] = sum(dp[j] * dp[i / j])`  
`res = sum(dp[i])`  
with `i, j, i / j` in the list `L`

C++

    
    
        int numFactoredBinarydps(vector<int>& A) {
            long res = 0, mod = pow(10, 9) + 7;
            sort(A.begin(), A.end());
            unordered_map<int, long> dp;
            for (int i = 0; i < A.size(); ++i) {
                dp[A[i]] = 1;
                for (int j = 0; j < i; ++j)
                    if (A[i] % A[j] == 0 && dp.count(A[i] / A[j]) == 1)
                        dp[A[i]] = (dp[A[i]] + dp[A[j]] * dp[A[i] / A[j]]) % mod;
            }
            for (auto &it : dp) res = (res + it.second) % mod;
            return res;
        }
    

Java

    
    
        public int numFactoredBinarydps(int[] A) {
            long res = 0L, mod = (long) Math.pow(10, 9) + 7;
            Arrays.sort(A);
            HashMap<Integer, Long> dp = new HashMap<>();
            for (int i = 0; i < A.length; ++i) {
                dp.put(A[i], 1L);
                for (int j = 0; j < i; ++j)
                    if (A[i] % A[j] == 0 && dp.containsKey(A[i] / A[j]))
                        dp.put(A[i], (dp.get(A[i]) + dp.get(A[j]) * dp.get(A[i] / A[j])) % mod);
            }
            for (long v : dp.values()) res = (res + v) % mod;
            return (int) res;
        }
    

Python

    
    
        def numBinarydps(self, A):
            A.sort()
            dp = {}
            for i in range(len(A)):
                dp[A[i]] = 1
                for j in range(i):
                    if A[i] % A[j] == 0 and A[i] / A[j] in dp:
                        dp[A[i]] += dp[A[j]] * dp[A[i] / A[j]]
            return sum(dp.values()) % (10**9 + 7)
    


### Solution 2
    
    
    /**sort the array
     * and use HashMap to record each Integer, and the number of trees with that Integer as root
     * (1) each integer A[i] will always have one tree with only itself
     * (2) if A[i] has divisor (a) in the map, and if A[i]/a also in the map
     *     then a can be the root of its left subtree, and A[i]/a can be the root of its right subtree;
     *     the number of such tree is map.get(a) * map.get(A[i]/a)
     * (3) sum over the map
     */
    class Solution {    
        public int numFactoredBinaryTrees(int[] A) {
            Arrays.sort(A);
            Map<Integer, Long> map = new HashMap();
            long count = 1;
            map.put(A[0], count);
            for (int i = 1; i < A.length; i++) {
                count = 1;
                for (Integer n : map.keySet()) {
                    if (A[i] % n == 0 && map.containsKey(A[i] / n)) {
                        count += map.get(n) * map.get(A[i] / n);
                    }
                }
                map.put(A[i], count);
            }
            long sum = 0;
            for (Integer n : map.keySet()) {
                sum = (sum + map.get(n)) % ((int) Math.pow(10, 9) + 7) ;
            }
            return (int) sum;
        }        
    }


### Solution 3
My `t[a]` tells the number of trees with root value `a`. The `1` is for making
a leaf (there's one way to make a leaf with that value), the `sum` is for non-
leaves (go over every possible left child value `b`).

    
    
    def numFactoredBinaryTrees(self, A):
        t = {}
        for a in sorted(A):
            t[a] = 1 + sum(t[b] * t.get(a/b, 0) for b in A if b < a)
        return sum(t.values()) % (10**9 + 7)
    



