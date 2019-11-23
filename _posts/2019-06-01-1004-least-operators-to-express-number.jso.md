---
layout: post
title: 1004. Least Operators to Express Number
---
### Question
Given a single positive integer `x`, we will write an expression of the form
`x (op1) x (op2) x (op3) x ...` where each operator `op1`, `op2`, etc. is
either addition, subtraction, multiplication, or division (`+`, `-`, `*`, or
`/)`.  For example, with `x = 3`, we might write `3 * 3 / 3 + 3 - 3` which is
a value of 3.

When writing such an expression, we adhere to the following conventions:

  1. The division operator (`/`) returns rational numbers.
  2. There are no parentheses placed anywhere.
  3. We use the usual order of operations: multiplication and division happens before addition and subtraction.
  4. It's not allowed to use the unary negation operator (`-`).  For example, "`x - x`" is a valid expression as it only uses subtraction, but "`-x + x`" is not because it uses negation.

We would like to write an expression with the least number of operators such
that the expression equals the given `target`.  Return the least number of
operators used.



 **Example 1:**

    
    
     **Input:** x = 3, target = 19
    **Output:** 5
    **Explanation:** 3 * 3 + 3 * 3 + 3 / 3.  The expression contains 5 operations.
    

**Example 2:**

    
    
    **Input:** x = 5, target = 501
    **Output:** 8
    **Explanation:** 5 * 5 * 5 * 5 - 5 * 5 * 5 + 5 / 5.  The expression contains 8 operations.
    

**Example 3:**

    
    
    **Input:** x = 100, target = 100000000
    **Output:** 3
    **Explanation:** 100 * 100 * 100 * 100.  The expression contains 3 operations.



 **Note:**

  * `2 <= x <= 100`
  * `1 <= target <= 2 * 10^8`

### Solution 1
    
    
    class Solution(object):
        def leastOpsExpressTarget(self, x, target):
            """
            :type x: int
            :type target: int
            :rtype: int
            """
            # 由于不能有括号，所以每一位（x进制）必须是由自己组成或者由下一位减自己余数组成,所以首先短除法求出每一位的余数
            rl = list()
            while target:
                rl.append(target%x)
                target/=x
            n = len(rl)
            # 在个位的时候，必须用x/x表示1，所以*2，但其它位不用，所以单独先提出
            pos = rl[0] * 2
            neg = (x-rl[0]) * 2
            for i in range(1,n):
                # 正数表示时，可用自己+剩下的正数表示或者多加一个本位然后减去上一位的余数表示
                # 负数表示时，可用自己的余数加上剩下的正数表示或者用自己的余数+剩下的余数，但此时可以合并同级项减少运算符
                # 如在10进制下，86可表示为
                # 80 + 6 （6 为下一位正数表示
                # 80 + 10 - 4 （4 为下一位负数表示）
                # 100 - 20 + 6 （100-20为本位余数表示，6为下一位正数表示
                # 100 - 20 + 10 - 4 （100-20为本位余数表示，10 -4 为下一位余数表示
                # 在此时， 20 和 10注定为同一位且符号相反，可以省去两个符号（一个符号在该位用i 个符号表示（如100为第二位，所以表示为+10 * 10，用两个符号，在此时所有符号均带自己的正负号
                pos, neg = min(rl[i] * i + pos, rl[i] * i + i + neg), min((x - rl[i]) * i + pos, (x-rl[i]) * i + i + neg - 2 * i)
            # 因为在前面算法中所有位都带自己的正负号，所以第一位应该减去自己的符号，所以总量减1
            # 或者在余数表示法中，加上一个更高位的减去自己表示本位余数
            # 所以此题归根结底还是考察对进制的理解而不是简单的理解bfs, dfs，那样复杂度远远高于此，但是是对惯性思维者的一种挑战
            return min(pos-1, n+neg-1)
    


### Solution 2
`pos` the number of operations needed to get `y % (x ^ (k+1))`  
`neg` the number of operations needed to get `x ^ (k + 1) - y % (x ^ (k + 1))`

 **Java:**

    
    
         public int leastOpsExpressTarget(int x, int y) {
            int pos = 0, neg = 0, k = 0, pos2, neg2, cur;
            while (y > 0) {
                cur = y % x;
                y /= x;
                if (k > 0) {
                    pos2 = Math.min(cur * k + pos, (cur + 1) * k + neg);
                    neg2 = Math.min((x - cur) * k + pos, (x - cur - 1) * k + neg);
                    pos = pos2;
                    neg = neg2;
                } else {
                    pos = cur * 2;
                    neg = (x - cur) * 2;
                }
                k++;
            }
            return Math.min(pos, k + neg) - 1;
        }
    

**C++:**

    
    
         int leastOpsExpressTarget(int x, int y) {
            int pos = 0, neg = 0, k, pos2, neg2, cur;
            for (k = 0; y > 0; ++k, y /= x) {
                cur = y % x;
                if (k > 0) {
                    pos2 = min(cur * k + pos, (cur + 1) * k + neg);
                    neg2 = min((x - cur) * k + pos, (x - cur - 1) * k + neg);
                    pos = pos2, neg = neg2;
                } else {
                    pos = cur * 2;
                    neg = (x - cur) * 2;
                }
            }
            return min(pos, k + neg) - 1;
        }
    

**Python:**

    
    
        def leastOpsExpressTarget( self, x, y):
            pos = neg = k = 0
            while y:
                y, cur = divmod(y, x)
                if k:
                    pos, neg = min(cur * k + pos, (cur + 1) * k + neg), min((x - cur) * k + pos, (x - cur - 1) * k + neg)
                else:
                    pos, neg = cur * 2, (x - cur) * 2
                k += 1
            return min(pos, k + neg) - 1
    


### Solution 3
    
    
    int leastOpsExpressTarget(int x, int target) {
    		// At this time, you can get target either by add target times x/x or subtract (x - target) times x/x to x
    		// For example, x = 3, target = 2. Then, 3/3 + 3/3 or 3 - 3/3 is possible result
            if (x > target) {
                return min(target * 2 - 1, (x - target) * 2);
            }
            if (x == target) {  // just push x at the end
                return 0;
            }
            
            long long sums = x;
            int times = 0;
            while (sums < target) {  // this is gready, put as much as possible 'x'
                times++;
                sums *= x;
            }
            
            if (sums == target) {  // one more 'x' you put, one more operator
                return times;
            }
            
    		// when you have remainder, you have two choices, one is add, the other is subtract
    		// for example, x = 3, target = 5. Then, 5 = 3 + 2 or 5 = 9 - 4
            int l = INT_MAX, r = INT_MAX;
            if (sums - target < target) {
                l = leastOpsExpressTarget(x, sums - target) + times;  // using subtract
            }
            r = leastOpsExpressTarget(x, target - (sums / x)) + times - 1;  // using add
            return min(l, r) + 1;  // No matter +/- used, one more operator is add
        }
    

I know it's not the best solution, but I think it might be the baseline to
understand other solutions.



