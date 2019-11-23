---
layout: post
title: 507. Perfect Number
---
### Question
We define the Perfect Number is a **positive** integer that is equal to the
sum of all its **positive** divisors except itself.

Now, given an **integer** n, write a function that returns true when it is a
perfect number and false when it is not.

 **Example:**  

    
    
     **Input:** 28
    **Output:** True
    **Explanation:** 28 = 1 + 2 + 4 + 7 + 14
    

**Note:** The input number **n** will not exceed 100,000,000. (1e8)

### Solution 1
    
    
    public class Solution {
        public boolean checkPerfectNumber(int num) {
            if (num == 1) return false;
            
            int sum = 0;
            for (int i = 2; i <= Math.sqrt(num); i++) {
                if (num % i == 0) {
                    sum += i;
                    if (i != num / i) sum += num / i;
                }
            }
            sum++;
            
            return sum == num;
        }
    }
    

`Update` Enlightened by discussion below by @StefanPochmann and @jdrogin, in
the given range we don't need to test `if (i != num / i)` before add `num / i`
to `sum`.

    
    
    public class Solution {
        public boolean checkPerfectNumber(int num) {
            if (num == 1) return false;
            
            int sum = 0;
            for (int i = 2; i <= Math.sqrt(num); i++) {
                if (num % i == 0) {
                    sum += i + num / i;
                }
            }
            sum++;
            
            return sum == num;
        }
    }
    


### Solution 2
The following wrong code gets accepted:

    
    
    def checkPerfectNumber(self, num):
        k = num.bit_length() / 2
        return num > 1 and num == (2 << k) - 1 << k
    

It just checks whether the number has a certain form (k+1 one-bits followed by
k zero-bits). That form is necessary (at least for numbers in the allowed
range, see
[Wikipedia](https://en.wikipedia.org/wiki/Perfect_number#Even_perfect_numbers))
but not sufficient. It incorrectly returns `True` for the following numbers,
so those should be added to the test suite:

120, 2016, 32640, 130816, 523776, 2096128, 8386560

@1337c0d3r


### Solution 3
One thing to keep in mind is the stopping condition of `for` loop is `less or
equal to SQRT`.

    
    
    class Solution(object):
        def checkPerfectNumber(self, num):
            """
            :type num: int
            :rtype: bool
            """
            if num <= 0: return False
            ans, SQRT = 0, int(num ** 0.5)
            ans = sum(i + num//i for i in range(1, SQRT+1) if not num % i)
            if num == SQRT ** 2: ans -= SQRT
            return ans - num == num
    



