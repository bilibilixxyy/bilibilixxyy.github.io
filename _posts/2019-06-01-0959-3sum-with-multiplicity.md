---
layout: post
title: 959. 3Sum With Multiplicity
---
### Question
Given an integer array `A`, and an integer `target`, return the number of
tuples `i, j, k`  such that `i < j < k` and `A[i] + A[j] + A[k] == target`.

 **As the answer can be very large, return it modulo  `10^9 + 7`**.



 **Example 1:**

    
    
     **Input:** A = [1,1,2,2,3,3,4,4,5,5], target = 8
    **Output:** 20
    **Explanation:**
    Enumerating by the values (A[i], A[j], A[k]):
    (1, 2, 5) occurs 8 times;
    (1, 3, 4) occurs 8 times;
    (2, 2, 4) occurs 2 times;
    (2, 3, 3) occurs 2 times.
    

**Example 2:**

    
    
    **Input:** A = [1,1,2,2,2,2], target = 5
    **Output:** 12
    **Explanation:**
    A[i] = 1, A[j] = A[k] = 2 occurs 12 times:
    We choose one 1 from [1,1] in 2 ways,
    and two 2s from [2,2,2,2] in 6 ways.
    



 **Note:**

  1. `3 <= A.length <= 3000`
  2. `0 <= A[i] <= 100`
  3. `0 <= target <= 300`

### Solution 1
Count the occurrence of each number.  
using hashmap or array up to you.

Loop `i` on all numbers,  
loop `j` on all numbers,  
check if `k = target - i - j` is valid.

Add the number of this combination to result.  
3 cases covers all possible combination:

  1. `i == j == k`
  2. `i == j != k`
  3. `i < k && j < k`

 **Time Complexity** :  
`3 <= A.length <= 3000`, so N = 3000  
But `0 <= A[i] <= 100`  
So my solution is `O(N + 101 * 101)`

 **C++:**

    
    
        int threeSumMulti(vector<int>& A, int target) {
            unordered_map<int, long> c;
             for (int a : A) c[a]++;
            long res = 0;
            for (auto it : c)
                for (auto it2 : c) {
                    int i = it.first, j = it2.first, k = target - i - j;
                    if (!c.count(k)) continue;
                    if (i == j && j == k)
                        res += c[i] * (c[i] - 1) * (c[i] - 2) / 6;
                    else if (i == j && j != k)
                        res += c[i] * (c[i] - 1) / 2 * c[k];
                    else if (i < j && j < k)
                        res += c[i] * c[j] * c[k];
                }
            return res % int(1e9 + 7);
        }
    

**Java:**

    
    
        public int threeSumMulti(int[] A, int target) {
            long[] c = new long[ 101];
            for (int a : A) c[a]++;
            long res = 0;
            for (int i = 0; i <= 100; i++)
                for (int j = i; j <= 100; j++) {
                    int k = target - i - j;
                    if (k > 100 || k < 0) continue;
                    if (i == j && j == k)
                        res += c[i] * (c[i] - 1) * (c[i] - 2) / 6;
                    else if (i == j && j != k)
                        res += c[i] * (c[i] - 1) / 2 * c[k];
                    else if (j < k)
                        res += c[i] * c[j] * c[k];
                }
            return (int)(res % (1e9 + 7));
        }
    

**Python:**

    
    
        def threeSumMulti(self, A, target):
            c = collections.Counter(A)
            res =  0
            for i, j in itertools.combinations_with_replacement(c, 2):
                k = target - i - j
                if i == j == k: res += c[i] * (c[i] - 1) * (c[i] - 2) / 6
                elif i == j != k: res += c[i] * (c[i] - 1) / 2 * c[k]
                elif k > i and k > j: res += c[i] * c[j] * c[k]
            return res % (10**9 + 7)
    


### Solution 2
## Think Outside of The Box!

Intuitively, you will try to solve it based on the solution of 3Sum.  
But... Build a map for counting different sums of two numbers. The rest of
things are straightfoward.

    
    
    class Solution {
        public int threeSumMulti(int[] A, int target) {
            Map<Integer, Integer> map = new HashMap<>();
            
            int res = 0;
            int mod = 1000000007;
            for (int i = 0; i < A.length; i++) {
                res = (res + map.getOrDefault(target - A[i], 0)) % mod;
                
                for (int j = 0; j < i; j++) {
                    int temp = A[i] + A[j];
                    map.put(temp, map.getOrDefault(temp, 0) + 1);
                }
            }
            return res;
        }
    }
    


### Solution 3
`dp[i][j][k]` represents number of combinations using `k` numbers within `A[0]
... A[i]` with the sum of `j`.  
Then `dp[n][target][3]` is the result. O(n * target)

    
    
    class Solution {
        public int threeSumMulti(int[] A, int target) {
            int n = A.length, M = (int)1e9 + 7;
            int[][][] dp = new int[n + 1][target + 1][4];
            for (int i = 0; i <= n; i++) {
                dp[i][0][0] = 1;
            }
            for (int i = 0; i < n; i++) {
                for (int j = 0; j <= target; j++) {
                    for (int k = 1; k <= 3; k++) {
                        dp[i + 1][j][k] = dp[i][j][k];
                        dp[i + 1][j][k] %= M;
                        if (j >= A[i]) {
                            dp[i + 1][j][k] += dp[i][j - A[i]][k - 1];
                            dp[i + 1][j][k] %= M;
                        }
                    }
                }
            }
            return dp[n][target][3];
        }
    }
    

O(target) space:

    
    
    class Solution {
        public int threeSumMulti(int[] A, int target) {
            int n = A.length, M = (int)1e9 + 7;
            int[][] dp = new int[target + 1][4];
            dp[0][0] = 1;
            for (int i = 0; i < n; i++) {
                for (int j = target; j >= A[i]; j--) {
                    for (int k = 3; k >= 1; k--) {
                        dp[j][k] += dp[j - A[i]][k - 1];
                        dp[j][k] %= M;
                    }
                }
            }
            return dp[target][3];
        }
    }
    

O(n^2) Straightforward:  
Guarantee `a <= b <= c`

    
    
    class Solution {
        public int threeSumMulti(int[] A, int target) {
            Map<Integer, Long> map = new HashMap<>();
            for (int a : A) {
                map.put(a, map.getOrDefault(a, 0l) + 1);
            }
            long result = 0;
            int M = (int)1e9 + 7;
            for (int a : map.keySet()) {
                for (int b : map.keySet()) {
                    if (b < a || a == b && map.get(a) == 1) {
                        continue;
                    }
                    int c = target - a - b;
                    if (c < b || !map.containsKey(c)) {
                        continue;
                    }
                    if (a == b && b == c) {
                        if (map.get(a) == 2) {
                            continue;
                        }
                        result += nCk(map.get(a), 3);
                    } else if (a == b) {
                        result += nCk(map.get(a), 2) * map.get(c);
                    } else if (b == c) {
                        if (map.get(b) == 1) {
                            continue;
                        }
                        result += nCk(map.get(b), 2) * map.get(a);
                    } else {
                        result += (map.get(a) * map.get(b) * map.get(c));
                    }
                    result %= M;
                }
            }
            return (int)result;
        }
        private long nCk(long n, int k) {
            if (k == 3) {
                return (n * (n - 1) * (n - 2)) / 6;
            } else {
                return n * (n - 1) / 2;
            }
        }
    }
    



