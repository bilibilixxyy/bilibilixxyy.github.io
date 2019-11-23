---
layout: post
title: 9. Palindrome Number
---
### Question
Determine whether an integer is a palindrome. An integer is a palindrome when
it reads the same backward as forward.

 **Example 1:**

    
    
     **Input:** 121
    **Output:** true
    

**Example 2:**

    
    
    **Input:** -121
    **Output:** false
    **Explanation:** From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
    

**Example 3:**

    
    
    **Input:** 10
    **Output:** false
    **Explanation:** Reads 01 from right to left. Therefore it is not a palindrome.
    

**Follow up:**

Coud you solve it without converting the integer to a string?

### Solution 1
compare half of the digits in x, so don't need to deal with overflow.

    
    
    public boolean isPalindrome(int x) {
        if (x<0 || (x!=0 && x%10==0)) return false;
        int rev = 0;
        while (x>rev){
        	rev = rev*10 + x%10;
        	x = x/10;
        }
        return (x==rev || x==rev/10);
    }


### Solution 2
    
    
    class Solution {
    public:
        bool isPalindrome(int x) {
            if(x<0|| (x!=0 &&x%10==0)) return false;
            int sum=0;
            while(x>sum)
            {
                sum = sum*10+x%10;
                x = x/10;
            }
            return (x==sum)||(x==sum/10);
        }
    };


### Solution 3
  * Impossible to solve without extra space. Always need space for constants, variables or whatever. Recursion calls will take space for call stack.

  * If you are talking about constant space, then even declaring a string / stack will take constant space. (In fact at most (log(10, INT_MAX) * sizeof char), which is no worse than declaring an integer or more). Actually, even recursion will take constant space.



