---
layout: post
title: 939. Valid Permutations for DI Sequence
---
### Question
We are given `S`, a length `n` string of characters from the set `{'D', 'I'}`.
(These letters stand for "decreasing" and "increasing".)

A  _valid permutation_  is a permutation `P[0], P[1], ..., P[n]` of integers
`{0, 1, ..., n}`, such that for all `i`:

  * If `S[i] == 'D'`, then `P[i] > P[i+1]`, and;
  * If `S[i] == 'I'`, then `P[i] < P[i+1]`.

How many valid permutations are there?  Since the answer may be large,
**return your answer modulo`10^9 + 7`**.



 **Example 1:**

    
    
     **Input:** "DID"
    **Output:** 5
    **Explanation:**
    The 5 valid permutations of (0, 1, 2, 3) are:
    (1, 0, 3, 2)
    (2, 0, 3, 1)
    (2, 1, 3, 0)
    (3, 0, 2, 1)
    (3, 1, 2, 0)
    



 **Note:**

  1. `1 <= S.length <= 200`
  2. `S` consists only of characters from the set `{'D', 'I'}`.

### Solution 1
 **Intuition** :

This the only problem this week that I feel like writing a solution.  
But don't know how to explain.

`dp[i][j]` means the number of possible permutations of first `i + 1` digits,  
where the `i + 1`th digit is `j + 1`th smallest in the rest of digits.

Ok, may not make sense ... Let's see the following diagram.  
![image](https://s3-lc-
upload.s3.amazonaws.com/users/lee215/image_1536486527.png)

I take the example of `S = "DID"`.  
The permutation can start from `1, 2, 3, 4`.  
So `dp[0][0] = dp[0][1] = dp[0][2] = dp[0][3] = 1`.  
In the parenthesis, I list all possible permutations.

We decrese from the first digit to the second,  
the down arrow show the all possibile decresing pathes.

The same, cause we increase from the second digit to the third,  
the up arrow show the all possibile increasing pathes.

`dp[2][1] = 5`, mean the number of permutations  
where the third digitis the second smallest of the rest.  
We have 413,314,214,423,324.  
Fow example 413, where 2,3 are left and 3 the second smallest of them.

 **Explanation** :  
As shown in the diagram,  
for "I", we calculate prefix sum of the array,  
for "D", we calculate sufixsum of the array.

 **Time Complexity** :  
O(N^2)

 **C++:**

    
    
        int numPermsDISequence(string S) {
            int n = S. length(), mod = 1e9 + 7;
            vector<vector<int>> dp(n + 1, vector<int>(n + 1));
            for (int j = 0; j <= n; j++) dp[0][j] = 1;
            for (int i = 0; i < n; i++)
                if (S[i] == 'I')
                    for (int j = 0, cur = 0; j < n - i; j++)
                        dp[i + 1][j] = cur = (cur + dp[i][j]) % mod;
                else
                    for (int j = n - i - 1, cur = 0; j >= 0; j--)
                        dp[i + 1][j] = cur = (cur + dp[i][j + 1]) % mod;
            return dp[n][0];
        }
    

**Java:**

    
    
        public int numPermsDISequence(String S) {
            int n = S. length(), mod = (int)1e9 + 7;
            int[][] dp = new int[n + 1][n + 1];
            for (int j = 0; j <= n; j++) dp[0][j] = 1;
            for (int i = 0; i < n; i++)
                if (S.charAt(i) == 'I')
                    for (int j = 0, cur = 0; j < n - i; j++)
                        dp[i + 1][j] = cur = (cur + dp[i][j]) % mod;
                else
                    for (int j = n - i - 1, cur = 0; j >= 0; j--)
                        dp[i + 1][j] = cur = (cur + dp[i][j + 1]) % mod;
            return dp[n][0];
        }
    

Now as we did for every DP, make it 1D dp.  
Reminded by @apple702, in the Java solution, it should be
`dp=Arrays.copyOf(dp2, n);`  
Otherwise it passes an address.

**C++:**

    
    
        int numPermsDISequence(string S) {
            int n = S. length(), mod = 1e9 + 7;
            vector<int> dp(n + 1, 1), dp2(n);
            for (int i = 0; i < n; dp = dp2, i++) {
                if (S[i] == 'I')
                    for (int j = 0, cur = 0; j < n - i; j++)
                        dp2[j] = cur = (cur + dp[j]) % mod;
                else
                    for (int j = n - i - 1, cur = 0; j >= 0; j--)
                        dp2[j] = cur = (cur + dp[j + 1]) % mod;
            }
            return dp[0];
        }
    

**Java:**

    
    
        public int numPermsDISequence(String S) {
            int n = S. length(), mod = (int)1e9 + 7;
            int[] dp = new int[n + 1], dp2 = new int[n];;
            for (int j = 0; j <= n; j++) dp[j] = 1;
            for (int i = 0; i < n; i++) {
                if (S.charAt(i) == 'I')
                    for (int j = 0, cur = 0; j < n - i; j++)
                        dp2[j] = cur = (cur + dp[j]) % mod;
                else
                    for (int j = n - i - 1, cur = 0; j >= 0; j--)
                        dp2[j] = cur = (cur + dp[j + 1]) % mod;
                dp = Arrays.copyOf(dp2, n);
            }
            return dp[0];
        }
    

**Python:**

    
    
        def numPermsDISequence(self, S):
             dp = [1] * (len(S) + 1)
            for c in S:
                if c == "I":
                    dp = dp[:-1]
                    for i in range(1, len(dp)):
                        dp[i] += dp[i - 1]
                else:
                    dp = dp[1:]
                    for i in range(len(dp) - 1)[::-1]:
                        dp[i] += dp[i + 1]
            return dp[0] % (10**9 + 7)
    


### Solution 2
Before diving into the state transition function, let us first start with a
simple example.

### 1\. a simple example

In the following discussion, for simplification, I will use both notation DI-
seq and DI-rule instead of DI sequence.

Consider a permutation 1032, which is based on a DI-seq "DID", how to use it
to construct a new instance ending at **2** and based on DI-seq "DID **D** "?

 **Method** :  
step 1.  
for the original permutation `1032`, we add 1 to the digits _that are larger
than or equal to_ **2**.

    
    
     1032->1043
      ^^
    

step 2.  
then directly append **2** to `1043`, i.e., 1043 -> 1043 **2**

**Remark on step 1** :  
(1) By performing add operation, 2 in the original permutation now becomes 3,
and thus there is no duplicate element for the new arrival **2**.  
(2) More importantly, such operation on the digits **will not break the
original DI-rule**. e.g., 1043 still keeps its old DI-rule, i.e., "DID". The
proof is straight-forward, you can validate yourself.

Now a new permutation with DI-rule "DID **D** " and ending at **2** has been
constructed from 1032, namely 1043 **2**.

With the same spirit, using 1032("DID"), we can construct instances with DI-
rule "DID **D** ": 2043 **1** (ending with **1** ), 2143 **0** (ending with
**0** ).  
(Note that the instance(based on "DID **D** ") which ends with 3 can not be
constructed.)

Similarly, from 1032("DID"), we can construct instances with DI-rule "DID
**I** ": 10423(ending with **3** ), 10324(ending with **4** ).  
(Note that the instance(based on "DID **I** ") which ends with 1 or 2 can not
be constructed.)

### 2\. state transition function

With the example above in mind, the transition function seems to be clear.

Given a string DI-seq S, let `dp[i][j]` represents the number of permutation
of number `0, 1, ... , i`, satisfying DI-rule S.substr(0, i), and ending with
digit `j`.

    
    
    if(S[i-1] == 'D')
       dp[i][j] = dp[i-1][j] + dp[i-1][j+1] + ... + dp[i-1][i-1]
    
    if(S[i-1] == 'I') 
       dp[i][j] = dp[i-1][0] + dp[i-1][1] + ... + dp[i-1][j-1]
    

### 3\. Solution

    
    
    lass Solution {
    public:
        int numPermsDISequence(string S) {
            int n = S.size(), m = 1e9 + 7;
            vector<vector<int>> dp(n+1, vector<int>(n+1, 0));
            dp[0][0] = 1;
            for(int i = 1; i <= n; i++)
                for(int j = 0; j <= i; j++)
                    if(S[i-1] == 'D')
                        for(int k = j; k <= i-1; k++)
                            dp[i][j] = dp[i][j]%m + dp[i-1][k]%m;
                    else
                        for(int k = 0; k <= j-1; k++)
                            dp[i][j] = dp[i][j]%m + dp[i-1][k]%m;
            int res = 0;
            for(int i = 0; i <= n; i++)
                res = res%m + dp[n][i]%m;
            return res%m;
        }
    };
    


### Solution 3
**Top-down with Memo:**

Definition: `helper(String s, Map<String, Long> map)`: Answer to `s`.

Intuition: Insert the largest number into appropriate postion.

eg. `s='IIDD'`, we can only insert `4` between `I` and `D`. We break the
remained numbers `0, 1, 2, 3` into two groups both with the size of 2. We have
`C(4, 2)` possible combinations. Then `helper("IIDD") = helper("I") *
helper("D") * C(4, 2)`.

Tricky: How to calculate `C(n, k) % M`? I referred a method using _Pascal
Triangle_ from [here](https://www.geeksforgeeks.org/compute-ncr-p-
set-1-introduction-and-dynamic-programming-solution/). (This part makes this
method ugly and lengthy, anybody has better approaches?)

Time complexity: `O(n^4)` in my implementation, however could improve to
`O(n^3)`.

Code:

    
    
    class Solution {
        private int M = (int)1e9 + 7;
        private int[][] nCkMemo;
        public int numPermsDISequence(String S) {
            int n = S.length();
            nCkMemo = new int[n + 1][n + 1];
            return (int)helper(S, new HashMap<>());
        }
        private long helper(String s, Map<String, Long> map) {
            if (s.equals("")) {
                return 1;
            }
            if (map.containsKey(s)) {
                return map.get(s);
            }
            long result = 0;
            int n = s.length();
            if (s.charAt(0) == 'D') {
                result += helper(s.substring(1), map);
                result %= M;
            }
            if (s.charAt(n - 1) == 'I') {
                result += helper(s.substring(0, n - 1), map);
                result %= M;
            }
            for (int i = 1; i < n; i++) {
                if (s.charAt(i - 1) == 'I' && s.charAt(i) == 'D') {
                    long left = helper(s.substring(0, i - 1), map);
                    long right = helper(s.substring(i + 1), map);
                    result += (((left * right) % M) * nCk(n, i)) % M;
                    result %= M;
                }
            }
            map.put(s, result);
            return result;
        }
        private int nCk(int n, int k) {
            if (k == 0 || k == n) {
                return 1;
            }
            if (nCkMemo[n][k] == 0) {
                nCkMemo[n][k] = (nCk(n - 1, k) + nCk(n - 1, k - 1)) % M;
            }
            return nCkMemo[n][k];
        }
    }
    

**Bottom-up DP:**

Same idea with the Top-down. `dp[i][j]` represent the answer of
`s.substring(i, j)`. Just a Bottom-up implementation:

Time complexity: `O(n^3)`

    
    
    class Solution {
        private int M = (int)1e9 + 7;
        public int numPermsDISequence(String S) {
            int n = S.length();
            long[][] dp = new long[n + 1][n + 1];
            int[][] nCkMemo = new int[n + 1][n + 1];
            for (int i = 0; i <= n; i++) {
                dp[i][i] = 1;
            }
            for (int len = 1; len <= n; len++) {
                for (int i = 0; i <= n - len; i++) {
                    int j = i + len;
                    if (S.charAt(i) == 'D') {
                        dp[i][j] += dp[i + 1][j];
                        dp[i][j] %= M;
                    }
                    for (int k = i + 1; k < j; k++) {
                        if (S.charAt(k - 1) == 'I' && S.charAt(k) == 'D') {
                            dp[i][j] += (((dp[i][k - 1] * dp[k + 1][j]) % M) * nCk(len, k - i, nCkMemo)) % M;
                            dp[i][j] %= M;
                        }
                    }
                    if (S.charAt(j - 1) == 'I') {
                        dp[i][j] += dp[i][j - 1];
                        dp[i][j] %= M;
                    }
                }
            }
            return (int)dp[0][n];
        }
        private int nCk(int n, int k, int[][] nCkMemo) {
            if (k == 0 || k == n) {
                return 1;
            }
            if (nCkMemo[n][k] == 0) {
                nCkMemo[n][k] = (nCk(n - 1, k, nCkMemo) + nCk(n - 1, k - 1, nCkMemo)) % M;
            }
            return nCkMemo[n][k];
        }
    }
    

**N^3 DP:**

Let's change the definition of `dp` matrix to make the calculation simple:
let's say `dp[i][j]` represents the number of permutation of number `0, 1, ...
, i` which ends with `j`. Also, it represents the answer of `s.substring(0,
i)` which ends with `j`.  
We will have two conditions:

  1. `s.charAt(i - 1) == 'I'`: In this case, `dp[i][j] = sum(dp[i - 1][0], dp[i - 1][1], ... , dp[i - 1][j - 1])`.
  2. `s.charAt(i - 1) == 'D'`: In this case, `dp[i][j] = sum(dp[i - 1][j], dp[i - 1][j + 1], ... , dp[i - 1][i - 1])`.

Imagine each time when appending the `j` to the previous permutations, you
have to **add 1 to each number in the previous permutation which is greater
than or equals to`j`**. In this way, we keep the orders and counts of previous
permutations and cumulate.

eg. We already have permutation `(1, 0, 3, 2)`. We are trying to append `2`.
Now the `(1, 0, 3, 2)` changes to `(1, 0, 4, 3)` then appended with a `2`. We
have `(1, 0, 4, 3, 2)`. Although the values change but the order and count
don't change.

Time complexity: `O(n^3)`

Code:

    
    
    class Solution {
        public int numPermsDISequence(String S) {
            int n = S.length(), M = (int)1e9 + 7;
            int[][] dp = new int[n + 1][n + 1];
            dp[0][0] = 1;
            for (int i = 1; i <= n; i++) {
                for (int j = 0; j <= i; j++) {
                    if (S.charAt(i - 1) == 'D') {
                        for (int k = j; k < i; k++) {
                            dp[i][j] += dp[i - 1][k];
                            dp[i][j] %= M;
                        }
                    } else {
                        for (int k = 0; k < j; k++) {
                            dp[i][j] += dp[i - 1][k];
                            dp[i][j] %= M;
                        }
                    }
                }
            }
            int result = 0;
            for (int j = 0; j <= n; j++) {
                result += dp[n][j];
                result %= M;
            }
            return result;
        }
    }
    

**N^2 DP:**

Notice that in the previous method, we are actually calculate the **prefix
sum** and **suffix sum** in the two conditions:

  1. `s.charAt(i - 1) == 'I'`: In this case, `dp[i][j] = sum[i - 1][j - 1]`.
  2. `s.charAt(i - 1) == 'D'`: In this case, `dp[i][j] = sum[i - 1][i - 1] - sum[i - 1][j - 1]`.

We can define `dp[i][j]` as `sum(dp[i][0], dp[i][1], ... dp[i][j])` which is
`sum[i][j]`.

Time complexity: `O(n^2)`

Code:

    
    
    class Solution {
        public int numPermsDISequence(String S) {
            int n = S.length(), M = (int)1e9 + 7;
            int[][] dp = new int[n + 1][n + 1];
            Arrays.fill(dp[0], 1);
            for (int i = 1; i <= n; i++) {
                for (int j = 0; j <= i; j++) {
                    dp[i][j] = j == 0 ? 0 : dp[i][j - 1];
                    if (S.charAt(i - 1) == 'D') {
                        dp[i][j] += (dp[i - 1][i - 1] - (j == 0 ? 0 : dp[i - 1][j - 1])) % M;
                        if (dp[i][j] < 0) {
                            dp[i][j] += M;
                        }
                    } else {
                        dp[i][j] += j == 0 ? 0 : dp[i - 1][j - 1];
                    }
                    dp[i][j] %= M;
                }
            }
            return dp[n][n];
        }
    }
    

**O(N) space:**

Previous solution could be optimized to `O(n)` space.

Time complexity: `O(n^2)`. Space complexity: `O(n)`.

    
    
    class Solution {
        public int numPermsDISequence(String S) {
            int n = S.length(), M = (int)1e9 + 7;
            int[] dp = new int[n + 1];
            Arrays.fill(dp, 1);
            for (int i = 1; i <= n; i++) {
                int[] temp = new int[n + 1];
                for (int j = 0; j <= i; j++) {
                    temp[j] = j == 0 ? 0 : temp[j - 1];
                    if (S.charAt(i - 1) == 'D') {
                        temp[j] += (dp[i - 1] - (j == 0 ? 0 : dp[j - 1])) % M;
                        if (temp[j] < 0) {
                            temp[j] += M;
                        }
                    } else {
                        temp[j] += j == 0 ? 0 : dp[j - 1];
                    }
                    temp[j] %= M;
                }
                dp = temp;
            }
            return dp[n];
        }
    }
    



