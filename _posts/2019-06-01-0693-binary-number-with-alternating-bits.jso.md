---
layout: post
title: 693. Binary Number with Alternating Bits
---
### Question
Given a positive integer, check whether it has alternating bits: namely, if
two adjacent bits will always have different values.

 **Example 1:**  

    
    
     **Input:** 5
    **Output:** True
    **Explanation:**
    The binary representation of 5 is: 101
    

**Example 2:**  

    
    
    **Input:** 7
    **Output:** False
    **Explanation:**
    The binary representation of 7 is: 111.
    

**Example 3:**  

    
    
    **Input:** 11
    **Output:** False
    **Explanation:**
    The binary representation of 11 is: 1011.
    

**Example 4:**  

    
    
    **Input:** 10
    **Output:** True
    **Explanation:**
    The binary representation of 10 is: 1010.
    

### Solution 1
### Solution 1 - Cancel Bits

    
    
     bool hasAlternatingBits(int n) {
        return !((n ^= n/4) & n-1);
    }
    

Xor the number with itself shifted right twice and check whether everything
after the leading 1-bit became/stayed 0. Xor is 0 iff the bits are equal, so
we get 0-bits iff the pair of leading 1-bit and the 0-bit in front of it are
repeated until the end.

    
    
        000101010
      ^ 000001010
      = 000100000
    

### Solution 2 - Complete Bits

    
    
    bool hasAlternatingBits(int n) {
        return !((n ^= n/2) & n+1);
    }
    

Xor the number with itself shifted right once and check whether everything
after the leading 1-bit became/stayed 1. Xor is 1 iff the bits differ, so we
get 1-bits iff starting with the leading 1-bit, the bits alternate between 1
and 0.

    
    
        000101010
      ^ 000010101
      = 000111111
    

### Solution 3 - Positive RegEx

    
    
    public boolean hasAlternatingBits(int n) {
        return Integer.toBinaryString(n).matches("(10)*1?");
    }
    

It's simple to describe with a regular expression.  
  

### Solution 4 - Negative RegEx

    
    
    def has_alternating_bits(n)
      n.to_s(2) !~ /00|11/
    end
    

It's even simpler to describe what we **don't** want: two zeros or ones in a
row.  
  

### Solution 5 - Negative String

    
    
     def hasAlternatingBits(self, n):
        return '00' not in bin(n) and '11' not in bin(n)
    

Same as before, just not using regex.  
  

### Solution 6 - Golfed

    
    
    def has_alternating_bits(n)
      (n^=n/2)&n+1<1
    end
    

### Solution 7 - Recursion

    
    
    def has_alternating_bits(n)
      n < 3 || n%2 != n/2%2 && has_alternating_bits(n/2)
    end
    

Compare the last two bits and recurse with the last bit shifted out.  
  

### Solution 8 - Complete Bits + RegEx

    
    
    public boolean hasAlternatingBits(int n) {
        return Integer.toBinaryString(n ^ n/2).matches("1+");
    }


### Solution 2
We know that if we shift the number by 1 to the right, all the ones will
become zeros and vice versa. Now if we AND those two numbers, we can get the
whole thing as zero but that won't work for numbers like 2, 4, 8... So we will
take another approach. Instead of AND we will do an XOR . This will make all
bits 1. Now we need to check if all the bits are 1. The best way to do that is
AND the number by (number+1) . It'll give you zero.

    
    
        boolean hasAlternatingBits2(int n) {
            /*
            n =         1 0 1 0 1 0 1 0
            n >> 1      0 1 0 1 0 1 0 1
            n ^ n>>1    1 1 1 1 1 1 1 1
            n           1 1 1 1 1 1 1 1
            n + 1     1 0 0 0 0 0 0 0 0
            n & (n+1)   0 0 0 0 0 0 0 0
            */
    
            n = n ^ (n>>1);
            return (n & n+1) == 0;
        }
    


### Solution 3
    
    
        def hasAlternatingBits(self, n):
            s = bin(n)
            return '00' not in s and '11' not in s



