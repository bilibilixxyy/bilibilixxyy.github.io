---
layout: post
title: 58. Length of Last Word
---
### Question
Given a string _s_ consists of upper/lower-case alphabets and empty space
characters `' '`, return the length of last word in the string.

If the last word does not exist, return 0.

 **Note:** A word is defined as a character sequence consists of non-space
characters only.

 **Example:**

    
    
     **Input:** "Hello World"
    **Output:** 5
    

### Solution 1
Well, the basic idea is very simple. Start from the tail of `s` and move
backwards to find the first non-space character. Then from this character,
move backwards and count the number of non-space characters until we pass over
the head of `s` or meet a space character. The count will then be the length
of the last word.

    
    
    class Solution {
    public:
        int lengthOfLastWord(string s) { 
            int len = 0, tail = s.length() - 1;
            while (tail >= 0 && s[tail] == ' ') tail--;
            while (tail >= 0 && s[tail] != ' ') {
                len++;
                tail--;
            }
            return len;
        }
    };


### Solution 2
This problem is not fun at all.

You spend 0.1% of the time on thinking of the algorithm, while 99.9% of the
time on fighting against the ' ' and 'a' cases.


### Solution 3
    
    
    public int lengthOfLastWord(String s) {
        return s.trim().length()-s.trim().lastIndexOf(" ")-1;
    }



