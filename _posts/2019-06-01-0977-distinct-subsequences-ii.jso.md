---
layout: post
title: 977. Distinct Subsequences II
---
### Question
Given a string `S`, count the number of distinct, non-empty subsequences of
`S` .

Since the result may be large, **return the answer modulo`10^9 + 7`**.



 **Example 1:**

    
    
     **Input:** "abc"
    **Output:** 7
    **Explanation** : The 7 distinct subsequences are "a", "b", "c", "ab", "ac", "bc", and "abc".
    

**Example 2:**

    
    
    **Input:** "aba"
    **Output:** 6
    **Explanation** : The 6 distinct subsequences are "a", "b", "ab", "ba", "aa" and "aba".
    

**Example 3:**

    
    
    **Input:** "aaa"
    **Output:** 3
    **Explanation** : The 3 distinct subsequences are "a", "aa" and "aaa".
    





 **Note:**

  1. `S` contains only lowercase letters.
  2. `1 <= S.length <= 2000`

### Solution 1
More detailed chinese solution [here](https://www.jianshu.com/p/02501f516437)
explaining how I come up with it.

## Explanation

Init an array `endswith[26]`  
`endswith[i]` to count how many sub sequence that ends with `i`th character.

Now we have `N = sum(endswith)` different sub sequence,  
add a new character `c` to each of them,  
then we have `N` different sub sequence that ends with `c`.

With this idea, we loop on the whole string `S`,  
and we update `end[c] = sum(end) + 1` for each character.

We need to plus one here, because `"c"` itself is also a sub sequence.

## Example

Input: `"aba"`  
Current parsed: `"ab"`

endswith `'a'`: `["a"]`  
endswith `'b'`: `["ab","b"]`

`"a"` -> `"aa"`  
`"ab"` -> `"aba"`  
`"b"` -> `"ba"`  
`""` -> `"a"`

endswith `'a'`: `["aa","aba","ba","a"]`  
endswith `'b'`: `["ab","b"]`  
result: 6

## Complexity

Time `O(26N)`, Space `O(1)`.

 **C++:**

    
    
        int distinctSubseqII(string S) {
            long endsWith[ 26] = {}, mod = 1e9 + 7;
            for (char c : S)
                endsWith[c - 'a'] = accumulate(begin(endsWith), end(endsWith), 1L) % mod;
            return accumulate(begin(endsWith), end(endsWith), 0L) % mod;
        }
    

**Java:**

    
    
        public  int distinctSubseqII(String S) {
            long end[] = new long[26], mod = (long)1e9 + 7;
            for (char c : S.toCharArray())
                end[c - 'a'] = Arrays.stream(end).sum()%mod + 1;
            return (int)(Arrays.stream(end).sum() % mod);
        }
    

**Python:**

    
    
         def distinctSubseqII(self, S):
            end = [0] * 26
            for c in S:
                end[ord(c) - ord('a')] = sum(end) + 1
            return sum(end) % (10**9 + 7)
    

Use a variable to count the `sum(end)` can avoid repeatly count it.  
Improve time complexity from `O(26N)` to `O(N)`, if you want.

**C++:**

    
    
         int distinctSubseqII(string S) {
            int res = 0, added = 0, mod = 1e9 + 7, endsWith[26] = {};
            for (char c : S) {
                added = (res - endsWith[c - 'a'] + 1) % mod;
                res = (res + added) % mod;
                endsWith[c - 'a'] = (endsWith[c - 'a'] + added) % mod;
            }
            return (res + mod) % mod;
        }
    

**Java:**

    
    
        public  int distinctSubseqII(String S) {
            int end[] = new int[26], res = 0, added = 0, mod = (int)1e9 + 7;
            for (char c : S.toCharArray()) {
                added = (res + 1 - end[c - 'a']) % mod;
                res = (res + added) % mod;
                end[c - 'a'] = (end[c - 'a'] + added) % mod;
            }
            return (res + mod) % mod;
        }
    

**Python:**

    
    
        def distinctSubseqII(self, S):
            res, end =  0, collections.Counter()
            for c in S:
                res, end[c] = res * 2 + 1 - end[c], res + 1
            return res % (10**9 + 7)
    


### Solution 2
`dp[i]` represents the count of unique subsequence ends with S[i].  
`dp[i]` is initialized to `1` for `S[0 ... i]`  
For each `dp[i]`, we define `j` from `0` to `i - 1`, we have:

  1. if `s[j] != s[i]`, `dp[i] += dp[j]`
  2. if `s[j] == s[i]`, do nothing to avoid duplicates.

Then `result = sum(dp[0], ... dp[n - 1])`  
Time complexity: `O(n^2)`

    
    
    class Solution {
        public int distinctSubseqII(String S) {
            int n = S.length(), M = (int)1e9 + 7, result = 0;
            int[] dp = new int[n];
            Arrays.fill(dp, 1);
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < i; j++) {
                    if (S.charAt(j) != S.charAt(i)) {
                        dp[i] += dp[j];
                        dp[i] %= M;
                    }
                }
                result += dp[i];
                result %= M;
            }
            return result;
        }
    }
    

Furthermore, we can use a `sum` to represent `sum(dp[0], ..., dp[i - 1])`.  
And also a `count` array, in which `count[S.charAt(i) - 'a']` represents the
count of presented subsequence ends with `S.charAt(i)`.  
Then `dp[i] = sum - count[S.charAt(i) - 'a']`.  
Time complexity: `O(n)`

    
    
    class Solution {
        public int distinctSubseqII(String S) {
            int n = S.length(), M = (int)1e9 + 7;
            int[] dp = new int[n];
            Arrays.fill(dp, 1);
            int[] count = new int[26];
            int sum = 0;
            for (int i = 0; i < n; i++) {
                int index = S.charAt(i) - 'a';
                dp[i] += sum - count[index];
                dp[i] = (dp[i] + M) % M;
                sum = (sum + dp[i]) % M;
                count[index] = (count[index] + dp[i]) % M;
            }
            return sum;
        }
    }
     
    

Optimize to `O(1)` space:

    
    
    class Solution {
        public int distinctSubseqII(String S) {
            int n = S.length(), M = (int)1e9 + 7;
            int[] count = new int[26];
            int sum = 0;
            for (int i = 0; i < n; i++) {
                int index = S.charAt(i) - 'a';
                int cur = (1 + sum - count[index] + M) % M;
                sum = (sum + cur) % M;
                count[index] = (count[index] + cur) % M;
            }
            return sum;
        }
    }
     
     
    


### Solution 3
This solution is from Geeks4Geeks's [Count Distinct
Subsequences](https://www.geeksforgeeks.org/count-distinct-subsequences/). It
requires O(n) memory though. The idea is that the next character doubles the
running count. If it's a repeated character, we also need to substract the
previous count for that character. Note the modular subtraction `a + mod - b`,
instead of just `a - b`. It caused me a lot of troubles during the contest!

    
    
    int distinctSubseqII(string S) {
        int pos[26] = {}, dp[S.size() + 1] = { 1 }, mod = 1e9 + 7;
        for (int i = 1; i <= S.size(); ++i) 
        {
            auto idx = S[i - 1] - 'a';
            if (pos[idx] != 0) dp[i] = mod - dp[pos[idx] - 1];
            dp[i] = (dp[i] + dp[i - 1] * 2 % mod) % mod;
            pos[S[i - 1] - 'a'] = i;
        } 
        return dp[S.size()] - 1; 
    } 
    

This solution can be simplified by storing counts for each character directly
in `pos`, and doubling/subtracting from the total count. This brings the
memory complexity to O(1):

    
    
    int distinctSubseqII(string S) {
        int pos[26] = {}, mod = 1e9 + 7, sum = 1;
        for (auto ch : S) {
            auto old_sum = sum;
            sum = (mod + sum * 2 % mod - pos[ch - 'a']) % mod;
            pos[ch - 'a'] = old_sum;
        } 
        return sum - 1; 
    }  
    



