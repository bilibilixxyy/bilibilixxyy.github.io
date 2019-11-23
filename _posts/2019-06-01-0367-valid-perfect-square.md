---
layout: post
title: 367. Valid Perfect Square
---
### Question
Given a positive integer _num_ , write a function which returns True if _num_
is a perfect square else False.

 **Note:** **Do not** use any built-in library function such as `sqrt`.

 **Example 1:**

    
    
     **Input:** 16
    **Output:** true
    

**Example 2:**

    
    
    **Input:** 14
    **Output:** false
    

### Solution 1
    
    
    public boolean isPerfectSquare(int num) {
         int i = 1;
         while (num > 0) {
             num -= i;
             i += 2;
         }
         return num == 0;
     }
    

The time complexity is **O(sqrt(n))** , a more efficient one using binary
search whose time complexity is **O(log(n))** :

    
    
    public boolean isPerfectSquare(int num) {
            int low = 1, high = num;
            while (low <= high) {
                long mid = (low + high) >>> 1;
                if (mid * mid == num) {
                    return true;
                } else if (mid * mid < num) {
                    low = (int) mid + 1;
                } else {
                    high = (int) mid - 1;
                }
            }
            return false;
        }
    

One thing to note is that we have to use **long** for mid to avoid **mid*mid**
from overflow. Also, you can use **long** type for **low** and **high** to
avoid type casting for mid from long to int.  
And a third way is to use **Newton Method** to calculate the square root or
num, refer to [Newton
Method](https://en.wikipedia.org/wiki/Integer_square_root#Using_only_integer_division)
for details.

    
    
    public boolean isPerfectSquare(int num) {
            long  x = num;
            while (x * x > num) {
                x = (x + num / x) >> 1;
            }
            return x * x == num;
        }
    


### Solution 2
Just slightly modified my [sqrt
solutions](https://leetcode.com/discuss/58631/3-4-short-lines-integer-newton-
every-language). You can find some explanation there.

(Note I renamed the parameter to x because that's the name in the sqrt problem
and I like it better.)

 **Java, C++, C, C#**

    
    
        long r =  x;
        while (r*r > x)
            r = (r + x/r) / 2;
        return r*r == x;
    

**Python**

    
    
        r =  x
        while r*r > x:
            r = (r + x/r) / 2
        return r*r == x
    

**Ruby**

    
    
        r  =  x
        r = (r + x/r) / 2 while r*r > x
        r*r == x
    

**JavaScript**

    
    
        r =  x;
        while (r*r > x)
            r = ((r + x/r) / 2) | 0;
        return r*r == x;


### Solution 3
  1. Solving with Bitwise trick.

    
    
        def BitwiseTrick(self, num):
           root = 0
           bit = 1 << 15
           
           while bit > 0 :
               root |= bit
               if root > num // root:    
                   root ^= bit                
               bit >>= 1        
           return root * root == num
    

  2. Using Newton's Method

    
    
        def NewtonMethod(self, num):
            r = num
            while r*r > num:
                r = (r + num/r) // 2
            return r*r == num
    

  3. Math Trick for Square number is 1+3+5+ ... +(2n-1)

    
    
        def Math(self, num):
            i = 1
            while (num>0):
                num -= i
                i += 2       
            return num == 0
    

  4. Binary Search Method

    
    
        def BinarySearch(self, num):
            left = 0
            right = num
            
            while left <= right:
                mid = left + (right-left)//2
                if  mid ** 2 == num:
                    return True
                elif mid ** 2 > num:
                    right = mid -1
                else:
                    left = mid +1
            return False
    

  5. Linear Method (Naive) - For comparison

    
    
        def Linear(self, num):
            i = 1
            while i ** 2 <= num:
                if i ** 2 == num:
                    return True
                else:
                    i += 1
            return False
    

I test these five methods with `for i in range(100000): function(i)`, and get
results as below.

Time for Bitwise's Method : 0.45249104499816895  
Time for Newton's Method : 0.3492701053619385  
Time for Math's Method : 2.641957998275757  
Time for Binary Search's Method : 1.5031492710113525  
Time for Linear's Method : 17.613927125930786



