---
layout: post
title: 3.Longest Substring Without Repeating Characters
---
### Question
Given a string, find the length of the **longest substring** without repeating
characters.

 **Example 1:**

    
    
     **Input:** "abcabcbb"
    **Output:** 3 
    **Explanation:** The answer is "abc", with the length of 3. 
    

**Example 2:**

    
    
    **Input:** "bbbbb"
    **Output:** 1
    **Explanation:** The answer is "b", with the length of 1.
    

**Example 3:**

    
    
    **Input:** "pwwkew"
    **Output:** 3
    **Explanation:** The answer is "wke", with the length of 3. 
                 Note that the answer must be a **substring** , "pwke" is a _subsequence_ and not a substring.
    

### Solution 1
the basic idea is, keep a hashmap which stores the characters in string as
keys and their positions as values, and keep two pointers which define the max
substring. move the right pointer to scan through the string , and meanwhile
update the hashmap. If the character is already in the hashmap, then move the
left pointer to the right of the same character last found. Note that the two
pointers can only move forward.

    
    
       public int lengthOfLongestSubstring(String s) {
            if (s.length()==0) return 0;
            HashMap<Character, Integer> map = new HashMap<Character, Integer>();
            int max=0;
            for (int i=0, j=0; i<s.length(); ++i){
                if (map.containsKey(s.charAt(i))){
                    j = Math.max(j,map.get(s.charAt(i))+1);
                }
                map.put(s.charAt(i),i);
                max = Math.max(max,i-j+1);
            }
            return max;
        }

### Solution 2
    
    
    int lengthOfLongestSubstring(string s) {
            vector<int> dict(256, -1);
            int maxLen = 0, start = -1;
            for (int i = 0; i != s.length(); i++) {
                if (dict[s[i]] > start)
                    start = dict[s[i]];
                dict[s[i]] = i;
                maxLen = max(maxLen, i - start);
            }
            return maxLen;
        }

### Solution 3
The idea is use a hash set to track the longest substring without repeating
characters so far, use a fast pointer j to see if character j is in the hash
set or not, if not, great, add it to the hash set, move j forward and update
the max length, otherwise, delete from the head by using a slow pointer i
until we can put character j to the hash set.

    
    
    public int lengthOfLongestSubstring(String s) {
        int i = 0, j = 0, max = 0;
        Set<Character> set = new HashSet<>();
        
        while (j < s.length()) {
            if (!set.contains(s.charAt(j))) {
                set.add(s.charAt(j++));
                max = Math.max(max, set.size());
            } else {
                set.remove(s.charAt(i++));
            }
        }
        
        return max;
    }


