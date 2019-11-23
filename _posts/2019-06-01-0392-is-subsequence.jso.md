---
layout: post
title: 392. Is Subsequence
---
### Question
Given a string **s** and a string **t** , check if **s** is subsequence of
**t**.

You may assume that there is only lower case English letters in both **s** and
**t**. **t** is potentially a very long (length ~= 500,000) string, and **s**
is a short string ( <=100).

A subsequence of a string is a new string which is formed from the original
string by deleting some (can be none) of the characters without disturbing the
relative positions of the remaining characters. (ie, `"ace"` is a subsequence
of `"abcde"` while `"aec"` is not).

**Example 1:**  
**s** = `"abc"`, **t** = `"ahbgdc"`

Return `true`.

**Example 2:**  
**s** = `"axc"`, **t** = `"ahbgdc"`

Return `false`.

**Follow up:**  
If there are lots of incoming S, say S1, S2, ... , Sk where k  >= 1B, and you
want to check one by one to see if T has its subsequence. In this scenario,
how would you change your code?

**Credits:**  
Special thanks to [@pbrother](https://leetcode.com/pbrother/) for adding this
problem and creating all test cases.

### Solution 1
Re: [Java binary search using TreeSet got TLE](/topic/57994/java-binary-
search-using-treeset-got-tle)

I think the Map and TreeSet could be simplified by Array and binarySearch.
Since we scan T from beginning to the end (index itself is in increasing
order), List will be sufficient. Then we can use binarySearch to replace with
TreeSet ability which is a little overkill for this problem. Here is my
solution.

    
    
        // Follow-up: O(N) time for pre-processing, O(Mlog?) for each S.
        // Eg-1. s="abc", t="bahbgdca"
        // idx=[a={1,7}, b={0,3}, c={6}]
        //  i=0 ('a'): prev=1
        //  i=1 ('b'): prev=3
        //  i=2 ('c'): prev=6 (return true)
        // Eg-2. s="abc", t="bahgdcb"
        // idx=[a={1}, b={0,6}, c={5}]
        //  i=0 ('a'): prev=1
        //  i=1 ('b'): prev=6
        //  i=2 ('c'): prev=? (return false)
        public boolean isSubsequence(String s, String t) {
            List<Integer>[] idx = new List[256]; // Just for clarity
            for (int i = 0; i < t.length(); i++) {
                if (idx[t.charAt(i)] == null)
                    idx[t.charAt(i)] = new ArrayList<>();
                idx[t.charAt(i)].add(i);
            }
            
            int prev = 0;
            for (int i = 0; i < s.length(); i++) {
                if (idx[s.charAt(i)] == null) return false; // Note: char of S does NOT exist in T causing NPE
                int j = Collections.binarySearch(idx[s.charAt(i)], prev);
                if (j < 0) j = -j - 1;
                if (j == idx[s.charAt(i)].size()) return false;
                prev = idx[s.charAt(i)].get(j) + 1;
            }
            return true;
        }
    


### Solution 2
Just use two pointers:

    
    
    public class Solution {
        public boolean isSubsequence(String s, String t) {
            if (s.length() == 0) return true;
            int indexS = 0, indexT = 0;
            while (indexT < t.length()) {
                if (t.charAt(indexT) == s.charAt(indexS)) {
                    indexS++;
                    if (indexS == s.length()) return true;
                }
                indexT++;
            }
            return false;
        }
    }


### Solution 3
    
    
    bool isSubsequence(char* s, char* t) {
        while (*t)
            s += *s == *t++;
        return !*s;
    }
    

Just go through t and "cross off" the matching characters in s. Then return
whether there's nothing left in s.

Sometimes, C makes things easier... here it's helpful that C strings are null
terminated and that I can easily pop off a string's first character in
constant time just by incrementing the pointer.



