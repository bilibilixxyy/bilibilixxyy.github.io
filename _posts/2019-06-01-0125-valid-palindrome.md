---
layout: post
title: 125. Valid Palindrome
---
### Question
Given a string, determine if it is a palindrome, considering only alphanumeric
characters and ignoring cases.

 **Note:**  For the purpose of this problem, we define empty string as valid
palindrome.

 **Example 1:**

    
    
     **Input:** "A man, a plan, a canal: Panama"
    **Output:** true
    

**Example 2:**

    
    
    **Input:** "race a car"
    **Output:** false
    

### Solution 1
    
    
    public class Solution {
        public boolean isPalindrome(String s) {
            if (s.isEmpty()) {
            	return true;
            }
            int head = 0, tail = s.length() - 1;
            char cHead, cTail;
            while(head <= tail) {
            	cHead = s.charAt(head);
            	cTail = s.charAt(tail);
            	if (!Character.isLetterOrDigit(cHead)) {
            		head++;
            	} else if(!Character.isLetterOrDigit(cTail)) {
            		tail--;
            	} else {
            		if (Character.toLowerCase(cHead) != Character.toLowerCase(cTail)) {
            			return false;
            		}
            		head++;
            		tail--;
            	}
            }
            
            return true;
        }
    }


### Solution 2
    
    
    public class Solution {
        public boolean isPalindrome(String s) {
            String actual = s.replaceAll("[^A-Za-z0-9]", "").toLowerCase();
            String rev = new StringBuffer(actual).reverse().toString();
            return actual.equals(rev);
        }
    }


### Solution 3
    
    
    bool isPalindrome(string s) {
        for (int i = 0, j = s.size() - 1; i < j; i++, j--) { // Move 2 pointers from each end until they collide
            while (isalnum(s[i]) == false && i < j) i++; // Increment left pointer if not alphanumeric
            while (isalnum(s[j]) == false && i < j) j--; // Decrement right pointer if no alphanumeric
            if (toupper(s[i]) != toupper(s[j])) return false; // Exit and return error if not match
        }
        
        return true;
    }



