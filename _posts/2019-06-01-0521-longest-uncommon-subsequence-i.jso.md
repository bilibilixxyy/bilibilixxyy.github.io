---
layout: post
title: 521. Longest Uncommon Subsequence I 
---
### Question
Given a group of two strings, you need to find the longest uncommon
subsequence of this group of two strings.The longest uncommon subsequence is
defined as the longest subsequence of one of these strings and this
subsequence should not be **any** subsequence of the other strings.

A **subsequence** is a sequence that can be derived from one sequence by
deleting some characters without changing the order of the remaining elements.
Trivially, any string is a subsequence of itself and an empty string is a
subsequence of any string.

The input will be two strings, and the output needs to be the length of the
longest uncommon subsequence. If the longest uncommon subsequence doesn't
exist, return -1.

 **Example 1:**  

    
    
     **Input:** "aba", "cdc"
    **Output:** 3
    **Explanation:** The longest uncommon subsequence is "aba" (or "cdc"),   
     because "aba" is a subsequence of "aba",   
    but not a subsequence of any other strings in the group of two strings. 
    

**Note:**

  1. Both strings' lengths will not exceed 100.
  2. Only letters from a ~ z will appear in input strings. 

### Solution 1
I know this problem may seem obviously trivial for many programming masters,
but not for me; actually I was really over-thinking into it and wondering why
it's only a 3-point problem.

To the problem contributor: you really got me this time! (if the baffling
problem description is intentional)

Anyone has the same feeling?


### Solution 2
    
    
    public int findLUSlength(String a, String b) {
        return a.equals(b) ? -1 : Math.max(a.length(), b.length());
    }
    


### Solution 3
For strings **A** , **B** , when len( **A** ) > len( **B** ), the longest
possible subsequence of either **A** or **B** is **A** , and no subsequence of
**B** can be equal to **A**. Answer: len( **A** ).

When len( **A** ) == len( **B** ), the only subsequence of **B** equal to
**A** is **B** ; so as long as **A** != **B** , the answer remains len( **A**
).

When **A** == **B** , any subsequence of **A** can be found in **B** and vice
versa, so the answer is -1.

    
    
     def findLUSlength(self, A, B):
        if A == B:
            return -1
        return max(len(A), len(B))
    



