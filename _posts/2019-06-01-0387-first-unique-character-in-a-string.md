---
layout: post
title: 387. First Unique Character in a String
---
### Question
Given a string, find the first non-repeating character in it and return it's
index. If it doesn't exist, return -1.

 **Examples:**

    
    
    s = "leetcode"
    return 0.
    
    s = "loveleetcode",
    return 2.
    

**Note:** You may assume the string contain only lowercase letters.

### Solution 1
Hey guys. My solution is pretty straightforward. It takes O(n) and goes
through the string twice:

  1. Get the frequency of each character.
  2. Get the first character that has a frequency of one.

Actually the code below passes all the cases. However, according to
@xietao0221, we could change the size of the frequency array to 256 to store
other kinds of characters. Thanks for all the other comments and suggestions.
Fight on!

    
    
    public class Solution {
        public int firstUniqChar(String s) {
            int freq [] = new int[26];
            for(int i = 0; i < s.length(); i ++)
                freq [s.charAt(i) - 'a'] ++;
            for(int i = 0; i < s.length(); i ++)
                if(freq [s.charAt(i) - 'a'] == 1)
                    return i;
            return -1;
        }
    }
    


### Solution 2
    
    
        def firstUniqChar(self, s):
            """
            :type s: str
            :rtype: int
            """
            
            letters='abcdefghijklmnopqrstuvwxyz'
            index=[s.index(l) for l in letters if s.count(l) == 1]
            return min(index) if len(index) > 0 else -1
    


### Solution 3
Brute force solution, traverse string s 2 times. First time, store counts of
every character into the hash table, second time, find the first character
that appears only once.

    
    
    class Solution {
    public:
        int firstUniqChar(string s) {
            unordered_map<char, int> m;
            for (auto &c : s) {
                m[c]++;
            }
            for (int i = 0; i < s.size(); i++) {
                if (m[s[i]] == 1) return i;
            }
            return -1;
        }
    };
    

if the string is extremely long, we wouldn't want to traverse it twice, so
instead only storing just counts of a char, we also store the index, and then
traverse the hash table.

    
    
    class Solution {
    public:
        int firstUniqChar(string s) {
            unordered_map<char, pair<int, int>> m;
            int idx = s.size();
            for (int i = 0; i < s.size(); i++) {
                m[s[i]].first++;
                m[s[i]].second = i;
            }
            for (auto &p : m) {
                if (p.second.first == 1) idx = min(idx, p.second.second);
            }
            return idx == s.size() ? -1 : idx;
        }
    };
    



