---
layout: post
title: 730. Count Different Palindromic Subsequences
---
### Question
Given a string S, find the number of different non-empty palindromic
subsequences in S, and **return that number modulo`10^9 + 7`.**

A subsequence of a string S is obtained by deleting 0 or more characters from
S.

A sequence is palindromic if it is equal to the sequence reversed.

Two sequences `A_1, A_2, ...` and `B_1, B_2, ...` are different if there is
some `i` for which `A_i != B_i`.

 **Example 1:**  

    
    
     **Input:** 
    S = 'bccb'
    **Output:** 6
    **Explanation:** 
    The 6 different non-empty palindromic subsequences are 'b', 'c', 'bb', 'cc', 'bcb', 'bccb'.
    Note that 'bcb' is counted only once, even though it occurs twice.
    

**Example 2:**  

    
    
    **Input:** 
    S = 'abcdabcdabcdabcdabcdabcdabcdabcddcbadcbadcbadcbadcbadcbadcbadcba'
    **Output:** 104860361
    **Explanation:** 
    There are 3104860382 different non-empty palindromic subsequences, which is 104860361 modulo 10^9 + 7.
    

**Note:**

* The length of `S` will be in the range `[1, 1000]`.
* Each character `S[i]` will be in the set `{'a', 'b', 'c', 'd'}`.

### Solution 1
I am not able to pass this question one time but struggle a lot in the basic
test cases like "a", "aa", "aaa", "aba", "aabb". Those test cases help my
early rough idea to be flawless. The basic idea of DP is easy to understand, I
maintain DP[i][j] to record in substring from i to j(included), the number of
palindrome without duplicate. Then we consider two cases of the DP equation:

when s.charAt(i) != s.charAt(j):  
dp[i][j] = dp[i][j] = dp[i][j - 1] + dp[i + 1][j] - dp[i + 1][j - 1];

When s.charAt(i) == s.charAt(j):  
 **the situation get much more complex and I fix a lot the wrong answers. I
have comment the branches where which kind of test cases are considered.**

    
    
    class Solution {
        public int countPalindromicSubsequences(String s) {
            int len = s.length();
            int [][] dp = new int[len][len];
    
            char[] chs = s.toCharArray();
            for(int i = 0; i < len; i++){
                dp[i][i] = 1;   // Consider the test case "a", "b" "c"...
            }
    
            for(int distance = 1; distance < len; distance++){
                for(int i = 0; i < len - distance; i++){
                    int j = i + distance;
                    if(chs[i] == chs[j]){
                        int low = i + 1;
                        int high = j - 1;
    
                  /* Variable low and high here are used to get rid of the duplicate*/
    
                        while(low <= high && chs[low] != chs[j]){
                            low++;
                        }
                        while(low <= high && chs[high] != chs[j]){
                            high--;
                        }
                        if(low > high){
                            // consider the string from i to j is "a...a" "a...a"... where there is no character 'a' inside the leftmost and rightmost 'a'
                           /* eg:  "aba" while i = 0 and j = 2:  dp[1][1] = 1 records the palindrome{"b"}, 
                             the reason why dp[i + 1][j  - 1] * 2 counted is that we count dp[i + 1][j - 1] one time as {"b"}, 
                             and additional time as {"aba"}. The reason why 2 counted is that we also count {"a", "aa"}. 
                             So totally dp[i][j] record the palindrome: {"a", "b", "aa", "aba"}. 
                             */ 
    
                            dp[i][j] = dp[i + 1][j - 1] * 2 + 2;  
                        } 
                        else if(low == high){
                            // consider the string from i to j is "a...a...a" where there is only one character 'a' inside the leftmost and rightmost 'a'
                           /* eg:  "aaa" while i = 0 and j = 2: the dp[i + 1][j - 1] records the palindrome {"a"}.  
                             the reason why dp[i + 1][j  - 1] * 2 counted is that we count dp[i + 1][j - 1] one time as {"a"}, 
                             and additional time as {"aaa"}. the reason why 1 counted is that 
                             we also count {"aa"} that the first 'a' come from index i and the second come from index j. So totally dp[i][j] records {"a", "aa", "aaa"}
                            */
                            dp[i][j] = dp[i + 1][j - 1] * 2 + 1;  
                        }
                        else{
                            // consider the string from i to j is "a...a...a... a" where there are at least two character 'a' close to leftmost and rightmost 'a'
                           /* eg: "aacaa" while i = 0 and j = 4: the dp[i + 1][j - 1] records the palindrome {"a",  "c", "aa", "aca"}. 
                              the reason why dp[i + 1][j  - 1] * 2 counted is that we count dp[i + 1][j - 1] one time as {"a",  "c", "aa", "aca"}, 
                              and additional time as {"aaa",  "aca", "aaaa", "aacaa"}.  Now there is duplicate :  {"aca"}, 
                              which is removed by deduce dp[low + 1][high - 1]. So totally dp[i][j] record {"a",  "c", "aa", "aca", "aaa", "aaaa", "aacaa"}
                              */
                            dp[i][j] = dp[i + 1][j - 1] * 2 - dp[low + 1][high - 1]; 
                        }
                    }
                    else{
                        dp[i][j] = dp[i][j - 1] + dp[i + 1][j] - dp[i + 1][j - 1];  //s.charAt(i) != s.charAt(j)
                    }
                    dp[i][j] = dp[i][j] < 0 ? dp[i][j] + 1000000007 : dp[i][j] % 1000000007;
                }
            }
    
            return dp[0][len - 1];
        }
    }
    


### Solution 2
    
    
    class Solution {
        int div=1000000007;
        public int countPalindromicSubsequences(String S) {    
            TreeSet[] characters = new TreeSet[26];
            int len = S.length();
            
            for (int i = 0; i < 26; i++) characters[i] = new TreeSet<Integer>();
            
            for (int i = 0; i < len; ++i) {
                int c = S.charAt(i) - 'a';
                characters[c].add(i);
            }
            Integer[][] dp = new Integer[len+1][len+1];
             return memo(S,characters,dp, 0, len);
        }
        
        public int memo(String S,TreeSet<Integer>[] characters,Integer[][] dp,int start,int end){
            if (start >= end) return 0;
            if(dp[start][end]!=null) return dp[start][end];
           
                long ans = 0;
                
                for(int i = 0; i < 26; i++) {
                    Integer new_start = characters[i].ceiling(start);
                    Integer new_end = characters[i].lower(end);
                  if (new_start == null || new_start >= end) continue;
                     ans++;
                    if (new_start != new_end) ans++;
                    ans+= memo(S,characters,dp,new_start+1,new_end);
                    
                }
                dp[start][end] = (int)(ans%div);
                return dp[start][end];
        }
        
    }
    


### Solution 3
Let dp[len][i][x] be the number of distinct palindromes of the subtring
starting at i of length len, where the first (and last) character is x. The DP
formula is simple :

  * If s[i] != x, then dp[len][i][x] = dp[len-1][i+1][x] (ignoring the first character in this window)
  * If s[i+len-1] != x then dp[len][i][x] = dp[len-1][i][x] (ignoring the last character in this window)
  * If both the first and last characters are x, then we need to count the number of distinct palindromes in the sub-window from i+1 to i + len -2. Need to be careful with how we count empty string.  
Since we only need to subproblems of length len-1, len-2, we only need to
remember the solutions for the subproblems of length len, len-1, len-2. This
is needed to pass the max test case.

    
    
    class Solution {
    public:
        int countPalindromicSubsequences(string s) {
            int md = 1000000007;
            int n = s.size();
            int dp[3][n][4];
            for (int len = 1; len <=n; ++len) {
                for (int i = 0; i + len <=n; ++i) for (int x = 0; x < 4; ++x)  {
                    int &ans = dp[2][i][x];
                    ans = 0;
                    int j = i + len - 1;
                    char c = 'a' + x;
                    if (len == 1) ans = s[i] == c;
                    else {
                        if (s[i] != c) ans = dp[1][i+1][x];
                        else if (s[j] != c) ans = dp[1][i][x];
                        else {
                            ans = 2;
                            if (len > 2) for (int y = 0; y < 4;++y) {
                                ans += dp[0][i+1][y];
                                ans %=md;
                            }
                        }
                    }
                }
                for (int i=0;i<2;++i) for (int j = 0; j < n; ++j) for (int x=0; x < 4;++x)
                    dp[i][j][x] = dp[i+1][j][x];
            }
            int ret = 0;
            for (int x = 0; x < 4;++x) ret = (ret + dp[2][0][x]) %md;
            return ret;
        }
    };
    



