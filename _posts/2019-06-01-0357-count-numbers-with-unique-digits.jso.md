---
layout: post
title: 357. Count Numbers with Unique Digits
---
### Question
Given a **non-negative** integer n, count all numbers with unique digits, x,
where 0 ≤ x  < 10n.

 **Example:**

    
    
     **Input:** 2
    **Output:** 91 
    **Explanation:** The answer should be the total numbers in the range of 0 ≤ x < 100, 
                 excluding 11,22,33,44,55,66,77,88,99
    

### Solution 1
Following the hint. Let f(n) = count of number with unique digits of length n.

f(1) = 10. (0, 1, 2, 3, ...., 9)

f(2) = 9 * 9. Because for each number i from 1, ..., 9, we can pick j to form
a 2-digit number ij and there are 9 numbers that are different from i for j to
choose from.

f(3) = f(2) * 8 = 9 * 9 * 8. Because for each number with unique digits of
length 2, say ij, we can pick k to form a 3 digit number ijk and there are 8
numbers that are different from i and j for k to choose from.

Similarly f(4) = f(3) * 7 = 9 * 9 * 8 * 7....

...

f(10) = 9 * 9 * 8 * 7 * 6 * ... * 1

f(11) = 0 = f(12) = f(13)....

any number with length > 10 couldn't be unique digits number.

The problem is asking for numbers from 0 to 10^n. Hence return f(1) + f(2) +
.. + f(n)

As @4acreg suggests, There are only 11 different ans. You can create a lookup
table for it. This problem is O(1) in essence.

    
    
      public int countNumbersWithUniqueDigits(int n) {
            if (n == 0)     return 1;
            
            int res = 10;
            int uniqueDigits = 9;
            int availableNumber = 9;
            while (n-- > 1 && availableNumber > 0) {
                uniqueDigits = uniqueDigits * availableNumber;
                res += uniqueDigits;
                availableNumber--;
            }
            return res;
        }


### Solution 2
This is a digit combination problem. Can be solved in at most 10 loops.

When n == 0, return 1. I got this answer from the test case.

When n == 1, _ can put 10 digit in the only position. [0, ... , 10]. Answer is
10.

When n == 2, _ _ first digit has 9 choices [1, ..., 9], second one has 9
choices excluding the already chosen one. So totally 9 * 9 = 81. answer should
be 10 + 81 = 91

When n == 3, _ _ _ total choice is 9 * 9 * 8 = 684. answer is 10 + 81 + 648 =
739

When n == 4, _ _ _ _ total choice is 9 * 9 * 8 * 7.

...

When n == 10, _ _ _ _ _ _ _ _ _ _ total choice is 9 * 9 * 8 * 7 * 6 * 5 * 4 *
3 * 2 * 1

When n == 11, _ _ _ _ _ _ _ _ _ _ _ total choice is 9 * 9 * 8 * 7 * 6 * 5 * 4
* 3 * 2 * 1 * 0 = 0

    
    
    public static int countNumbersWithUniqueDigits(int n) {
        if (n == 0) {
            return 1;
        }
        int ans = 10, base = 9;
        for (int i = 2; i <= n && i <= 10; i++) {
            base = base * (9 - i + 2);
            ans += base;
        }
        return ans;
    }


### Solution 3
    
    
    class Solution(object):
        def countNumbersWithUniqueDigits(self, n):
            """
            :type n: int
            :rtype: int
            """
            choices = [9, 9, 8, 7, 6, 5, 4, 3, 2, 1]
            ans, product = 1, 1
            
            for i in range(n if n <= 10 else 10):
                product *= choices[i]
                ans += product
                
            return ans
    

For the first (most left) digit, we have 9 options (no zero); for the second
digit we used one but we can use 0 now, so 9 options; and we have 1 less
option for each following digits. Number can not be longer than 10 digits.



