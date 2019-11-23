---
layout: post
title: 1087. Longest Arithmetic Sequence
---
### Question
Given an array `A` of integers, return the **length** of the longest
arithmetic subsequence in `A`.

Recall that a _subsequence_ of `A` is a list `A[i_1], A[i_2], ..., A[i_k]`
with `0 <= i_1 < i_2 < ... < i_k <= A.length - 1`, and that a sequence `B` is
_arithmetic_ if `B[i+1] - B[i]` are all the same value (for `0 <= i < B.length
- 1`).



 **Example 1:**

    
    
     **Input:** [3,6,9,12]
    **Output:** 4
    **Explanation:**
    The whole array is an arithmetic sequence with steps of length = 3.
    

**Example 2:**

    
    
    **Input:** [9,4,7,2,10]
    **Output:** 3
    **Explanation:**
    The longest arithmetic subsequence is [4,7,10].
    

**Example 3:**

    
    
    **Input:** [20,1,15,3,10,5,8]
    **Output:** 4
    **Explanation:**
    The longest arithmetic subsequence is [20,15,10,5].
    



 **Note:**

  1. `2 <= A.length <= 2000`
  2. `0 <= A[i] <= 10000`

### Solution 1
`dp[diff][index]` equals to the length of arithmetic sequence at `index` with
difference `diff`.

 **Java**

    
    
        public int longestArithSeqLength(int[] A) {
            int res =  2, n = A.length;
            HashMap<Integer, Integer>[] dp = new HashMap[n];
            for (int j = 0; j < A.length; j++) {
                dp[j] = new HashMap<>();
                for (int i = 0; i < j; i++) {
                    int d = A[j] - A[i];
                    dp[j].put(d, dp[i].getOrDefault(d, 1) + 1);
                    res = Math.max(res, dp[j].get(d));
                }
            }
            return res;
        }
    

**C++**

    
    
         int longestArithSeqLength(vector<int>& A) {
            unordered_map<int, unordered_map<int, int>> dp;
            int res = 2, n = A.size();
            for (int i = 0; i < n; ++i)
                for (int j = i + 1; j < n; ++j)  {
                    int d = A[j] - A[i];
                    dp[d][j] = dp[d].count(i) ? dp[d][i] + 1 : 2;
                    res = max(res, dp[d][j]);
                }
            return res;
        }
    

**Python:**

    
    
        def longestArithSeqLength(self, A):
            dp = {}
             for i in xrange(len(A)):
                for j in xrange(i + 1, len(A)):
                    dp[j, A[j] - A[i]] = dp.get((i, A[j] - A[i]), 1) + 1
            return max(dp.values())
    


### Solution 2
The main idea is to maintain a map of differences seen at each index.

We iteratively build the map for a new index `i`, by considering all elements
to the left one-by-one.  
For each pair of indices `(i,j)` and difference `d = A[i]-A[j]` considered, we
check if there was an existing chain at the index `j` with difference `d`
already.

  * If yes, we can then extend the existing chain length by 1.
  * Else, if not, then we can start a new chain of length `2` with this new difference `d` and `(A[j], A[i])` as its elements.

At the end, we can then return the maximum chain length that we have seen so
far.

    
    
    Time Complexity: O(n^2)
    Space Complexity: O(n^2)
    
    
    
    class Solution {
        public int longestArithSeqLength(int[] A) {
            if (A.length <= 1) return A.length;
           
            int longest = 0;
            
            // Declare a dp array that is an array of hashmaps.
            // The map for each index maintains an element of the form-
            //   (difference, length of max chain ending at that index with that difference).        
            HashMap<Integer, Integer>[] dp = new HashMap[A.length];
            
            for (int i = 0; i < A.length; ++i) {
                // Initialize the map.
                dp[i] = new HashMap<Integer, Integer>();
            }
            
            for (int i = 1; i < A.length; ++i) {
                int x = A[i];
                // Iterate over values to the left of i.
                for (int j = 0; j < i; ++j) {
                    int y = A[j];
                    int d = x - y;
                    
                    // We at least have a minimum chain length of 2 now,
                    // given that (A[j], A[i]) with the difference d, 
                    // by default forms a chain of length 2.
                    int len = 2;  
                    
                    if (dp[j].containsKey(d)) {
                        // At index j, if we had already seen a difference d,
                        // then potentially, we can add A[i] to the same chain
                        // and extend it by length 1.
                        len = dp[j].get(d) + 1;
                    }
                    
                    // Obtain the maximum chain length already seen so far at index i 
                    // for the given differene d;
                    int curr = dp[i].getOrDefault(d, 0);
                    
                    // Update the max chain length for difference d at index i.
                    dp[i].put(d, Math.max(curr, len));
                    
                    // Update the global max.
                    longest = Math.max(longest, dp[i].get(d));
                }
            }
            
            return longest;
        }
    }
    


### Solution 3
same as LIS problem, use extra space record the length for all different
arithmetic  
d[i][v] means the length ending with A[i] for a difference v

    
    
    class Solution:
        def longestArithSeqLength(self, A: List[int]) -> int:
            d = collections.defaultdict(dict)
            n = len(A)
            res = 1
            for i in range(n):
                for j in range(i):
                    v = A[i] - A[j]
                    # the default length is 1
                    if v not in d[j]: d[j][v] = 1
                    if v not in d[i]: d[i][v] = 0
                    d[i][v] = max(d[i][v] ,d[j][v] + 1)
                    res = max(res, d[i][v])
            return res
    



