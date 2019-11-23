---
layout: post
title: 696. Count Binary Substrings
---
### Question
Give a string `s`, count the number of non-empty (contiguous) substrings that
have the same number of 0's and 1's, and all the 0's and all the 1's in these
substrings are grouped consecutively.

Substrings that occur multiple times are counted the number of times they
occur.

 **Example 1:**  

    
    
     **Input:** "00110011"
    **Output:** 6
    **Explanation:** There are 6 substrings that have equal number of consecutive 1's and 0's: "0011", "01", "1100", "10", "0011", and "01".
      
     Notice that some of these substrings repeat and are counted the number of times they occur.
      
    Also, "00110011" is not a valid substring because **all** the 0's (and 1's) are not grouped together.
    

**Example 2:**  

    
    
    **Input:** "10101"
    **Output:** 4
    **Explanation:** There are 4 substrings: "10", "01", "10", "01" that have equal number of consecutive 1's and 0's.
    

**Note:**

* `s.length` will be between 1 and 50,000.
* `s` will only consist of "0" or "1" characters.

### Solution 1
    
    
     public int countBinarySubstrings(String s) {
        int prevRunLength = 0, curRunLength = 1, res = 0;
        for (int i=1;i<s.length();i++) {
            if (s.charAt(i) == s.charAt(i-1)) curRunLength++;
            else {
                prevRunLength = curRunLength;
                curRunLength = 1;
            }
            if (prevRunLength >= curRunLength) res++;
        }
        return res;
    }
    


### Solution 2
First, I count the number of 1 or 0 grouped consecutively.  
For example "0110001111" will be `[1, 2, 3, 4]`.

Second, for any possible substrings with 1 and 0 grouped consecutively, the
number of valid substring will be the minimum number of 0 and 1.  
For example "0001111", will be `min(3, 4) = 3`, (`"01", "0011", "000111"`)

 **Python:**

    
    
        def countBinarySubstrings(self, s):
            s = map( len, s.replace('01', '0 1').replace('10', '1 0').split())
            return sum(min(a, b) for a, b in zip(s, s[1:]))
    

**C++:**

    
    
         int countBinarySubstrings(string s) {
            int cur = 1, pre = 0, res = 0;
            for (int i = 1; i < s.size(); i++) {
                if (s[i] == s[i - 1]) cur++;
                else {
                    res += min(cur, pre);
                    pre = cur;
                    cur = 1;
                }
            }
            return res + min(cur, pre);
        }
    

**Java:** :

    
    
        public int countBinarySubstrings(String s) {
            int cur = 1, pre = 0, res = 0;
            for (int i = 1; i < s.length(); i++) {
                if (s.charAt(i) == s.charAt(i - 1)) cur++;
                else {
                    res += Math.min(cur, pre);
                    pre = cur;
                    cur = 1;
                }
            }
            return res + Math.min(cur, pre);
        }
    


### Solution 3
It takes me some time to understand this problem, after look at the top
solution. I figured out how to solve it, Thanks to @compton_scatter, here is
just some explaination of his solution:

  1. preRun count the same item happend before (let say you have 0011, preRun = 2 when you hit the first 1, means there are two zeros before first '1')
  2. curRun count the current number of items (let say you have 0011, curRun = 2 when you hit the second 1, means there are two 1s so far)
  3. Whenever item change (from 0 to 1 or from 1 to 0), preRun change to curRun, reset curRun to 1 (store the curRun number into PreRun, reset curRun)
  4. Every time preRun >= curRun means there are more 0s before 1s, so could do count++ . (This was the tricky one, ex. 0011 when you hit the first '1', curRun = 1, preRun = 2, means 0s number is larger than 1s number, so we could form "01" at this time, count++ . When you hit the second '1', curRun = 2, preRun = 2, means 0s' number equals to 1s' number, so we could form "0011" at this time, that is why count++)

    
    
    class Solution {
    public int countBinarySubstrings(String s) {
    if (s == null || s.length() == 0) return 0;
            int preRun = 0;
            int curRun =1;
            int count = 0;
            for (int i = 1; i < s.length(); i++){
                if (s.charAt(i) == s.charAt(i-1)) curRun++;
                else {
                    preRun = curRun;
                    curRun = 1;
                }
                if (preRun >= curRun) count++;
            }
            return count;
        }
    }
    



