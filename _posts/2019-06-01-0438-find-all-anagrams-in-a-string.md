---
layout: post
title: 438. Find All Anagrams in a String
---
### Question
Given a string **s** and a **non-empty** string **p** , find all the start
indices of **p** 's anagrams in **s**.

Strings consists of lowercase English letters only and the length of both
strings **s** and **p** will not be larger than 20,100.

The order of output does not matter.

 **Example 1:**

    
    
     **Input:**
    s: "cbaebabacd" p: "abc"
    
    **Output:**
    [0, 6]
    
    **Explanation:**
    The substring with start index = 0 is "cba", which is an anagram of "abc".
    The substring with start index = 6 is "bac", which is an anagram of "abc".
    

**Example 2:**

    
    
    **Input:**
    s: "abab" p: "ab"
    
    **Output:**
    [0, 1, 2]
    
    **Explanation:**
    The substring with start index = 0 is "ab", which is an anagram of "ab".
    The substring with start index = 1 is "ba", which is an anagram of "ab".
    The substring with start index = 2 is "ab", which is an anagram of "ab".
    

### Solution 1
**Among all leetcode questions, I find that there are at least 5 substring
search problem which could be solved by the sliding window algorithm.**  
so I sum up the algorithm template here. wish it will help you!

  1.  _ **the template**_ :

    
    
    public class Solution {
        public List<Integer> slidingWindowTemplateByHarryChaoyangHe(String s, String t) {
            //init a collection or int value to save the result according the question.
            List<Integer> result = new LinkedList<>();
            if(t.length()> s.length()) return result;
            
            //create a hashmap to save the Characters of the target substring.
            //(K, V) = (Character, Frequence of the Characters)
            Map<Character, Integer> map = new HashMap<>();
            for(char c : t.toCharArray()){
                map.put(c, map.getOrDefault(c, 0) + 1);
            }
            //maintain a counter to check whether match the target string.
            int counter = map.size();//must be the map size, NOT the string size because the char may be duplicate.
            
            //Two Pointers: begin - left pointer of the window; end - right pointer of the window
            int begin = 0, end = 0;
            
            //the length of the substring which match the target string.
            int len = Integer.MAX_VALUE; 
            
            //loop at the begining of the source string
            while(end < s.length()){
                
                char c = s.charAt(end);//get a character
                
                if( map.containsKey(c) ){
                    map.put(c, map.get(c)-1);// plus or minus one
                    if(map.get(c) == 0) counter--;//modify the counter according the requirement(different condition).
                }
                end++;
                
                //increase begin pointer to make it invalid/valid again
                while(counter == 0 /* counter condition. different question may have different condition */){
                    
                    char tempc = s.charAt(begin);//***be careful here: choose the char at begin pointer, NOT the end pointer
                    if(map.containsKey(tempc)){
                        map.put(tempc, map.get(tempc) + 1);//plus or minus one
                        if(map.get(tempc) > 0) counter++;//modify the counter according the requirement(different condition).
                    }
                    
                    /* save / update(min/max) the result if find a target*/
                    // result collections or result int value
                    
                    begin++;
                }
            }
            return result;
        }
    }
    

  1. Firstly, here is my sliding solution this question. I will sum up the template below this code.

**2) the similar questions are:**

<https://leetcode.com/problems/minimum-window-substring/>  
<https://leetcode.com/problems/longest-substring-without-repeating-
characters/>  
<https://leetcode.com/problems/substring-with-concatenation-of-all-words/>  
<https://leetcode.com/problems/longest-substring-with-at-most-two-distinct-
characters/>  
<https://leetcode.com/problems/find-all-anagrams-in-a-string/>

**3) I will give my solution for these questions use the above template one by
one**

**Minimum-window-substring**  
<https://leetcode.com/problems/minimum-window-substring/>

    
    
     public class Solution {
        public String minWindow(String s, String t) {
            if(t.length()> s.length()) return "";
            Map<Character, Integer> map = new HashMap<>();
            for(char c : t.toCharArray()){
                map.put(c, map.getOrDefault(c,0) + 1);
            }
            int counter = map.size();
            
            int begin = 0, end = 0;
            int head = 0;
            int len = Integer.MAX_VALUE;
            
            while(end < s.length()){
                char c = s.charAt(end);
                if( map.containsKey(c) ){
                    map.put(c, map.get(c)-1);
                    if(map.get(c) == 0) counter--;
                }
                end++;
                
                while(counter == 0){
                    char tempc = s.charAt(begin);
                    if(map.containsKey(tempc)){
                        map.put(tempc, map.get(tempc) + 1);
                        if(map.get(tempc) > 0){
                            counter++;
                        }
                    }
                    if(end-begin < len){
                        len = end - begin;
                        head = begin;
                    }
                    begin++;
                }
                
            }
            if(len == Integer.MAX_VALUE) return "";
            return s.substring(head, head+len);
        }
    }
    

you may find that I only change a little code above to solve the question
"Find All Anagrams in a String":  
change

    
    
                    if(end-begin < len){
                        len = end - begin;
                        head = begin;
                    }
    

to

    
    
                    if(end-begin == t.length()){
                        result.add(begin);
                    }
    

**longest substring without repeating characters**  
<https://leetcode.com/problems/longest-substring-without-repeating-
characters/>

    
    
     public class Solution {
        public int lengthOfLongestSubstring(String s) {
            Map<Character, Integer> map = new HashMap<>();
            int begin = 0, end = 0, counter = 0, d = 0;
    
            while (end < s.length()) {
                // > 0 means repeating character
                //if(map[s.charAt(end++)]-- > 0) counter++;
                char c = s.charAt(end);
                map.put(c, map.getOrDefault(c, 0) + 1);
                if(map.get(c) > 1) counter++;
                end++;
                
                while (counter > 0) {
                    //if (map[s.charAt(begin++)]-- > 1) counter--;
                    char charTemp = s.charAt(begin);
                    if (map.get(charTemp) > 1) counter--;
                    map.put(charTemp, map.get(charTemp)-1);
                    begin++;
                }
                d = Math.max(d, end - begin);
            }
            return d;
        }
    }
    

**Longest Substring with At Most Two Distinct Characters**  
<https://leetcode.com/problems/longest-substring-with-at-most-two-distinct-
characters/>

    
    
     public class Solution {
        public int lengthOfLongestSubstringTwoDistinct(String s) {
            Map<Character,Integer> map = new HashMap<>();
            int start = 0, end = 0, counter = 0, len = 0;
            while(end < s.length()){
                char c = s.charAt(end);
                map.put(c, map.getOrDefault(c, 0) + 1);
                if(map.get(c) == 1) counter++;//new char
                end++;
                while(counter > 2){
                    char cTemp = s.charAt(start);
                    map.put(cTemp, map.get(cTemp) - 1);
                    if(map.get(cTemp) == 0){
                        counter--;
                    }
                    start++;
                }
                len = Math.max(len, end-start);
            }
            return len;
        }
    }
    

**Substring with Concatenation of All Words**  
<https://leetcode.com/problems/substring-with-concatenation-of-all-words/>

    
    
     public class Solution {
        public List<Integer> findSubstring(String S, String[] L) {
            List<Integer> res = new LinkedList<>();
            if (L.length == 0 || S.length() < L.length * L[0].length())   return res;
            int N = S.length();
            int M = L.length; // *** length
            int wl = L[0].length();
            Map<String, Integer> map = new HashMap<>(), curMap = new HashMap<>();
            for (String s : L) {
                if (map.containsKey(s))   map.put(s, map.get(s) + 1);
                else                      map.put(s, 1);
            }
            String str = null, tmp = null;
            for (int i = 0; i < wl; i++) {
                int count = 0;  // remark: reset count 
                int start = i;
                for (int r = i; r + wl <= N; r += wl) {
                    str = S.substring(r, r + wl);
                    if (map.containsKey(str)) {
                        if (curMap.containsKey(str))   curMap.put(str, curMap.get(str) + 1);
                        else                           curMap.put(str, 1);
                        
                        if (curMap.get(str) <= map.get(str))    count++;
                        while (curMap.get(str) > map.get(str)) {
                            tmp = S.substring(start, start + wl);
                            curMap.put(tmp, curMap.get(tmp) - 1);
                            start += wl;
                            
                            //the same as https://leetcode.com/problems/longest-substring-without-repeating-characters/
                            if (curMap.get(tmp) < map.get(tmp)) count--;
                            
                        }
                        if (count == M) {
                            res.add(start);
                            tmp = S.substring(start, start + wl);
                            curMap.put(tmp, curMap.get(tmp) - 1);
                            start += wl;
                            count--;
                        }
                    }else {
                        curMap.clear();
                        count = 0;
                        start = r + wl;//not contain, so move the start
                    }
                }
                curMap.clear();
            }
            return res;
        }
    }
    

**Find All Anagrams in a String**  
<https://leetcode.com/problems/find-all-anagrams-in-a-string/>

    
    
     public class Solution {
        public List<Integer> findAnagrams(String s, String t) {
            List<Integer> result = new LinkedList<>();
            if(t.length()> s.length()) return result;
            Map<Character, Integer> map = new HashMap<>();
            for(char c : t.toCharArray()){
                map.put(c, map.getOrDefault(c, 0) + 1);
            }
            int counter = map.size();
            
            int begin = 0, end = 0;
            int head = 0;
            int len = Integer.MAX_VALUE;
            
            
            while(end < s.length()){
                char c = s.charAt(end);
                if( map.containsKey(c) ){
                    map.put(c, map.get(c)-1);
                    if(map.get(c) == 0) counter--;
                }
                end++;
                
                while(counter == 0){
                    char tempc = s.charAt(begin);
                    if(map.containsKey(tempc)){
                        map.put(tempc, map.get(tempc) + 1);
                        if(map.get(tempc) > 0){
                            counter++;
                        }
                    }
                    if(end-begin == t.length()){
                        result.add(begin);
                    }
                    begin++;
                }
                
            }
            return result;
        }
    }
    


### Solution 2
Same idea from a fantastic sliding window template, please refer:  
<https://discuss.leetcode.com/topic/30941/here-is-a-10-line-template-that-can-
solve-most-substring-problems>

Time Complexity will be O(n) because the "start" and "end" points will only
move from left to right once.

    
    
    public List<Integer> findAnagrams(String s, String p) {
        List<Integer> list = new ArrayList<>();
        if (s == null || s.length() == 0 || p == null || p.length() == 0) return list;
        int[] hash = new int[256]; //character hash
        //record each character in p to hash
        for (char c : p.toCharArray()) {
            hash[c]++;
        }
        //two points, initialize count to p's length
        int left = 0, right = 0, count = p.length();
        while (right < s.length()) {
            //move right everytime, if the character exists in p's hash, decrease the count
            //current hash value >= 1 means the character is existing in p
            if (hash[s.charAt(right++)]-- >= 1) count--; 
            
            //when the count is down to 0, means we found the right anagram
            //then add window's left to result list
            if (count == 0) list.add(left);
        
            //if we find the window's size equals to p, then we have to move left (narrow the window) to find the new match window
            //++ to reset the hash because we kicked out the left
            //only increase the count if the character is in p
            //the count >= 0 indicate it was original in the hash, cuz it won't go below 0
            if (right - left == p.length() && hash[s.charAt(left++)]++ >= 0) count++;
        }
        return list;
    }


### Solution 3
I saw a lot of explanations of this including this link

<https://discuss.leetcode.com/topic/30941/here-is-a-10-line-template-that-can-
solve-most-substring-problems>

and couldn't understand any of it until staring at it for an hour or so, to
help solidify it I wrote my own version with extensive comments for each line.
Now I think I finally get how the sliding window works.

    
    
    public class Solution {
        public List<Integer> findAnagrams(String s, String p) {
           ///We will use sliding window template
           
           ArrayList<Integer> soln = new ArrayList<Integer>();
           
           //Check for bad input
           if (s.length() == 0 || p.length() == 0 || s.length() < p.length()){
               return new ArrayList<Integer>();
           }
           
           //Set up character hash
           //Keep track of how many times each character appears
           int[] chars = new int[26];
           for (Character c : p.toCharArray()){
               //Increment to setup hash of all characters currently in the window
               //Later on, these get DECREMENTED when a character is found
               //A positive count later on means that the character is still "needed" in the anagram
               //A negative count means that either the character was found more times than necessary
               //Or that it isn't needed at all
               chars[c-'a']++;
           }
           
           //Start = start poniter, end = end pointer,
           //len = length of anagram to find
           //diff = length of currently found anagram. If it equals
           //the length of anagram to find, it must have been found
           int start = 0, end = 0, len = p.length(), diff = len;
           
           char temp;
           //Before we begin this, the "window" has a length of 0, start and
           //end pointers both at 0
           for (end = 0; end < len; end++){
               //Process current char
               temp = s.charAt(end);
               
               //As discussed earlier, decrement it
               chars[temp-'a']--;
               
               //If it's still >= 0, the anagram still "needed" it so we count it towards the anagram by
               //decrementing diff
               if (chars[temp-'a'] >= 0){
                   diff--;
               }
           }
           
           //This would mean that s began with an anagram of p
           if (diff == 0){
               soln.add(0);
           }
           
           //At this point, start remains at 0, end has moved so that the window is the length of the anagram
           //from this point on we are going to be moving start AND end on each iteration, to shift the window
           //along the string
           while (end < s.length()){
               
               //Temp represents the current first character of the window. The character that is
               //going to be "left behind" as the window moves. 
               temp = s.charAt(start);
               
               //If it's not negative, this means that the character WAS part of the anagram. That means we
               //are one step "farther away" from completing an anagram. So we must increment diff.
               if (chars[temp-'a'] >= 0){
                   diff++;
               }
               
               //Increment the hash value for this character, because it is no longer contained in the window
               chars[temp-'a']++;
               
               //Increment start to start shifting the window over by 1
               start++;
               
               //Temp represents the last character of the window, the "new" character from the window shift.
               //This character "replaces" the one we removed before so the window stays the same length (p.length())
               temp = s.charAt(end);
               
               //Decrement hash value for this character, because it is now a part of the window
               chars[temp-'a']--;
               
               //Again, if it's not negative it is part of the anagram. So decrement diff
               if (chars[temp-'a'] >= 0){
                   diff--;
               }
               
               //If diff has reached zero, that means for the last p.length() iterations, diff was decremented and
               //NOT decremented, which means every one of those characters was in the anagram, so it must be an anagram
               
               //Note: If many windows in a row find anagrams, then each iteration will have diff incremented then decremented again
               if (diff == 0){
                   soln.add(start);
               }
               
               //Increment for next iteration
               end++;
               
           }
           
           return soln;
           
           
        }
    }
    



