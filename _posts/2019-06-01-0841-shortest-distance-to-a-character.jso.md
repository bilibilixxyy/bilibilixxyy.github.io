---
layout: post
title: 841. Shortest Distance to a Character
---
### Question
Given a string `S` and a character `C`, return an array of integers
representing the shortest distance from the character `C` in the string.

 **Example 1:**

    
    
     **Input:** S = "loveleetcode", C = 'e'
    **Output:** [3, 2, 1, 0, 1, 0, 0, 1, 2, 2, 1, 0]
    



 **Note:**

  1. `S` string length is in `[1, 10000].`
  2. `C` is a single character, and guaranteed to be in string `S`.
  3. All letters in `S` and `C` are lowercase.

### Solution 1
 **Explanation**  
Initial result array.  
Loop twice on the string `S`.  
First forward pass to find shortest distant to character on left.  
Second backward pass to find shortest distant to character on right.

 **Note**  
In python solution, I merged these two `for` statement.  
I can do the same in C++/Java by:

    
    
    for (int i = 0; i >= 0; res[n-1] == n ? ++i : --i)
    

But it will become less readable.

**Time Complexity** O(N)

**C++**

    
    
        vector<int> shortestToChar(string S, char C) {
            int n = S. size();
            vector<int> res(n, n);
            int pos = -n;
            for (int i = 0; i < n; ++i) {
                if (S[i] == C) pos = i;
                res[i] = min(res[i], abs(i - pos));
            }
            for (int i = n - 1; i >= 0; --i) {
                if (S[i] == C)  pos = i;
                res[i] = min(res[i], abs(i - pos));
            }
            return res;
        }
    

**Java**

    
    
        public  int[] shortestToChar(String S, char C) {
            int n = S.length();
            int[] res = new int[n];
            int pos = -n;
            for (int i = 0; i < n; ++i) {
                if (S.charAt(i) == C) pos = i;
                res[i] = i - pos;
            }
            for (int i = n - 1; i >= 0; --i) {
                if (S.charAt(i) == C)  pos = i;
                res[i] = Math.min(res[i], Math.abs(i - pos));
            }
            return res;
        }
    

**Python**

    
    
        def shortestToChar(self, S, C):
            n =  len(S)
            res = [n] * n
            pos = -n
            for i in range(n) + range(n)[::-1]:
                if S[i] == C: pos = i
                res[i] = min(res[i], abs(i - pos))
            return res
    

Another idea is quite similar.  
We give it one more loop at first to find all character `C` and initialize
distance to 0.  
The same, we can merge the extra pass to the forward pass one, like:

    
    
    for (int i = 0; i < n; ++i) if (S[i] == C) res[i] = 0; else if (i > 0) res[i] = res[i - 1] + 1;
    

But it will become less readable.

**C++:**

    
    
        vector<int> shortestToChar(string S, char C) {
            int n = S. size();
            vector<int> res(n, n);
            for (int i = 0; i < n; ++i) if (S[i] == C) res[i] = 0;
            for (int i = 1; i < n; ++i) res[i] = min(res[i], res[i - 1] + 1);
            for (int i = n - 2; i >= 0; --i) res[i] = min(res[i], res[i + 1] + 1);
            return res;
        }
    
    

**Java:**

    
    
        public int[] shortestToChar(String S, char C) {
            int n = S. length();
            int[] res = new int[n];
            for (int i = 0; i < n; ++i) res[i] = S.charAt(i) == C ? 0 : n;
            for (int i = 1; i < n; ++i) res[i] = Math.min(res[i], res[i - 1] + 1);
            for (int i = n - 2; i >= 0; --i) res[i] = Math.min(res[i], res[i + 1] + 1);
            return res;
        }
    
    

**Python:**

    
    
        def shortestToChar(self, S, C):
            n =  len(S)
            res = [0 if c == C else n for c in S]
            for i in range(n - 1): res[i + 1] = min(res[i + 1], res[i] + 1)
            for i in range(n - 1)[::-1]: res[i] = min(res[i], res[i + 1] + 1)
            return res
    


### Solution 2
You need to find shortest distance for _EACH_ Character in the String 'S' to
Character 'C'


### Solution 3
    
    
    /**  "loveleetcode" "e"
     *  1. put 0 at all position equals to e, and max at all other position
     *     we will get [max, max, max, 0, max, 0, 0, max, max, max, max, 0]
     *  2. scan from left to right, if =max, skip, else dist[i+1] = Math.min(dp[i] + 1, dp[i+1]), 
     *     we can get [max, max, max, 0, 1, 0, 0, 1, 2, 3, 4, 0]
     *  3. scan from right to left, use dp[i-1] = Math.min(dp[i] + 1, dp[i-1])
     *     we will get[3, 2, 1, 0, 1, 0, 0, 1, 2, 2, 1, 0] 
     */
    class Solution {
        public int[] shortestToChar(String s, char c) {
            int n = s.length();
            int[] dist = new int[n];
            for (int i = 0; i < n; i++) {
                if (s.charAt(i) == c) continue;
                dist[i] = Integer.MAX_VALUE;
            }
            for (int i = 0; i < n-1; i++) {
                if (dist[i] == Integer.MAX_VALUE) continue;
                else dist[i + 1] = Math.min(dist[i+1], dist[i] + 1);
            }
            for (int i = n-1; i > 0; i--) {
                dist[i-1] = Math.min(dist[i-1], dist[i] + 1);
            }
            return dist; 
        }
    }
    



