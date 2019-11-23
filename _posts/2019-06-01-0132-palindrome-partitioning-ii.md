---
layout: post
title: 132. Palindrome Partitioning II
---
### Question
Given a string _s_ , partition _s_ such that every substring of the partition
is a palindrome.

Return the minimum cuts needed for a palindrome partitioning of _s_.

 **Example:**

    
    
     **Input:**  "aab"
    **Output:** 1
    **Explanation:** The palindrome partitioning ["aa","b"] could be produced using 1 cut.
    

### Solution 1
    
    
     class Solution {
    public:
        int minCut(string s) {
            int n = s.size();
            vector<int> cut(n+1, 0);  // number of cuts for the first k characters
            for (int i = 0; i <= n; i++) cut[i] = i-1;
            for (int i = 0; i < n; i++) {
                for (int j = 0; i-j >= 0 && i+j < n && s[i-j]==s[i+j] ; j++) // odd length palindrome
                    cut[i+j+1] = min(cut[i+j+1],1+cut[i-j]);
    
                for (int j = 1; i-j+1 >= 0 && i+j < n && s[i-j+1] == s[i+j]; j++) // even length palindrome
                    cut[i+j+1] = min(cut[i+j+1],1+cut[i-j+1]);
            }
            return cut[n];
        }
    };


### Solution 2
This can be solved by two points:

  1. `cut[i]` is the minimum of `cut[j - 1] + 1 (j <= i)`, if `[j, i]` is palindrome.
  2. If `[j, i]` is palindrome, `[j + 1, i - 1]` is palindrome, and `c[j] == c[i]`.

The 2nd point reminds us of using dp (caching).

    
    
    a   b   a   |   c  c
                    j  i
           j-1  |  [j, i] is palindrome
       cut(j-1) +  1
    

Hope it helps!

    
    
    public int minCut(String s) {
        char[] c = s.toCharArray();
        int n = c.length;
        int[] cut = new int[n];
        boolean[][] pal = new boolean[n][n];
        
        for(int i = 0; i < n; i++) {
            int min = i;
            for(int j = 0; j <= i; j++) {
                if(c[j] == c[i] && (j + 1 > i - 1 || pal[j + 1][i - 1])) {
                    pal[j][i] = true;  
                    min = j == 0 ? 0 : Math.min(min, cut[j - 1] + 1);
                }
            }
            cut[i] = min;
        }
        return cut[n - 1];
    }


### Solution 3
Calculate and maintain 2 DP states:

  1. pal[i][j] , which is whether s[i..j] forms a pal

  2. d[i], which  
is the minCut for s[i..n-1]

Once we comes to a pal[i][j]==true:

  * if j==n-1, the string s[i..n-1] is a Pal, minCut is 0, d[i]=0;
  * else: the current cut num (first cut s[i..j] and then cut the rest  
s[j+1...n-1]) is 1+d[j+1], compare it to the exisiting minCut num  
d[i], repalce if smaller.

d[0] is the answer.

    
    
     class Solution {
        public:
            int minCut(string s) {
                if(s.empty()) return 0;
                int n = s.size();
                vector<vector<bool>> pal(n,vector<bool>(n,false));
                vector<int> d(n);
                for(int i=n-1;i>=0;i--)
                {
                    d[i]=n-i-1;
                    for(int j=i;j<n;j++)
                    {
                        if(s[i]==s[j] && (j-i<2 || pal[i+1][j-1]))
                        {
                           pal[i][j]=true;
                           if(j==n-1)
                               d[i]=0;
                           else if(d[j+1]+1<d[i])
                               d[i]=d[j+1]+1;
                        }
                    }
                }
                return d[0];
            }
        };



