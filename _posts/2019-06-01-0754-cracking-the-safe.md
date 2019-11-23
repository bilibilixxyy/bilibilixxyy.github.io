---
layout: post
title: 754. Cracking the Safe
---
### Question
There is a box protected by a password. The password is a sequence of `n`
digits where each digit can be one of the first `k` digits `0, 1, ..., k-1`.

While entering a password, the last `n` digits entered will automatically be
matched against the correct password.

For example, assuming the correct password is `"345"`, if you type `"012345"`,
the box will open because the correct password matches the suffix of the
entered password.

Return any password of **minimum length** that is guaranteed to open the box
at some point of entering it.



 **Example 1:**

    
    
     **Input:** n = 1, k = 2
    **Output:** "01"
    **Note:** "10" will be accepted too.
    

**Example 2:**

    
    
    **Input:** n = 2, k = 2
    **Output:** "00110"
    **Note:** "01100", "10011", "11001" will be accepted too.
    



 **Note:**

  1. `n` will be in the range `[1, 4]`.
  2. `k` will be in the range `[1, 10]`.
  3. `k^n` will be at most `4096`.

### Solution 1
<https://www.youtube.com/watch?v=iPLQgXUiU14>

This video helped a lot, combined with spending some time to reprove it on my
own, in my own words!


### Solution 2
In order to guarantee to open the box at last, the input password ought to
contain all length-n combinations on digits [0..k-1] - there should be `k^n`
combinations in total.

To make the input password as short as possible, we'd better make each
possible length-n combination on digits [0..k-1] occurs **exactly once** as a
substring of the password. The existence of such a password is proved by [De
Bruijn sequence](https://en.wikipedia.org/wiki/De_Bruijn_sequence#Uses):

 _A de Bruijn sequence of order n on a size-k alphabet A is a cyclic sequence
in which every possible length-n string on A occurs exactly once as a
substring. It has length k^n, which is also the number of distinct substrings
of length n on a size-k alphabet; de Bruijn sequences are therefore optimally
short._

We reuse last n-1 digits of the input-so-far password as below:

    
    
    e.g., n = 2, k = 2
    all 2-length combinations on [0, 1]: 
    00 (`00`110), 
     01 (0`01`10), 
      11 (00`11`0), 
       10 (001`10`)
       
    the password is 00110
    

We can utilize **DFS** to find the password:

> **goal** : to find the shortest input password such that each possible
n-length combination of digits [0..k-1] occurs exactly once as a substring.

> **node** : current input password

> **edge** : if the last n - 1 digits of `node1` can be transformed to `node2`
by appending a digit from `0..k-1`, there will be an edge between `node1` and
`node2`

> **start node** : n repeated 0's  
>  **end node** : all n-length combinations among digits 0..k-1 are visited

> **visitedComb** : all combinations that have been visited

* * *
    
    
        public String crackSafe(int n, int k) {
            // Initialize pwd to n repeated 0's as the start node of DFS.
            String strPwd = String.join("", Collections.nCopies(n, "0"));
            StringBuilder sbPwd = new StringBuilder(strPwd);
            
            Set<String> visitedComb = new HashSet<>();
            visitedComb.add(strPwd);
        
            int targetNumVisited = (int) Math.pow(k, n);
            
            crackSafeAfter(sbPwd, visitedComb, targetNumVisited, n, k);
            
            return sbPwd.toString();
        }
        
        private boolean crackSafeAfter(StringBuilder pwd, Set<String> visitedComb, int targetNumVisited, int n, int k) {
            // Base case: all n-length combinations among digits 0..k-1 are visited. 
            if (visitedComb.size() == targetNumVisited) {
                return true;
            }
            
            String lastDigits = pwd.substring(pwd.length() - n + 1); // Last n-1 digits of pwd.
            for (char ch = '0'; ch < '0' + k; ch++) {
                String newComb = lastDigits + ch;
                if (!visitedComb.contains(newComb))  {
                    visitedComb.add(newComb);
                    pwd.append(ch);
                    if (crackSafeAfter(pwd, visitedComb, targetNumVisited, n, k)) {
                        return true;
                    }
                    visitedComb.remove(newComb);
                    pwd.deleteCharAt(pwd.length() - 1);
                }
            }
            
            return false;
        }
    

**(人 •͈ᴗ•͈)** Thanks for voting!


### Solution 3
This is kinda greedy approach.  
So straight up we can tell that we have k^n combinations of the lock.  
So the best way to generate the string is reusing last n-1 digits of previous
lock << I can't really prove this but I realized this after writing down some
examples.

Hence, we'll use dfs to generate the string with goal is when our string
contains all possible combinations.

    
    
    class Solution {
        public String crackSafe(int n, int k) {
            StringBuilder sb = new StringBuilder();
            int total = (int) (Math.pow(k, n));
            for (int i = 0; i < n; i++) sb.append('0');
    
            Set<String> visited = new HashSet<>();
            visited.add(sb.toString());
    
            dfs(sb, total, visited, n, k);
    
            return sb.toString();
        }
    
        private boolean dfs(StringBuilder sb, int goal, Set<String> visited, int n, int k) {
            if (visited.size() == goal) return true;
            String prev = sb.substring(sb.length() - n + 1, sb.length());
            for (int i = 0; i < k; i++) {
                String next = prev + i;
                if (!visited.contains(next)) {
                    visited.add(next);
                    sb.append(i);
                    if (dfs(sb, goal, visited, n, k)) return true;
                    else {
                        visited.remove(next);
                        sb.delete(sb.length() - 1, sb.length());
                    }
                }
            }
            return false;
        }
    
    }
    



