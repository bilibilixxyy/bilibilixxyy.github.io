---
layout: post
title: 712. Minimum ASCII Delete Sum for Two Strings
---
### Question
Given two strings `s1, s2`, find the lowest ASCII sum of deleted characters to
make two strings equal.

 **Example 1:**  

    
    
     **Input:** s1 = "sea", s2 = "eat"
    **Output:** 231
    **Explanation:** Deleting "s" from "sea" adds the ASCII value of "s" (115) to the sum.
    Deleting "t" from "eat" adds 116 to the sum.
    At the end, both strings are equal, and 115 + 116 = 231 is the minimum sum possible to achieve this.
    

**Example 2:**  

    
    
    **Input:** s1 = "delete", s2 = "leet"
    **Output:** 403
    **Explanation:** Deleting "dee" from "delete" to turn the string into "let",
    adds 100[d]+101[e]+101[e] to the sum.  Deleting "e" from "leet" adds 101[e] to the sum.
    At the end, both strings are equal to "let", and the answer is 100+101+101+101 = 403.
    If instead we turned both strings into "lee" or "eet", we would get answers of 433 or 417, which are higher.
    

**Note:**

* `0 < s1.length, s2.length <= 1000`.
* All elements of each string will have an ASCII value in `[97, 122]`.

### Solution 1
This is clearly a DP problem.

    
    
    dp[i][j] is the cost for s1.substr(0,i) and s2.substr(0, j). Note s1[i], s2[j] not included in the substring.
    
    Base case: dp[0][0] = 0
    target: dp[m][n]
    
    if s1[i-1] = s2[j-1]   // no deletion
        dp[i][j] = dp[i-1][j-1];
    else   // delete either s1[i-1] or s2[j-1]
        dp[i][j] = min(dp[i-1][j]+s1[i-1], dp[i][j-1]+s2[j-1]);
    

We can use a 2D vector, or an optimized O(n) extra space. See below. The run
time is O(mn).

    
    
    class Solution {
    public:
        int minimumDeleteSum(string s1, string s2) {
            int m = s1.size(), n = s2.size();
            vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
            for (int j = 1; j <= n; j++)
                dp[0][j] = dp[0][j-1]+s2[j-1];
            for (int i = 1; i <= m; i++) {
                dp[i][0] = dp[i-1][0]+s1[i-1];
                for (int j = 1; j <= n; j++) {
                    if (s1[i-1] == s2[j-1])
                        dp[i][j] = dp[i-1][j-1];
                    else 
                        dp[i][j] = min(dp[i-1][j]+s1[i-1], dp[i][j-1]+s2[j-1]);
                }
            }
            return dp[m][n];
        }
    };
    

Optimized O(n) extra space

    
    
    class Solution {
    public:
        int minimumDeleteSum(string s1, string s2) {
            int m = s1.size(), n = s2.size();
            vector<int> dp(n+1, 0);
            for (int j = 1; j <= n; j++)
                dp[j] = dp[j-1]+s2[j-1];
            for (int i = 1; i <= m; i++) {
                int t1 = dp[0];
                dp[0] += s1[i-1];
                for (int j = 1; j <= n; j++) {
                    int t2 = dp[j];
                    dp[j] = s1[i-1] == s2[j-1]? t1:min(dp[j]+s1[i-1], dp[j-1]+s2[j-1]);
                    t1 = t2;
                }
            }
            return dp[n];
        }
    };
    


### Solution 2
Very Similar to Longest Common Subsequence Problem.

Let, s1 & s2 be the two strings with 1 based indexes.  
Now assume, dp[i][j] = minimumDeleteSum( s1[0,i], s2[0,j])

 **Base case** :  
When either of the strings is empty, then whole of the other string has to be
deleted.  
for e.g. if s1 = "", s2 = "abc", then only way we could match these strings by
deleting characters is by dropping 'a','b','c' of s2 to make it empty like s1.

Thus, whenever one of them is empty(i.e. i==0 or j==0) then answer is sum of
ASCII code of the characters of the other string.

Hence the _**1st**_ rule: **dp[i][j] =**

  *  **sum_ascii(s2) - > if i==0**
  *  **sum_ascii(s1) - > if j==0**

 **Non-Base case**

Of the two strings, if both of their last characters match then certainly the
answer comes from skipping those characters.  
i.e. Answer("zca","bza") = Answer("zc","bz")

Hence the _**2nd**_ rule: **dp[i][j] =**

  *  **dp[i-1][j-1] - > if s1[i]==s2[j]**

Finally, if the last characters are different then its one of the three
situations:

  * drop s1's last character (ASCII(s1's last) + dp[i-1][j])
  * drop s2's last character (ASCII(s2's last) + dp[i][j-1])
  * drop both last characters (ASCII(s1's last) + ASCII(s2's last) + dp[i-1[[j-1])

Hence the _**3rd**_ rule: **dp[i][j] =**

  *  **Min((ASCII(s1's last) + dp[i-1][j]),(ASCII(s2's last) + dp[i][j-1]),(ASCII(s1's last) + ASCII(s2's last) + dp[i-1[[j-1]))**

Combining these 3 rules gives us an elegant solution.

    
    
    public int minimumDeleteSum(String s1, String s2) {
            int m = s1.length();
            int n = s2.length();
            int[][] dp = new int[m+1][n+1];
            for(int i=0;i<=m;i++){
                for(int j=0;j<=n;j++){
                    if(i==0 || j==0){
                        int a = 0;
                        for(int z=1;z<=Math.max(j,i);z++){
                            a += (i==0?s2.charAt(z-1):s1.charAt(z-1));
                        }
                        dp[i][j] = a;
                    }
                    else if(s1.charAt(i-1)==s2.charAt(j-1)){
                        dp[i][j] = dp[i-1][j-1];
                    }
                    else{
                        dp[i][j] = Math.min(s1.charAt(i-1)+dp[i-1][j],s2.charAt(j-1)+dp[i][j-1]);
                        dp[i][j] = Math.min(dp[i][j],s1.charAt(i-1)+s2.charAt(j-1)+dp[i-1][j-1]);
                    }
                }
            }
            return dp[m][n];
        }
    


### Solution 3
The same idea as edit distance. Straightforward 19 lines.

    
    
    class Solution {
        public int minimumDeleteSum(String s1, String s2) {
            int[][] count = new int[s1.length() + 1][s2.length() + 1];
            for(int i = 1; i < count.length; i++){
                count[i][0] = count[i-1][0] + s1.charAt(i-1);
            }
            for(int i = 1; i < count[0].length; i++){
                count[0][i] = count[0][i-1] + s2.charAt(i-1);
            }
            for(int i = 1; i < count.length; i++){
                for(int j = 1; j < count[0].length; j++){
                    int cost = (s1.charAt(i-1) == s2.charAt(j-1))? 0 : s1.charAt(i-1) + s2.charAt(j-1);
                    count[i][j] = Math.min(count[i-1][j] + s1.charAt(i-1), count[i][j-1] + s2.charAt(j-1));
                    count[i][j] = Math.min(count[i][j], count[i-1][j-1] + cost);
                }
            }
            return count[s1.length()][s2.length()];   
        }
    }
    ``



