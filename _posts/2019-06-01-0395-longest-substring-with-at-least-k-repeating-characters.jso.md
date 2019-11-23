---
layout: post
title: 395. Longest Substring with At Least K Repeating Characters
---
### Question
Find the length of the longest substring **_T_** of a given string (consists
of lowercase letters only) such that every character in **_T_** appears no
less than _k_ times.

 **Example 1:**

    
    
    Input:
    s = "aaabb", k = 3
    
    Output:
    3
    
    The longest substring is "aaa", as 'a' is repeated 3 times.
    

**Example 2:**

    
    
    Input:
    s = "ababbc", k = 2
    
    Output:
    5
    
    The longest substring is "ababb", as 'a' is repeated 2 times and 'b' is repeated 3 times.
    

### Solution 1
For each h, apply two pointer technique to find the longest substring with at
least K repeating characters and the number of unique characters in substring
is h.

    
    
    public class Solution {
        public int longestSubstring(String s, int k) {
            char[] str = s.toCharArray();
            int[] counts = new int[26];
            int h, i, j, idx, max = 0, unique, noLessThanK;
            
            for (h = 1; h <= 26; h++) {
                Arrays.fill(counts, 0);
                i = 0; 
                j = 0;
                unique = 0;
                noLessThanK = 0;
                while (j < str.length) {
                    if (unique <= h) {
                        idx = str[j] - 'a';
                        if (counts[idx] == 0)
                            unique++;
                        counts[idx]++;
                        if (counts[idx] == k)
                            noLessThanK++;
                        j++;
                    }
                    else {
                        idx = str[i] - 'a';
                        if (counts[idx] == k)
                            noLessThanK--;
                        counts[idx]--;
                        if (counts[idx] == 0)
                            unique--;
                        i++;
                    }
                    if (unique == h && unique == noLessThanK)
                        max = Math.max(j - i, max);
                }
            }
            
            return max;
        }
    }
    


### Solution 2
## Update:

As pointed out by @hayleyhu, I can just take the first too rare character
instead of a rarest. Submitted once, accepted in 48 ms.

    
    
    def longestSubstring(self, s, k):
        for c in set(s):
            if s.count(c) < k:
                return max(self.longestSubstring(t, k) for t in s.split(c))
        return len(s)
    

## Original:

    
    
    def longestSubstring(self, s, k):
        if len(s) < k:
            return 0
        c = min(set(s), key=s.count)
        if s.count(c) >= k:
            return len(s)
        return max(self.longestSubstring(t, k) for t in s.split(c))
    

If every character appears at least k times, the whole string is ok. Otherwise
split by a least frequent character (because it will always be too infrequent
and thus can't be part of any ok substring) and make the most out of the
splits.

As usual for Python here, the runtime varies a lot, this got accepted in times
from 32 ms to 74 ms.


### Solution 3
    
    
    public int longestSubstring(String s, int k) {
    	char[] str = s.toCharArray();
    	return helper(str,0,s.length(),k);
    }
    private int helper(char[] str, int start, int end,  int k){
    	if (end - start < k) return 0;//substring length shorter than k.
    	int[] count = new int [26];
    	for (int i = start; i<end; i++) {
    		int idx = str[i] - 'a';
    		count[idx]++;
    	}
    	for (int i=0; i<26; i++) {
    		if (count[i] < k && count[i] > 0) { //count[i]=0 => i+'a' does not exist in the string, skip it.
    			for (int j = start; j<end; j++) {
    				if (str[j] == i+'a') {
    					int left = helper(str, start, j, k);
    					int right = helper(str, j+1, end, k);
    					return Math.max(left, right);
    				}
    			}
    		}
    	}
    	return end - start;
    }
    	
    

Edit: Aother recursion solution. Similar idea.

    
    
    class Solution {
    	public int longestSubstring(String s, int k) {
    		int[] count = new int[26];
    		int n = s.length();
    		for (int i = 0; i < n; i++) {
    			count[s.charAt(i) - 'a']++;
    		}
    		int start = 0;
    		int i = 0;
    		int maxLen = 0;
    		boolean isContinuous = true;
    		while(i < n) {
    			if (count[s.charAt(i)-'a'] < k) {
    				isContinuous = false;
    				int subRes = longestSubstring(s.substring(start, i), k);
    				maxLen = Math.max(subRes, maxLen);
    				while (i < n && count[s.charAt(i) -'a'] < k) {
    					i++;
    				}
    				start = i;
    			} else {
    				i++;
    			}
    		}
    
    		if (isContinuous) {
    			maxLen = i - start;
    		} else {
    			maxLen = Math.max(maxLen, longestSubstring(s.substring(start, i), k));
    		}
    
    		return maxLen;
    	}
    }



