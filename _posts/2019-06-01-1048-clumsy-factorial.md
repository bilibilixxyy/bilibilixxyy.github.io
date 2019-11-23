---
layout: post
title: 1048. Clumsy Factorial
---
### Question
Normally, the factorial of a positive integer `n` is the product of all
positive integers less than or equal to `n`.  For example, `factorial(10) = 10
* 9 * 8 * 7 * 6 * 5 * 4 * 3 * 2 * 1`.

We instead make a _clumsy factorial:_  using the integers in decreasing order,
we swap out the multiply operations for a fixed rotation of operations:
multiply (*), divide (/), add (+) and subtract (-) in this order.

For example, `clumsy(10) = 10 * 9 / 8 + 7 - 6 * 5 / 4 + 3 - 2 * 1`.  However,
these operations are still applied using the usual order of operations of
arithmetic: we do all multiplication and division steps before any addition or
subtraction steps, and multiplication and division steps are processed left to
right.

Additionally, the division that we use is _floor division_  such that `10 * 9
/ 8` equals `11`.  This guarantees the result is an integer.

`Implement the clumsy` function as defined above: given an integer `N`, it
returns the clumsy factorial of `N`.



 **Example 1:**

    
    
     **Input:** 4
    **Output:**  7
    **Explanation:** 7 = 4 * 3 / 2 + 1
    

**Example 2:**

    
    
    **Input:** 10
    **Output:** 12
    **Explanation:** 12 = 10 * 9 / 8 + 7 - 6 * 5 / 4 + 3 - 2 * 1
    



 **Note:**

  1. `1 <= N <= 10000`
  2. `-2^31 <= answer <= 2^31 - 1`  (The answer is guaranteed to fit within a 32-bit integer.)

### Solution 1
As defined in the description of problem, `Additionally, the division that we
use is floor division such that 10 * 9 / 8 equals 11.`

We can easily observe below:

    
    
    5 * 4 / 3 = 6
    6 * 5 / 4 = 7
    10 * 9 / 8 = 11
    ...
    ...
    ...
    
    

so we can get this formula: `i * (i-1) / (i-2) = i+1` when `i >= 5`

we can simplify our computation as below:

    
    
        i * (i-1) / (i-2) + (i-3) - (i-4) * (i-5) / (i-6) + (i-7) - (i-8) * .... + rest elements
    =   (i+1) + "(i-3)" - "(i-4) * (i-5) / (i-6)" + "(i-7)" - "(i-8) * " .... + rest elements
    =   (i+1) + "(i-3) - (i-3)" + "(i-7) - (i-7)" +  ....  + rest elements
    =   (i+1) + rest elements
    

we can call each 4 numbers a `chunk`, so from `N // 4` we can know how many
chunks there are, then the rest `0`, `1`, `2` and `3` elements will influence
our final result.

  1. when `0` element left: final result is `(i+1) + ... + 5 - (4*3/2) + 1`, which is `i+1`
  2. when `1` element left: final result is `(i+1) + ... + 6 - (5*4/3) + 2 - 1`, which is `i+2`
  3. when `2` element left: final result is `(i+1) + ... + 7 - (6*5/4) + 3 - 2 * 1`, which is `i+2`
  4. when `3` element left: final result is `(i+1) + ... + 8 - (7*6/5) + 4 - 3 * 2 / 1`, which is `i-1`

After consider the corner case, we can arrive at the solution:

    
    
    class Solution:
        def clumsy(self, N: int) -> int:
            if N <= 2:
                return N
            if N <= 4:
                return N + 3
            
            if (N - 4) % 4 == 0:
                return N + 1
            elif (N - 4) % 4 <= 2:
                return N + 2
            else:
                return N - 1
    

Simple implementation, credit to `@motorix`

    
    
    class Solution:
        def clumsy(self, N: int) -> int:
            magic = [1, 2, 2, -1, 0, 0, 3, 3]
            return N + (magic[N % 4] if N > 4 else magic[N + 3])
    


### Solution 2
## Solution 1, Brute Force

 **Java:**

    
    
         public int clumsy(int N) {
            if (N == 0) return 0;
            if (N == 1) return 1;
            if (N == 2) return 2;
            if (N == 3) return 6;
            return N * (N - 1) / (N - 2) + helper(N - 3);
        }
        public int helper(int N) {
            if (N == 0) return 0;
            if (N == 1) return 1;
            if (N == 2) return 1;
            if (N == 3) return 1;
            return N - (N - 1) * (N - 2) / (N - 3) + helper(N - 4);
        }
    

  

## Solution 2, Improve to O(1)

`N * N - 3 * N = N * N - 3 * N + 2 - 2`  
`N * (N - 3) = (N - 1) * (N - 2) - 2`。（factorization）  
`N = (N - 1) * (N - 2) / (N - 3) - 2 / (N - 3)` （Divide `N - 3` on both side）  
`N - (N - 1) * (N - 2) / (N - 3) = - 2 / (N - 3)`  
`- 2 / (N - 3) = 0`, If `N - 3 > 2`.

So when `N > 5`, `N - (N - 1) * (N - 2) / (N - 3) = 0`

Now it's `O(1)`

    
    
        public int clumsy(int N) {
            if (N == 0) return 0;
            if (N == 1) return 1;
            if (N == 2) return 2;
            if (N == 3) return 6;
            return N * (N - 1) / (N - 2) + helper(N - 3);
        }
        public int helper(int N) {
            if (N == 0) return 0;
            if (N == 1) return 1;
            if (N == 2) return 1;
            if (N == 3) return 1;
            if (N == 4) return -2;
            if (N == 5) return 0;
            return helper((N - 2) % 4 + 2);
        }
    

  

# Solution 3, Better Format

Now make a summary.  
N = 0, return 0  
N = 1, return 1  
N = 2, return 2  
N = 3, return 6  
N = 4, return 7  
N = 5 + 4K, return N + 2  
N = 6 + 4K, return N + 2  
N = 7 + 4K, return N - 1  
N = 8 + 4K, return N + 1

**Java**

    
    
         public int clumsy(int N) {
            if (N == 1) return 1;
            if (N == 2) return 2;
            if (N == 3) return 6;
            if (N == 4) return 7;
            if (N % 4 == 1) return N + 2;
            if (N % 4 == 2) return N + 2;
            if (N % 4 == 3) return N - 1;
            return N + 1;
        }
    

**C++**

    
    
         int clumsy(int N) {
            if (N == 1) return 1;
            if (N == 2) return 2;
            if (N == 3) return 6;
            if (N == 4) return 7;
            if (N % 4 == 1) return N + 2;
            if (N % 4 == 2) return N + 2;
            if (N % 4 == 3) return N - 1;
            return N + 1;
        }
    


### Solution 3
    
    
        def clumsy(self, N):
            op = itertools.cycle("*/+-")
            return eval("".join(str(x) + next(op) for x in range(N, 0, -1))[:-1])
    

`O(1)` version

    
    
        def clumsy(self, N):
            return [0, 1, 2, 6, 7][N] if N < 5 else N + [1, 2, 2, - 1][N % 4]
    



