---
layout: post
title: 28. Implement strStr()
---
### Question
Implement [strStr()](http://www.cplusplus.com/reference/cstring/strstr/).

Return the index of the first occurrence of needle in haystack, or **-1** if
needle is not part of haystack.

 **Example 1:**

    
    
     **Input:** haystack = "hello", needle = "ll"
    **Output:** 2
    

**Example 2:**

    
    
    **Input:** haystack = "aaaaa", needle = "bba"
    **Output:** -1
    

**Clarification:**

What should we return when `needle` is an empty string? This is a great
question to ask during an interview.

For the purpose of this problem, we will return 0 when `needle` is an empty
string. This is consistent to C's
[strstr()](http://www.cplusplus.com/reference/cstring/strstr/) and Java's
[indexOf()](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#indexOf\(java.lang.String\)).

### Solution 1
    
    
    public int strStr(String haystack, String needle) {
      for (int i = 0; ; i++) {
        for (int j = 0; ; j++) {
          if (j == needle.length()) return i;
          if (i + j == haystack.length()) return -1;
          if (needle.charAt(j) != haystack.charAt(i + j)) break;
        }
      }
    }


### Solution 2
    
    
    public class Solution {
        public int strStr(String haystack, String needle) {
            int l1 = haystack.length(), l2 = needle.length();
            if (l1 < l2) {
                return -1;
            } else if (l2 == 0) {
                return 0;
            }
            int threshold = l1 - l2;
            for (int i = 0; i <= threshold; ++i) {
                if (haystack.substring(i,i+l2).equals(needle)) {
                    return i;
                }
            }
            return -1;
        }
    }


### Solution 3
    
    
    class Solution(object):
    def strStr(self, haystack, needle):
        """
        :type haystack: str
        :type needle: str
        :rtype: int
        """
        for i in range(len(haystack) - len(needle)+1):
            if haystack[i:i+len(needle)] == needle:
                return i
        return -1



