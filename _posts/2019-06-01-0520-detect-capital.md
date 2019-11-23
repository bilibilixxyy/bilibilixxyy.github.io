---
layout: post
title: 520. Detect Capital
---
### Question
Given a word, you need to judge whether the usage of capitals in it is right
or not.

We define the usage of capitals in a word to be right when one of the
following cases holds:

  1. All letters in this word are capitals, like "USA".
  2. All letters in this word are not capitals, like "leetcode".
  3. Only the first letter in this word is capital, like "Google".

Otherwise, we define that this word doesn't use capitals in a right way.



 **Example 1:**

    
    
     **Input:** "USA"
    **Output:** True
    



**Example 2:**

    
    
    **Input:** "FlaG"
    **Output:** False
    



 **Note:** The input will be a non-empty word consisting of uppercase and
lowercase latin letters.

### Solution 1
    
    
    def detectCapitalUse(self, word):
        return word.isupper() or word.islower() or word.istitle()


### Solution 2
Java

    
    
    public class Solution {
        public boolean detectCapitalUse(String word) {
            int cnt = 0;
            for(char c: word.toCharArray()) if('Z' - c >= 0) cnt++;
            return ((cnt==0 || cnt==word.length()) || (cnt==1 && 'Z' - word.charAt(0)>=0));
        }
    }
    

Python

    
    
    class Solution(object):
        def detectCapitalUse(self, word):
            c = 0
            for i in word:
                if i == i.upper():
                    c += 1
            return c == len(word) or (c == 1 and word[0] == word[0].upper()) or c == 0 
    


### Solution 3
Regex again.

    
    
    public boolean detectCapitalUse(String word) {
        return word.matches("[A-Z]+|[a-z]+|[A-Z][a-z]+");
    }
    



