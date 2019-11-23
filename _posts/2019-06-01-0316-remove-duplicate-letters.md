---
layout: post
title: 316. Remove Duplicate Letters
---
### Question
Given a string which contains only lowercase letters, remove duplicate letters
so that every letter appear once and only once. You must make sure your result
is the smallest in lexicographical order among all possible results.

 **Example 1:**

    
    
     **Input:** "bcabc"
    **Output:** "abc"
    

**Example 2:**

    
    
    **Input:** "cbacdcbc"
    **Output:** "acdb"
    

### Solution 1
Given the string s, the greedy choice (i.e., the leftmost letter in the
answer) is the smallest s[i], s.t.  
the suffix s[i .. ] contains all the unique letters. (Note that, when there
are more than one smallest s[i]'s, we choose the leftmost one. Why? Simply
consider the example: "abcacb".)

After determining the greedy choice s[i], we get a new string s' from s by

  1. removing all letters to the left of s[i],
  2. removing all s[i]'s from s.

We then recursively solve the problem w.r.t. s'.

The runtime is O(26 * n) = O(n).

    
    
    public class Solution {
        public String removeDuplicateLetters(String s) {
            int[] cnt = new int[26];
            int pos = 0; // the position for the smallest s[i]
            for (int i = 0; i < s.length(); i++) cnt[s.charAt(i) - 'a']++;
            for (int i = 0; i < s.length(); i++) {
                if (s.charAt(i) < s.charAt(pos)) pos = i;
                if (--cnt[s.charAt(i) - 'a'] == 0) break;
            }
            return s.length() == 0 ? "" : s.charAt(pos) + removeDuplicateLetters(s.substring(pos + 1).replaceAll("" + s.charAt(pos), ""));
        }
    }


### Solution 2
    
    
    public String removeDuplicateLetters(String sr) {
    
        int[] res = new int[26]; //will contain number of occurences of character (i+'a')
        boolean[] visited = new boolean[26]; //will contain if character (i+'a') is present in current result Stack
        char[] ch = sr.toCharArray();
        for(char c: ch){  //count number of occurences of character 
            res[c-'a']++;
        }
        Stack<Character> st = new Stack<>(); // answer stack
        int index;
        for(char s:ch){ 
            index= s-'a';
            res[index]--;   //decrement number of characters remaining in the string to be analysed
            if(visited[index]) //if character is already present in stack, dont bother
                continue;
            //if current character is smaller than last character in stack which occurs later in the string again
            //it can be removed and  added later e.g stack = bc remaining string abc then a can pop b and then c
            while(!st.isEmpty() && s<st.peek() && res[st.peek()-'a']!=0){ 
                visited[st.pop()-'a']=false;
            }
            st.push(s); //add current character and mark it as visited
            visited[index]=true;
        }
    
        StringBuilder sb = new StringBuilder();
        //pop character from stack and build answer string from back
        while(!st.isEmpty()){
            sb.insert(0,st.pop());
        }
        return sb.toString();
    }


### Solution 3
The basic idea is to find out the smallest result letter by letter (one letter
at a time). Here is the thinking process for input "cbacdcbc":

  1. find out the last appeared position for each letter;  
c - 7  
b - 6  
a - 2  
d - 4

  2. find out the smallest index from the map in step 1 (a - 2);
  3. the first letter in the final result must be the smallest letter from index 0 to index 2;
  4. repeat step 2 to 3 to find out remaining letters.

  * the smallest letter from index 0 to index 2: a
  * the smallest letter from index 3 to index 4: c
  * the smallest letter from index 4 to index 4: d
  * the smallest letter from index 5 to index 6: b

so the result is "acdb"

Notes:

  * after one letter is determined in step 3, it need to be removed from the "last appeared position map", and the same letter should be ignored in the following steps
  * in step 3, the beginning index of the search range should be the index of previous determined letter plus one

* * *
    
    
    public class Solution {
    
        public String removeDuplicateLetters(String s) {
            if (s == null || s.length() <= 1) return s;
    
            Map<Character, Integer> lastPosMap = new HashMap<>();
            for (int i = 0; i < s.length(); i++) {
                lastPosMap.put(s.charAt(i), i);
            }
    
            char[] result = new char[lastPosMap.size()];
            int begin = 0, end = findMinLastPos(lastPosMap);
    
            for (int i = 0; i < result.length; i++) {
                char minChar = 'z' + 1;
                for (int k = begin; k <= end; k++) {
                    if (lastPosMap.containsKey(s.charAt(k)) && s.charAt(k) < minChar) {
                        minChar = s.charAt(k);
                        begin = k+1;
                    }
                }
    
                result[i] = minChar;
                if (i == result.length-1) break;
    
                lastPosMap.remove(minChar);
                if (s.charAt(end) == minChar) end = findMinLastPos(lastPosMap);
            }
    
            return new String(result);
        }
    
        private int findMinLastPos(Map<Character, Integer> lastPosMap) {
            if (lastPosMap == null || lastPosMap.isEmpty()) return -1;
            int minLastPos = Integer.MAX_VALUE;
            for (int lastPos : lastPosMap.values()) {
                 minLastPos = Math.min(minLastPos, lastPos);
            }
            return minLastPos;
        }
    
    }



