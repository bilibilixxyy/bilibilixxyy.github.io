---
layout: post
title: 29. Divide Two Integers
---
### Question
Given two integers `dividend` and `divisor`, divide two integers without using
multiplication, division and mod operator.

Return the quotient after dividing `dividend` by `divisor`.

The integer division should truncate toward zero.

 **Example 1:**

    
    
     **Input:** dividend = 10, divisor = 3
    **Output:** 3

**Example 2:**

    
    
    **Input:** dividend = 7, divisor = -3
    **Output:** -2

 **Note:**

  * Both dividend and divisor will be 32-bit signed integers.
  * The divisor will never be 0.
  * Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [−231,  231 − 1]. For the purpose of this problem, assume that your function returns 231 − 1 when the division result overflows.

### Solution 1
The key observation is that the quotient of a division is just the number of
times that we can subtract the `divisor` from the `dividend` without making it
negative.

Suppose `dividend = 15` and `divisor = 3`, `15 - 3 > 0`. We now try to
subtract more by _shifting_ `3` to the left by `1` bit (`6`). Since `15 - 6 >
0`, shift `6` again to `12`. Now `15 - 12 > 0`, shift `12` again to `24`,
which is larger than `15`. So we can at most subtract `12` from `15`. Since
`12` is obtained by shifting `3` to left twice, it is `1 << 2 = 4` times of
`3`. We add `4` to an answer variable (initialized to be `0`). The above
process is like `15 = 3 * 4 + 3`. We now get part of the quotient (`4`), with
a remaining dividend `3`.

Then we repeat the above process by subtracting `divisor = 3` from the
remaining `dividend = 3` and obtain `0`. We are done. In this case, no shift
happens. We simply add `1 << 0 = 1` to the answer variable.

This is the full algorithm to perform division using bit manipulations. The
sign also needs to be taken into consideration. And we still need to handle
one overflow case: `dividend = INT_MIN` and `divisor = -1`.

    
    
    class Solution {
    public:
        int divide(int dividend, int divisor) {
            if (dividend == INT_MIN && divisor == -1) {
                return INT_MAX;
            }
            long dvd = labs(dividend), dvs = labs(divisor), ans = 0;
            int sign = dividend > 0 ^ divisor > 0 ? -1 : 1;
            while (dvd >= dvs) {
                long temp = dvs, m = 1;
                while (temp << 1 <= dvd) {
                    temp <<= 1;
                    m <<= 1;
                }
                dvd -= temp;
                ans += m;
            }
            return sign * ans;
        }
    };
    


### Solution 2
    
    
    public int divide(int dividend, int divisor) {
    	//Reduce the problem to positive long integer to make it easier.
    	//Use long to avoid integer overflow cases.
    	int sign = 1;
    	if ((dividend > 0 && divisor < 0) || (dividend < 0 && divisor > 0))
    		sign = -1;
    	long ldividend = Math.abs((long) dividend);
    	long ldivisor = Math.abs((long) divisor);
    	
    	//Take care the edge cases.
    	if (ldivisor == 0) return Integer.MAX_VALUE;
    	if ((ldividend == 0) || (ldividend < ldivisor))	return 0;
    	
    	long lans = ldivide(ldividend, ldivisor);
    	
    	int ans;
    	if (lans > Integer.MAX_VALUE){ //Handle overflow.
    		ans = (sign == 1)? Integer.MAX_VALUE : Integer.MIN_VALUE;
    	} else {
    		ans = (int) (sign * lans);
    	}
    	return ans;
    }
    
    private long ldivide(long ldividend, long ldivisor) {
    	// Recursion exit condition
    	if (ldividend < ldivisor) return 0;
    	
    	//  Find the largest multiple so that (divisor * multiple <= dividend), 
    	//  whereas we are moving with stride 1, 2, 4, 8, 16...2^n for performance reason.
    	//  Think this as a binary search.
    	long sum = ldivisor;
    	long multiple = 1;
    	while ((sum+sum) <= ldividend) {
    		sum += sum;
    		multiple += multiple;
    	}
    	//Look for additional value for the multiple from the reminder (dividend - sum) recursively.
    	return multiple + ldivide(ldividend - sum, ldivisor);
    }


### Solution 3
    
    
    class Solution:
    # @return an integer
    def divide(self, dividend, divisor):
        positive = (dividend < 0) is (divisor < 0)
        dividend, divisor = abs(dividend), abs(divisor)
        res = 0
        while dividend >= divisor:
            temp, i = divisor, 1
            while dividend >= temp:
                dividend -= temp
                res += i
                i <<= 1
                temp <<= 1
        if not positive:
            res = -res
        return min(max(-2147483648, res), 2147483647)



