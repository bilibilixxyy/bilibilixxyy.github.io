---
layout: post
title: 424. Longest Repeating Character Replacement
---
### Question
Given a string `s` that consists of only uppercase English letters, you can
perform at most `k` operations on that string.

In one operation, you can choose **any** character of the string and change it
to any other uppercase English character.

Find the length of the longest sub-string containing all repeating letters you
can get after performing the above operations.

 **Note:**  
Both the string's length and _k_ will not exceed 10 4.

 **Example 1:**

    
    
     **Input:**
    s = "ABAB", k = 2
    
    **Output:**
    4
    
    **Explanation:**
    Replace the two 'A's with two 'B's or vice versa.
    



**Example 2:**

    
    
    **Input:**
    s = "AABABBA", k = 1
    
    **Output:**
    4
    
    **Explanation:**
    Replace the one 'A' in the middle with 'B' and form "AABBBBA".
    The substring "BBBB" has the longest repeating letters, which is 4.
    

### Solution 1
    
    
        public int characterReplacement(String s, int k) {
            int len = s.length();
            int[] count = new int[26];
            int start = 0, maxCount = 0, maxLength = 0;
            for (int end = 0; end < len; end++) {
                maxCount = Math.max(maxCount, ++count[s.charAt(end) - 'A']);
                while (end - start + 1 - maxCount > k) {
                    count[s.charAt(start) - 'A']--;
                    start++;
                }
                maxLength = Math.max(maxLength, end - start + 1);
            }
            return maxLength;
        }
    

There's no edge case for this question. The initial step is to extend the
window to its limit, that is, the longest we can get to with maximum number of
modifications. Until then the variable **start** will remain at 0.

Then as **end** increase, the whole substring from 0 to **end** will violate
the rule, so we need to update **start** accordingly (slide the window). We
move **start** to the right until the whole string satisfy the constraint
again. Then each time we reach such situation, we update our max length.


### Solution 2
The problem says that we can make at most k changes to the string (any
character can be replaced with any other character). So, let's say there were
no constraints like the k. Given a string convert it to a string with all same
characters with minimal changes. The answer to this is

> length of the entire string - number of times of the maximum occurring
character in the string

Given this, we can apply the at most k changes constraint and maintain a
sliding window such that

> (length of substring - number of times of the maximum occurring character in
the substring) <= k

    
    
    class Solution {
    public:
        int characterReplacement(string s, int k) {
            vector<int> counts(26, 0);
            int start = 0;
            int maxCharCount = 0;
            int n = s.length();
            int result = 0;
            for(int end = 0; end < n; end++){
                counts[s[end]-'A']++;
                if(maxCharCount < counts[s[end]-'A']){
                    maxCharCount = counts[s[end]-'A'];
                }
                while(end-start-maxCharCount+1 > k){
                    counts[s[start]-'A']--;
                    start++;
                    for(int i = 0; i < 26; i++){
                        if(maxCharCount < counts[i]){
                            maxCharCount = counts[i];
                        }
                    }
                }
                result = max(result, end-start+1);
            }
            return result;
        }
    };
    


### Solution 3
If we want to replace the characters in a substring and make it into the
longest repeating, then we definitely want to find the character with maximum
frequency and then replace all the other characters by this one, hence in this
way, we can minimize the number of replacement.

Hence, with such idea within mind, when we build a sliding window **[start,
end]** , we want this window to have this property: **(the length of the
window) - (the maximum frequency of the character in this window) > k**. Then
we can see that [start, end-1] can be fit into k replacement.

If we can find such a sliding window, then how to we move this window? We can
simply shift the **start to start+1** , since in this way this window will no
longer hold the property **(the length of the window) - (the maximum frequency
of the character in this window) > k**, and then we can keep moving **end to
end+1** to see if we have a longer window.

Below, we use localMaxFreq to record the maximum frequency seen so far in the
current window.

    
    
    class Solution {
    public:
        int characterReplacement(string s, int k) {
            int size = s.size(); int ret = 0;
            vector<int> count(26, 0);
            int start = 0; int end = 0; int localMaxFreq = 0; 
            for(; end<size; end++){
                count[s[end]-'A'] += 1;
                localMaxFreq = max(localMaxFreq, count[s[end]-'A']);
                if((end-start+1)-localMaxFreq > k) {
                    ret = max(ret, (end-start));
                    count[s[start]-'A'] -= 1;
                    start += 1;
                    localMaxFreq = *(max_element(count.begin(), count.end()));
                }
            }
            return max(ret, end-start);
        }
    };
    

The above code uses localMaxFreq to keep track the maximum frequency of each
current window. However, if we think carefully, we can find that if
**localMaxFreq of window A >= localMaxFreq of window B**, then the A window
must have longer length than the B window, this is because since both window A
and window B hold this property **(the length of the window) - (the maximum
frequency of the character in this window) > k**, and if **localMaxFreq of
window A >= localMaxFreq of window B**, then **(the length of the window A) >=
(the length of the window B)** by simple algebra .

Hence, we only need to keep track of a **globalMaxFreq** to record the
globally maximum frequency of each window what has been seen so far.

    
    
    class Solution {
     public:
        int characterReplacement(string s, int k) {
            int size = s.size(); int ret = 0;
            vector<int> count(26, 0);
            int start = 0; int end = 0; int globalMaxFreq = 0; 
            for(; end<size; end++){
                count[s[end]-'A'] += 1;
                globalMaxFreq = max(globalMaxFreq, count[s[end]-'A']);
                if((end-start+1)-globalMaxFreq > k) {
                    ret = max(ret, (end-start));
                    count[s[start]-'A'] -= 1;
                    start += 1;
                }
            }
            return max(ret, end-start);
        }
    };
    



