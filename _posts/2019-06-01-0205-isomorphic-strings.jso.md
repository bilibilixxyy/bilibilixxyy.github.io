---
layout: post
title: 205. Isomorphic Strings
---
### Question
Given two strings **_s_** and **_t_** , determine if they are isomorphic.

Two strings are isomorphic if the characters in **_s_** can be replaced to get
**_t_**.

All occurrences of a character must be replaced with another character while
preserving the order of characters. No two characters may map to the same
character but a character may map to itself.

 **Example 1:**

    
    
     **Input:** **_s_** = "egg", **_t =_**"add"
    **Output:** true
    

**Example 2:**

    
    
    **Input:** **_s_** = "foo", **_t =_**"bar"
    **Output:** false

**Example 3:**

    
    
    **Input:** **_s_** = "paper", **_t =_**"title"
    **Output:** true

 **Note:**  
You may assume both **_s  _**and **_t  _**have the same length.

### Solution 1
The idea is that we need to map a char to another one, for example, "egg" and
"add", we need to constract the mapping 'e' -> 'a' and 'g' -> 'd'. Instead of
directly mapping 'e' to 'a', another way is to mark them with same value, for
example, 'e' -> 1, 'a'-> 1, and 'g' -> 2, 'd' -> 2, this works same.

So we use two arrays here m1 and m2, initialized space is 256 (Since the whole
ASCII size is 256, 128 also works here). Traverse the character of both s and
t on the same position, if their mapping values in m1 and m2 are different,
means they are not mapping correctly, returen false; else we construct the
mapping, since m1 and m2 are both initialized as 0, we want to use a new value
when i == 0, so i + 1 works here.

    
    
    class Solution {
    public:
        bool isIsomorphic(string s, string t) {
            int m1[256] = {0}, m2[256] = {0}, n = s.size();
            for (int i = 0; i < n; ++i) {
                if (m1[s[i]] != m2[t[i]]) return false;
                m1[s[i]] = i + 1;
                m2[t[i]] = i + 1;
            }
            return true;
        }
    };


### Solution 2
    
    
    def isIsomorphic1(self, s, t):
        d1, d2 = {}, {}
        for i, val in enumerate(s):
            d1[val] = d1.get(val, []) + [i]
        for i, val in enumerate(t):
            d2[val] = d2.get(val, []) + [i]
        return sorted(d1.values()) == sorted(d2.values())
            
    def isIsomorphic2(self, s, t):
        d1, d2 = [[] for _ in xrange(256)], [[] for _ in xrange(256)]
        for i, val in enumerate(s):
            d1[ord(val)].append(i)
        for i, val in enumerate(t):
            d2[ord(val)].append(i)
        return sorted(d1) == sorted(d2)
        
    def isIsomorphic3(self, s, t):
        return len(set(zip(s, t))) == len(set(s)) == len(set(t))
        
    def isIsomorphic4(self, s, t): 
        return [s.find(i) for i in s] == [t.find(j) for j in t]
        
    def isIsomorphic5(self, s, t):
        return map(s.find, s) == map(t.find, t)
    
    def isIsomorphic(self, s, t):
        d1, d2 = [0 for _ in xrange(256)], [0 for _ in xrange(256)]
        for i in xrange(len(s)):
            if d1[ord(s[i])] != d2[ord(t[i])]:
                return False
            d1[ord(s[i])] = i+1
            d2[ord(t[i])] = i+1
        return True


### Solution 3
Hi guys!

The main idea is to store the last seen positions of current (i-th) characters
in both strings. If previously stored positions are different then we know
that the fact they're occuring in the current i-th position simultaneously is
a mistake. We could use a map for storing but as we deal with chars which are
basically ints and can be used as indices we can do the whole thing with an
array.

Check the code below. Happy coding!

* * *
    
    
    public class Solution {
        public boolean isIsomorphic(String s1, String s2) {
            int[] m = new int[512];
            for (int i = 0; i < s1.length(); i++) {
                if (m[s1.charAt(i)] != m[s2.charAt(i)+256]) return false;
                m[s1.charAt(i)] = m[s2.charAt(i)+256] = i+1;
            }
            return true;
        }
    }



