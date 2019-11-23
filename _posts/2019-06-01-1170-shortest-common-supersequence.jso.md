---
layout: post
title: 1170. Shortest Common Supersequence
---
### Question
Given two strings `str1` and `str2`, return the shortest string that has both
`str1` and `str2` as subsequences.  If multiple answers exist, you may return
any of them.

 _(A string S is a subsequence of string T if deleting some number of
characters from T (possibly 0, and the characters are chosen _anywhere_ from
T) results in the string S.)_



 **Example 1:**

    
    
     **Input:** str1 = "abac", str2 = "cab"
    **Output:** "cabac"
    **Explanation:**
    str1 = "abac" is a substring of "cabac" because we can delete the first "c".
    str2 = "cab" is a substring of "cabac" because we can delete the last "ac".
    The answer provided is the shortest such string that satisfies these properties.
    



 **Note:**

  1. `1 <= str1.length, str2.length <= 1000`
  2. `str1` and `str2` consist of lowercase English letters.

### Solution 1
Video explanation on the
[Youtube](https://www.youtube.com/watch?v=aUrgjK7-Fu4)

##  **Intuition**

Change the problem to fin the LCS  
  

##  **Complexity**

TIme O(MN) dp,  
Space O(MN) * O(string), but actually we can also save the storage of string.  
  

 **C++:**

    
    
        string shortestCommonSupersequence(string& A, string& B) {
            int  i = 0, j = 0;
            string res = "";
            for (char c : lcs(A, B)) {
                while (A[i] != c)
                    res += A[i++];
                while (B[j] != c)
                    res += B[j++];
                res += c, i++, j++;
            }
            return res + A.substr(i) + B.substr(j);
        }
    
        string lcs(string& A, string& B) {
            int n = A.size(), m = B.size();
            vector<vector<string>> dp(n + 1, vector<string>(m + 1, ""));
            for (int i = 0; i < n; ++i)
                for (int j = 0; j < m; ++j)
                    if (A[i] == B[j])
                        dp[i + 1][j + 1] = dp[i][j] + A[i];
                    else
                        dp[i + 1][j + 1] = dp[i + 1][j].size() > dp[i][j + 1].size() ?  dp[i + 1][j] : dp[i][j + 1];
            return dp[n][m];
        }
    

**Python:**

    
    
        def shortestCommonSupersequence(self, A, B):
            def lcs(A, B):
                n, m = len(A), len(B)
                dp = [[ "" for _ in xrange(m + 1)] for _ in range(n + 1)]
                for i in range(n):
                    for j in range(m):
                        if A[i] == B[j]:
                            dp[i + 1][j + 1] = dp[i][j] + A[i]
                        else:
                            dp[i + 1][j + 1] = max(dp[i + 1][j], dp[i][j + 1], key=len)
                return dp[-1][-1]
    
            res, i, j = "", 0, 0
            for c in lcs(A, B):
                while A[i] != c:
                    res += A[i]
                    i += 1
                while B[j] != c:
                    res += B[j]
                    j += 1
                res += c
                i, j = i + 1, j + 1
            return res + A[i:] + B[j:]
    


### Solution 2
DP Recurrence:

Let str1[0..m - 1] and str2[0..n - 1] be two strings with lengths m and n .

if (m == 0) return n;  
if (n == 0) return m;

// If last characters are same, then add 1 to result and recur for str1[]  
if (str1.charAt(m - 1) == str2.charAt(n - 1))  
return 1 + shortestCommonSupersequence(str1, str2, m - 1, n - 1);

// Else find shortest of following two  
// a) Remove last character from str1 and recur  
// b) Remove last character from str2 and recur  
else return 1 + min( shortestCommonSupersequence(str1, str2, m - 1, n),
shortestCommonSupersequence(str1, str2, m, n - 1) );

    
    
    public String shortestCommonSupersequence(String str1, String str2) {
            int m = str1.length();
            int n = str2.length();
    
            int[][] dp = new int[m + 1][n + 1];
    
            for (int i = 0; i <= m; i++)
            {
                for (int j = 0; j <= n; j++)
                {
                    if (i == 0)
                        dp[i][j] = j;
                    else if (j == 0)
                        dp[i][j] = i;
                    else if (str1.charAt(i - 1) == str2.charAt(j - 1))
                        dp[i][j] = 1 + dp[i - 1][j - 1];
                    else
                        dp[i][j] = 1 + Math.min(dp[i - 1][j],
                                dp[i][j - 1]);
                }
            }
    
            int l = dp[m][n]; // Length of the ShortestSuperSequence
            char[] arr = new char[l];
            int i=m, j=n;
            while(i>0 && j>0)
            {
                /* If current character in str1 and str2 are same, then
                 current character is part of shortest supersequence */
                if(str1.charAt(i-1) == str2.charAt(j-1)) {
                    arr[--l] = str1.charAt(i-1);
                    i--;j--;
                }else if(dp[i-1][j]<dp[i][j-1]) {
                    arr[--l] = str1.charAt(i-1);
                    i--;
                }
                else {
                    arr[--l] = str2.charAt(j-1);
                    j--;
                }
            }
            while (i > 0) {
                arr[--l] = str1.charAt(i-1);
                i--;
            }
            while (j > 0) {
                arr[--l] = str2.charAt(j-1);
                j--;
            }
            return new String(arr);
        }
    


### Solution 3
  1. Find LCS;  
e.g, `s1 = "XABCBDAB", s2 = "yBDCABA"`, for `LCS DP` computation, please refer
to the following picture, which will also be helpful to understand the
building process for the final result.

![image](https://assets.leetcode.com/users/rock/image_1560675195.png)

  2. Reversely append the chars to StringBuilder, if the char is among the LCS, choose either one between the two strings.  
a) start from `i = m - 1` and `j = n - 1`, check if the corresponding chars
are equal, that is, s1.charAt(i) == s2.charAt(j); if yes, append either of
them; if no, append the char with larger `dp` value.  
b) whether s1 or s2 reaches left end first, continue to append remaining chars
in the other string.

    
    
        public String shortestCommonSupersequence(String s1, String s2) {
    
            // find LCS.
            int m = s1.length(), n = s2.length();
            int[][] dp = new int[m + 1][n + 1];
            for (int i = 0; i < m; ++i) {
                for (int j = 0; j < n; ++j) {
                    if (s1.charAt(i) == s2.charAt(j)) {
                        dp[i + 1][j + 1] = 1 + dp[i][j];    
                    }else {
                        dp[i + 1][j + 1] = Math.max(dp[i + 1][j], dp[i][j + 1]);
                    }
                }
            }
    		
            // Build result.
            StringBuilder sb = new StringBuilder();
            int i = m - 1, j = n - 1;
            while (i >= 0 || j >= 0) { 
                if (i < 0 ^ j < 0) { // only one string reaches left end.
                    char c = i < 0 ? s2.charAt(j--) : s1.charAt(i--); // remaining chars in the other string.
                    sb.append(c);
                }else if (s1.charAt(i) == s2.charAt(j)) { // common char in LCS.
                    sb.append(s1.charAt(i)); // append the char of either s1 or s2.
                    --i; --j;  
                }else { // the char is not in LCS.
                    char c = dp[i][j + 1] > dp[i + 1][j] ? s1.charAt(i--) : s2.charAt(j--); // the char corresponding to larger dp value.
                    sb.append(c);
                }
            } 
            return sb.reverse().toString();
        }
    

**Analysis:**

Time & space:: O(m * n), where m = s1.length(), n = s2.length().



