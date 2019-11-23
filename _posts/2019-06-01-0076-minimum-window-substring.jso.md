---
layout: post
title: 76. Minimum Window Substring
---
### Question
Given a string S and a string T, find the minimum window in S which will
contain all the characters in T in complexity O(n).

 **Example:**

    
    
     **Input: S** = "ADOBECODEBANC", **T** = "ABC"
    **Output:** "BANC"
    

**Note:**

  * If there is no such window in S that covers all characters in T, return the empty string `""`.
  * If there is such window, you are guaranteed that there will always be only one unique minimum window in S.

### Solution 1
I will first give the solution then show you the magic template.

 **The code of solving this problem is below. It might be the shortest among
all solutions provided in Discuss**.

    
    
     string minWindow(string s, string t) {
            vector<int> map(128,0);
            for(auto c: t) map[c]++;
            int counter=t.size(), begin=0, end=0, d=INT_MAX, head=0;
            while(end<s.size()){
                if(map[s[end++]]-->0) counter--; //in t
                while(counter==0){ //valid
                    if(end-begin<d)  d=end-(head=begin);
                    if(map[s[begin++]]++==0) counter++;  //make it invalid
                }  
            }
            return d==INT_MAX? "":s.substr(head, d);
        }
    

**Here comes the template.**

For most substring problem, we are given a string and need to find a substring
of it which satisfy some restrictions. A general way is to use a hashmap
assisted with two pointers. The template is given below.

    
    
    int findSubstring(string s){
            vector<int> map(128,0);
            int counter; // check whether the substring is valid
            int begin=0, end=0; //two pointers, one point to tail and one  head
            int d; //the length of substring
    
            for() { /* initialize the hash map here */ }
    
            while(end<s.size()){
    
                if(map[s[end++]]-- ?){  /* modify counter here */ }
    
                while(/* counter condition */){ 
                     
                     /* update d here if finding minimum*/
    
                    //increase begin to make it invalid/valid again
                    
                    if(map[s[begin++]]++ ?){ /*modify counter here*/ }
                }  
    
                /* update d here if finding maximum*/
            }
            return d;
      }
    

_One thing needs to be mentioned is that when asked to find maximum substring,
we should update maximum after the inner while loop to guarantee that the
substring is valid. On the other hand, when asked to find minimum substring,
we should update minimum inside the inner while loop._

The code of solving **Longest Substring with At Most Two Distinct Characters**
is below:

    
    
     int lengthOfLongestSubstringTwoDistinct(string s) {
            vector<int> map(128, 0);
            int counter=0, begin=0, end=0, d=0; 
            while(end<s.size()){
                if(map[s[end++]]++==0) counter++;
                while(counter>2) if(map[s[begin++]]--==1) counter--;
                d=max(d, end-begin);
            }
            return d;
        }
    

The code of solving **Longest Substring Without Repeating Characters** is
below:

**Update 01.04.2016, thanks @weiyi3 for advise.**

    
    
     int lengthOfLongestSubstring(string s) {
            vector<int> map(128,0);
            int counter=0, begin=0, end=0, d=0; 
            while(end<s.size()){
                if(map[s[end++]]++>0) counter++; 
                while(counter>0) if(map[s[begin++]]-->1) counter--;
                d=max(d, end-begin); //while valid, update d
            }
            return d;
        }
    

I think this post deserves some upvotes! : )


### Solution 2
The current window is `s[i:j]` and the result window is `s[I:J]`. In `need[c]`
I store how many times I need character `c` (can be negative) and `missing`
tells how many characters are still missing. In the loop, first add the new
character to the window. Then, if nothing is missing, remove as much as
possible from the window start and then update the result.

    
    
    def minWindow(self, s, t):
        need, missing = collections.Counter(t), len(t)
        i = I = J = 0
        for j, c in enumerate(s, 1):
            missing -= need[c] > 0
            need[c] -= 1
            if not missing:
                while i < j and need[s[i]] < 0:
                    need[s[i]] += 1
                    i += 1
                if not J or j - i <= J - I:
                    I, J = i, j
        return s[I:J]


### Solution 3
    
    
    public class Solution {
    public String minWindow(String s, String t) {
        if(s == null || s.length() < t.length() || s.length() == 0){
            return "";
        }
        HashMap<Character,Integer> map = new HashMap<Character,Integer>();
        for(char c : t.toCharArray()){
            if(map.containsKey(c)){
                map.put(c,map.get(c)+1);
            }else{
                map.put(c,1);
            }
        }
        int left = 0;
        int minLeft = 0;
        int minLen = s.length()+1;
        int count = 0;
        for(int right = 0; right < s.length(); right++){
            if(map.containsKey(s.charAt(right))){
                map.put(s.charAt(right),map.get(s.charAt(right))-1);
                if(map.get(s.charAt(right)) >= 0){
                    count ++;
                }
                while(count == t.length()){
                    if(right-left+1 < minLen){
                        minLeft = left;
                        minLen = right-left+1;
                    }
                    if(map.containsKey(s.charAt(left))){
                        map.put(s.charAt(left),map.get(s.charAt(left))+1);
                        if(map.get(s.charAt(left)) > 0){
                            count --;
                        }
                    }
                    left ++ ;
                }
            }
        }
        if(minLen>s.length())  
        {  
            return "";  
        }  
        
        return s.substring(minLeft,minLeft+minLen);
    }
    

}



