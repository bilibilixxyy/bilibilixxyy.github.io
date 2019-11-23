---
layout: post
title: 5.Longest Palindromic Substring
---
### Question
Given a string **s** , find the longest palindromic substring in **s**. You
may assume that the maximum length of **s** is 1000.

 **Example 1:**

    
    
     **Input:** "babad"
    **Output:** "bab"
    **Note:** "aba" is also a valid answer.
    

**Example 2:**

    
    
    **Input:** "cbbd"
    **Output:** "bb"
    
### Solution 1
The performance is pretty good, surprisingly.

    
    
    public class Solution {
    private int lo, maxLen;
    
    public String longestPalindrome(String s) {
    	int len = s.length();
    	if (len < 2)
    		return s;
    	
        for (int i = 0; i < len-1; i++) {
         	extendPalindrome(s, i, i);  //assume odd length, try to extend Palindrome as possible
         	extendPalindrome(s, i, i+1); //assume even length.
        }
        return s.substring(lo, lo + maxLen);
    }
    
    private void extendPalindrome(String s, int j, int k) {
    	while (j >= 0 && k < s.length() && s.charAt(j) == s.charAt(k)) {
    		j--;
    		k++;
    	}
    	if (maxLen < k - j - 1) {
    		lo = j + 1;
    		maxLen = k - j - 1;
    	}
    }}
### Solution 2
`dp(i, j)` represents whether `s(i ... j)` can form a palindromic substring,
`dp(i, j)` is true when `s(i)` equals to `s(j)` and `s(i+1 ... j-1)` is a
palindromic substring. When we found a palindrome, check if it's the longest
one. Time complexity O(n^2).

    
    
    public String longestPalindrome(String s) {
      int n = s.length();
      String res = null;
        
      boolean[][] dp = new boolean[n][n];
        
      for (int i = n - 1; i >= 0; i--) {
        for (int j = i; j < n; j++) {
          dp[i][j] = s.charAt(i) == s.charAt(j) && (j - i < 3 || dp[i + 1][j - 1]);
                
          if (dp[i][j] && (res == null || j - i + 1 > res.length())) {
            res = s.substring(i, j + 1);
          }
        }
      }
        
      return res;
    }
### Solution 3
    
    
    string longestPalindrome(string s) {
        if (s.empty()) return "";
        if (s.size() == 1) return s;
        int min_start = 0, max_len = 1;
        for (int i = 0; i < s.size();) {
          if (s.size() - i <= max_len / 2) break;
          int j = i, k = i;
          while (k < s.size()-1 && s[k+1] == s[k]) ++k; // Skip duplicate characters.
          i = k+1;
          while (k < s.size()-1 && j > 0 && s[k + 1] == s[j - 1]) { ++k; --j; } // Expand.
          int new_len = k - j + 1;
          if (new_len > max_len) { min_start = j; max_len = new_len; }
        }
        return s.substr(min_start, max_len);
    }

