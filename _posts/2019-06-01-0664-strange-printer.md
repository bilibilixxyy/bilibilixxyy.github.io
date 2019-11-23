---
layout: post
title: 664. Strange Printer
---
### Question
There is a strange printer with the following two special requirements:

  1. The printer can only print a sequence of the same character each time.
  2. At each turn, the printer can print new characters starting from and ending at any places, and will cover the original existing characters.

Given a string consists of lower English letters only, your job is to count
the minimum number of turns the printer needed in order to print it.

 **Example 1:**  

    
    
     **Input:** "aaabbb"
    **Output:** 2
    **Explanation:** Print "aaa" first and then print "bbb".
    

**Example 2:**  

    
    
    **Input:** "aba"
    **Output:** 2
    **Explanation:** Print "aaa" first and then print "b" from the second place of the string, which will cover the existing character 'a'.
    

**Hint** : Length of the given string will not exceed 100.

### Solution 1
The problem wants us to find the number of ways to do something without giving
specific steps like how to achieve it. This can be a typical signal that
dynamic programming may come to help.

`dp[i][j]` stands for the minimal turns we need for string from index `i` to
index `j`.  
So we have

  * `dp[i][i] = 1`: we need 1 turn to paint a single character.
  * `dp[i][i + 1]`
    * `dp[i][i + 1] = 1` if `s.chartAt(i) == s.charAt(i + 1)`
    * `dp[i][i + 1] = 2` if `s.chartAt(i) != s.charAt(i + 1)`

Then we can iteration `len` from 2 to possibly n. For each iteration, we
iteration `start` index from 0 to the farthest possible.

  * The maximum turns for `dp[start][start + len]` is `len + 1`, i.e. print one character each time.
  * We can further divide the substring to two parts: **start - > start+k** and **start+k+1 - > start+len**. It is something as following:
    
        index |start  ...  start + k| |start + k + 1 ... start + len|
    char  |  a    ...       b   | |      c       ...      b     |
    

    * As shown above, if we have `s.charAt(start + k) == s.charAt(start + len)`, we can **make it in one turn when we print this character (i.e.`b` here)**
    * This case we can reduce our turns to `dp[start][start + k] + dp[start + k + 1][start + len] - 1`

Complete codes are here

    
    
    class Solution {
        public int strangePrinter(String s) {
            if (s == null || s.length() == 0) {
                return 0;
            }
            
            int n = s.length();
            int[][] dp = new int[n][n];
            for (int i = 0; i < n; i++) {
                dp[i][i] = 1;
                if (i < n - 1) {
                    dp[i][i + 1] = s.charAt(i) == s.charAt(i + 1) ? 1 : 2;
                }
            }
            
            for (int len = 2; len < n; len++) {
                for (int start = 0; start + len < n; start++) {
                    dp[start][start + len] = len + 1;
                    for (int k = 0; k < len; k++) {
                        int temp = dp[start][start + k] + dp[start + k + 1][start + len];
                        dp[start][start + len] = Math.min(
                            dp[start][start + len],
                            s.charAt(start + k) == s.charAt(start + len) ? temp - 1 : temp
                        );
                    }
                }
            }
            
            return dp[0][n - 1];
        }
    }
    

Time complexity is `O(n^3)`

Some simple optimization. Consecutive repeating characters do not affect our
printing as we can always print them together. i.e `aaabbb` is equivalent with
`ab`. So we can reduce the string first which somehow reduce `n`

    
    
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < s.length(); i++) {
        if (i > 0 && s.charAt(i) == s.charAt(i - 1)) {
            continue;
        }
        sb.append(s.charAt(i));
    }
    s = sb.toString();
    

This helps reduce running time from 60ms to 53ms.


### Solution 2
 **Logical Thinking**

The problem is easier to solve if we decrease it to subproblems using **Divide
and Conquer**.  
 **base case** :  
if s contains only 1 character, 1 turn is needed  
if s contains 2 characters, 1 turn is needed if they equal to each other, or
else, 2 turns are needed  
 **divide the problem** :  
we keep dividing s until the substring contains 1 or 2 characters (as the
**base case** )  
Take s = "abc" for example,

    
    
      abc
     /    \
    a,bc ab,c (base case here)
    

**conquer the subproblems** :  
`turns s needed = min(turns one substring needed + turns the other needed)`
(since there are many ways to divide s, we pick the one needs minimum turns)  
Please note that, if s = "aba", and we divide it into "a,ba", turns "aba"
needed = turns "a" needed + turns "ba" needed `- 1` (aaa => aba rather than a
=> ab => aba).

To avoid duplicate calculations, we implement the idea above using **Bottom-up
Dynamic Programming**.  
**state** : `state[i][j]` turns needed to print s[i .. j] (both inclusive)  
**aim state** : `state[0][n - 1]` (n = s.length() - 1)  
**state transition** :

    
    
    state[i][i] = 1;
    state[i][i + 1] = 1 if s[i] == s[i + 1]
    state[i][i + 1] = 2 if s[i] != s[i + 1]
    state[i][j] = min(state[i][k] + state[k + 1][j]) for i <= k <= j - 1
    	please note that, if s[i] equals to s[j] , state[i][j] should -1
    

**Note**  
We observe that states defined on small sections contribute to the states
defined on the larger one.  
We define `j = i + dist` to represent sections easily.  
Take i = 2, dist = 3 for example,  
`state[2][5] = state[2][3] + state[4][5]`,  
state[4][5] should be calculated before `state[2][5]`, so **i should keep
decreasing**.  
`dist = |5 - 4|` should be counted before `dist = |5 - 2|`, so **dist should
be increasing**.

**Clear Java Code**

    
    
        public int strangePrinter(String s) {
    
             if (s == null || s.length() == 0) {
                return 0;
            }
    
            int n = s.length();
            int[][] state = new int[n][n];
    
            for (int i = 0; i < n; i++) {
                state[i][i] = 1;
            }
    
            for (int i = n - 1; i >= 0; i--) {
                for (int dist = 1; dist + i < n; dist++) {
                    int j = dist + i;
                    if (dist == 1) {
                        state[i][j] = (s.charAt(i) == s.charAt(j)) ? 1 : 2;
                        continue;
                    }
                    state[i][j] = Integer.MAX_VALUE;
                    for (int k = i; k + 1 <= j; k++) {
                        int tmp = state[i][k] + state[k + 1][j];
                        state[i][j] = Math.min(state[i][j], tmp);
                    }
                    if (s.charAt(i) == s.charAt(j)) {
                        state[i][j]--;
                    }
                }
            }
    
            return state[0][n - 1];
        }
    

**I would appreciate your VOTE UP ;)**


### Solution 3
    
    
    class Solution {
        public int strangePrinter(String s) {
            int n = s. length();
            if (n == 0) return 0;
            
            int[][] dp = new int[101][101];
            for (int i = 0; i < n; i++) dp[i][i] = 1;
            
            for (int i = 1; i < n; i++) {
                for (int j = 0; j < n - i; j++) {
                    dp[j][j + i] = i + 1;
                    for (int k = j + 1; k <= j + i; k++) {
                        int temp = dp[j][k - 1] + dp[k][j + i];
                        if (s.charAt(k - 1) == s.charAt(j + i)) temp--;
                        dp[j][j + i] = Math.min(dp[j][j + i], temp);
                    }
                }
            }
            return dp[0][n - 1];
        }
    }
    



