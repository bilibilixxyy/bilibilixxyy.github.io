---
layout: post
title: 441. Arranging Coins
---
### Question
You have a total of _n_ coins that you want to form in a staircase shape,
where every _k_ -th row must have exactly _k_ coins.

Given _n_ , find the total number of **full** staircase rows that can be
formed.

 _n_ is a non-negative integer and fits within the range of a 32-bit signed
integer.

 **Example 1:**

    
    
    n = 5
    
    The coins can form the following rows:
    ¤
    ¤ ¤
    ¤ ¤
    
    Because the 3rd row is incomplete, we return 2.
    

**Example 2:**

    
    
    n = 8
    
    The coins can form the following rows:
    ¤
    ¤ ¤
    ¤ ¤ ¤
    ¤ ¤
    
    Because the 4th row is incomplete, we return 3.
    

### Solution 1
### [JAVA] Clean Code with Explanations and Running Time [2 Solutions]

[Full Solutions and Explanations](https://ratchapong.com/algorithm-
practice/leetcode/arranging-coins)

### **Solution 1**

``` public class Solution { public int arrangeCoins(int n) { int start = 0;
int end = n; int mid = 0; while (start >> 1; if ((0.5 * mid * mid + 0.5 * mid
)

#### Complexity Analysis

Uniform cost model is used as Cost Model and `n` is the input number. `b` in
this case would be `2`.

 **Time Complexity:**

  * Best Case `O(log_b(n))` : With respect to the input, the algorithm will always depend on the value of input.
  * Average Case `O(log_b(n))` : With respect to the input, the algorithm will always depend on the value of input.
  * Worst Case `O(log_b(n))` : With respect to the input, the algorithm will always depend on the value of input.

 **Auxiliary Space:**

  * Worst Case `O(1)` : Additional variables are of constant size.

#### Algorithm

 **Approach:** Binary Search

The problem is basically asking the maximum length of consecutive number that
has the running sum lesser or equal to `n`. In other word, find `x` that
satisfy the following condition:

`1 + 2 + 3 + 4 + 5 + 6 + 7 + ... + x <= n`

`sum_{i=1}^x i <= n`

Running sum can be simplified,

`(x * ( x + 1)) / 2 <= n`

Binary search is used in this case to slowly narrow down the `x` that will
satisfy the equation. Note that `0.5 * mid * mid + 0.5 * mid` does not have
overflow issue as the intermediate result is implicitly autoboxed to `double`
data type.

* * *

### **Solution 2**

``` public class Solution { public int arrangeCoins(int n) { return (int)
((Math.sqrt(1 + 8.0 * n) - 1) / 2); } } ```

#### Complexity Analysis

Uniform cost model is used as Cost Model and `n` is the input number. `b` in
this case would be `2`.

 **Time Complexity:**

  * Best Case `O(1)` : With respect to the input, the algorithm will always perform basic mathematical operation that run in constant time.
  * Average Case `O(1)` : With respect to the input, the algorithm will always perform basic mathematical operation that run in constant time.
  * Worst Case `O(1)` : With respect to the input, the algorithm will always perform basic mathematical operation that run in constant time.

 **Auxiliary Space:**

  * Worst Case `O(1)` : No extra space is used.

#### Algorithm

 **Approach:** Mathematics

The problem is basically asking the maximum length of consecutive number that
has the running sum lesser or equal to `n`. In other word, find `x` that
satisfy the following condition:

`1 + 2 + 3 + 4 + 5 + 6 + 7 + ... + x <= n`

`sum_{i=1}^x i <= n`

Running sum can be simplified,

`(x * ( x + 1)) / 2 <= n`

Using quadratic formula, `x` is evaluated to be,

`x = 1 / 2 * (-sqrt(8 * n + 1)-1)` (Inapplicable) or `x = 1 / 2 * (sqrt(8 * n
+ 1)-1)`

Negative root is ignored and positive root is used instead. Note that `8.0 *
n` is very important because it will cause Java to implicitly autoboxed the
intermediate result into `double` data type. The code will not work if it is
simply `8 * n`. Alternatively, an explicit casting can be done `8 * (long)
n)`.

* * *


### Solution 2
The idea is about quadratic equation, the formula to get the sum of arithmetic
progression is  
sum = (x + 1) * x / 2  
so for this problem, if we know the the sum, then we can know the x = (-1 +
sqrt(8 * n + 1)) / 2

    
    
    public class Solution {
        public int arrangeCoins(int n) {
            return (int)((-1 + Math.sqrt(1 + 8 * (long)n)) / 2);
        }
    }
    


### Solution 3
**We can solve this problem with pure math**

  * The Coins are arranged in the following way:
    * [1,2,3,4,5....]
  * This is an arithmetic sequence. To find the sum **S** of an arithmetic series , the formula is :
    *  **S = ((a + b)* h) /2**
    * Where:
      *  **a** is the number of coins on the first line
      *  **b** is the number of coins of the last line
      *  **h** is the number of lines we have (we want to find h)
    * We note that **a** is always 1 because we start with one coin
      * Therefore **a = 1**
    * Note that the number of coins on the last line is always the same as the the number of total lines.
      * Therefore: **b = h**
    * The equation can then be simplified into:
      *  **S = (1 + h)*h / 2**
    * Since we want only full lines, we want the sum **S** to be smaller than **n** , the total amount of coins in the question.
    * Therefore:
      *  **(1 + h)*h / 2 <= n**
    * Which simplifies into:
      *  **h^2 + h <= 2n**
    * Or:
      *  **h^2 + h - 2n <= 0**
    * This can be solved using the quadratic formula: (note that a and b here are different from a and b before)
      *  **ax^2 + bx + c = 0**
      *  **x = -b - sqrt(b^2 - 4ac) / 2a**
      *  **x = -b + sqrt(b^2 - 4ac) / 2a**
    * In our case:
      *  **a = 1, b = 1, c = -2n**
    * which yields:
      *  **h ≤ (sqrt(8n + 1) -1)/2**
      *  **h ≥ (-sqrt(8n + 1) -1)/2**
    * Since **n >= 1**, and negative values of **h** are irrelevent, we can ignore the second equation. (It simply requires **h > 0**)
    * Finally, the code is simply:
    
        import math
    class Solution(object):
    def arrangeCoins(self, n):
        """
        :type n: int
        :rtype: int
        """
        #int is simply to floor the floating point so we get the largest integer smaller than the expression
        return int((math.sqrt(8 * n + 1)-1)/2)```



