---
layout: post
title: 647. Palindromic Substrings
---
### Question
Given a string, your task is to count how many palindromic substrings in this
string.

The substrings with different start indexes or end indexes are counted as
different substrings even they consist of same characters.

 **Example 1:**

    
    
     **Input:** "abc"
    **Output:** 3
    **Explanation:** Three palindromic strings: "a", "b", "c".
    



**Example 2:**

    
    
    **Input:** "aaa"
    **Output:** 6
    **Explanation:** Six palindromic strings: "a", "a", "a", "aa", "aa", "aaa".
    



 **Note:**

  1. The input string length won't exceed 1000.

### Solution 1
Idea is start from each index and try to extend palindrome for both odd and
even length.

    
    
    public class Solution {
        int count = 0;
        
        public int countSubstrings(String s) {
            if (s == null || s.length() == 0) return 0;
            
            for (int i = 0; i < s.length(); i++) { // i is the mid point
                extendPalindrome(s, i, i); // odd length;
                extendPalindrome(s, i, i + 1); // even length
            }
            
            return count;
        }
        
        private void extendPalindrome(String s, int left, int right) {
            while (left >=0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
                count++; left--; right++;
            }
        }
    }
    


### Solution 2
A very easy explanation with an example  
Lets take a string "aabaa"

 **Step 1:** Start a for loop to point at every single character from where we
will trace the palindrome string.  
checkPalindrome(s,i,i); //To check the palindrome of odd length palindromic
sub-string  
checkPalindrome(s,i,i+1); //To check the palindrome of even length palindromic
sub-string

 **Step 2:** From each character of the string, we will keep checking if the
sub-string is a palindrome and increment the palindrome count. To check the
palindrome, keep checking the left and right of the character if it is same or
not.

 **First Loop:**  
![0_1500788783696_300147d3-e98e-4977-83f1-9eb8213a485e-image.png](https://discuss.leetcode.com/assets/uploads/files/1500788789821-300147d3-e98e-4977-83f1-9eb8213a485e-image.png)  
Palindrome: a (Count=1)  
![0_1500788808121_fec1dec5-ab5f-44cf-8dbd-
eb2780e8d65f-image.png](https://discuss.leetcode.com/assets/uploads/files/1500788808273-fec1dec5-ab5f-44cf-8dbd-
eb2780e8d65f-image.png)  
Palindrome: aa (Count=2)

 **Second Loop:**  
![0_1500788845582_881440b8-6dde-4b6f-a864-24fef277069b-image.png](https://discuss.leetcode.com/assets/uploads/files/1500788845825-881440b8-6dde-4b6f-a864-24fef277069b-image.png)  
Palindrome: a (Count=3)  
![0_1500788872920_61fc20cb-0cb2-4179-8f5a-529cbad7a2ec-
image.png](https://discuss.leetcode.com/assets/uploads/files/1500788872992-61fc20cb-0cb2-4179-8f5a-529cbad7a2ec-
image.png)  
Palindrome: No Palindrome

 **Third Loop:**  
![0_1500788901120_bf12b13b-ff32-4703-86cf-0bcb54465428-image.png](https://discuss.leetcode.com/assets/uploads/files/1500788901208-bf12b13b-ff32-4703-86cf-0bcb54465428-image.png)  
Palindrome: b,aba,aabaa (Count=6)  
![0_1500788934388_5cc2c31d-404c-456a-a77d-1432bb0c679b-image.png](https://discuss.leetcode.com/assets/uploads/files/1500788934464-5cc2c31d-404c-456a-a77d-1432bb0c679b-image.png)  
Palindrome: No Palindrome

 **Forth Loop:**  
![0_1500788981884_a2d3f30e-0745-4a75-b2c0-940834bd6a84-image.png](https://discuss.leetcode.com/assets/uploads/files/1500788981974-a2d3f30e-0745-4a75-b2c0-940834bd6a84-image.png)  
Palindrome: a (Count=7)  
![0_1500789009429_f38aa5c2-17ac-47db-8fe9-b9bb4ceb1407-image.png](https://discuss.leetcode.com/assets/uploads/files/1500789009507-f38aa5c2-17ac-47db-8fe9-b9bb4ceb1407-image.png)  
Palindrome: aa (Count=8)

Count = 9 (For the last character)

 **Answer = 9**

'''

    
    
    int count =1;
    public int countSubstrings(String s) {
        if(s.length()==0) 
            return 0;
        for(int i=0; i<s.length()-1; i++){
            checkPalindrome(s,i,i);     //To check the palindrome of odd length palindromic sub-string
            checkPalindrome(s,i,i+1);   //To check the palindrome of even length palindromic sub-string
        }
        return count;
    }    
    
    private void checkPalindrome(String s, int i, int j) {
        while(i>=0 && j<s.length() && s.charAt(i)==s.charAt(j)){    //Check for the palindrome string 
            count++;    //Increment the count if palindromin substring found
            i--;    //To trace string in left direction
            j++;    //To trace string in right direction
        }
    }
    

'''


### Solution 3
This solution is almost same as the DP solution for longest palindromic
substring, instead of storing the longest, just get the count of palindromic
substrings.

    
    
    public int countSubstrings(String s) {
        int n = s.length();
        int res = 0;
        boolean[][] dp = new boolean[n][n];
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                dp[i][j] = s.charAt(i) == s.charAt(j) && (j - i < 3 || dp[i + 1][j - 1]);
                if(dp[i][j]) ++res;
            }
        }
        return res;
    }
    
    



