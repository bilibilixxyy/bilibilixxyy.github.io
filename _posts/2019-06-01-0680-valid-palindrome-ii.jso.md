---
layout: post
title: 680. Valid Palindrome II
---
### Question
Given a non-empty string `s`, you may delete **at most** one character. Judge
whether you can make it a palindrome.

 **Example 1:**  

    
    
     **Input:** "aba"
    **Output:** True
    

**Example 2:**  

    
    
    **Input:** "abca"
    **Output:** True
    **Explanation:** You could delete the character 'c'.
    

**Note:**  

  1. The string will only contain lowercase characters a-z.The maximum length of the string is 50000.

### Solution 1
    
    
    public boolean validPalindrome(String s) {
        int l = -1, r = s.length();
        while (++l < --r) 
            if (s.charAt(l) != s.charAt(r)) return isPalindromic(s, l, r+1) || isPalindromic(s, l-1, r);
        return true;
    }
    
    public boolean isPalindromic(String s, int l, int r) {
        while (++l < --r) 
            if (s.charAt(l) != s.charAt(r)) return false;
        return true;
    }
    


### Solution 2
We can use the standard two-pointer approach that starts at the left and right
of the string and move inwards. Whenever there is a mismatch, we can either
exclude the character at the left or the right pointer. We then take the two
remaining substrings and compare against its reversed and see if either one is
a palindrome.

 _\- Yangshun_

    
    
    class Solution(object):
        def validPalindrome(self, s):
             """
            :type s: str
            :rtype: bool
            """
            # Time: O(n)
            # Space: O(n)
            left, right = 0, len(s) - 1
            while left < right:
                if s[left] != s[right]:
                    one, two = s[left:right], s[left + 1:right + 1]
                    return one == one[::-1] or two == two[::-1]
                left, right = left + 1, right - 1
            return True
    


### Solution 3
Follow normal way (two pointers) to check if `s` is palindrome. When two chars
are not equal, try to skip (pseudo `delete`) either left one or right one and
continue checking.

    
    
    class Solution {
        public boolean validPalindrome(String s) {
            int i = 0, j = s.length() - 1;
            while (i < j && s.charAt(i) == s.charAt(j)) {
                i++; j--;
            }
    
            if (i >= j) return true;
    
            if (isPalin(s, i + 1, j) || isPalin(s, i, j - 1)) return true;
            return false;
        }
    
        private boolean isPalin(String s, int i, int j) {
            while (i < j) {
                if (s.charAt(i) == s.charAt(j)) {
                    i++; j--;
                }
                else return false;
            }
            return true;
        }
    }
    



