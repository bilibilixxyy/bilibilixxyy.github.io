---
layout: post
title: 7.Reverse Integer
---
### Question
Given a 32-bit signed integer, reverse digits of an integer.

 **Example 1:**

    
    
     **Input:** 123
    **Output:** 321
    

**Example 2:**

    
    
    **Input:** -123
    **Output:** -321
    

**Example 3:**

    
    
    **Input:** 120
    **Output:** 21
    

**Note:**  
Assume we are dealing with an environment which could only store integers
within the 32-bit signed integer range: [−231,  231 − 1]. For the purpose of
this problem, assume that your function returns 0 when the reversed integer
overflows.

### Solution 1
Only 15 lines.  
If overflow exists, the new result will not equal previous one.  
No flags needed. No hard code like 0xf7777777 needed.  
Sorry for my bad english.

    
    
    public int reverse(int x)
    {
        int result = 0;
    
        while (x != 0)
        {
            int tail = x % 10;
            int newResult = result * 10 + tail;
            if ((newResult - tail) / 10 != result)
            { return 0; }
            result = newResult;
            x = x / 10;
        }
    
        return result;
    }

### Solution 2
    
    
    public int reverse(int x) {
            long rev= 0;
            while( x != 0){
                rev= rev*10 + x % 10;
                x= x/10;
                if( rev > Integer.MAX_VALUE || rev < Integer.MIN_VALUE)
                    return 0;
            }
            return (int) rev;
        }
    

**Update: Not using long:**

    
    
       public int reverse(int x) {
            int prevRev = 0 , rev= 0;
            while( x != 0){
                rev= rev*10 + x % 10;
                if((rev - x % 10) / 10 != prevRev){
                    return 0;
                }
                prevRev = rev;
                x= x/10;
            }
            return rev;
        }
    

### Solution 3
Get the `s`ign, get the `r`eversed absolute integer, and return their product
if `r` didn't "overflow".

    
    
    def reverse(self, x):
        s = cmp(x, 0)
        r = int(`s*x`[::-1])
        return s*r * (r < 2**31)
    

As compressed one-liner, for potential comparison:

    
    
    def reverse(self, x):
        s=cmp(x,0);r=int(`s*x`[::-1]);return(r<2**31)*s*r
    

Anybody got something shorter?


