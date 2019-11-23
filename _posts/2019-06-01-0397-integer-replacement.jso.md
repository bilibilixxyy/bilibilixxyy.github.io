---
layout: post
title: 397. Integer Replacement
---
### Question
Given a positive integer _n_ and you can do operations as follow:

  1. If _n_ is even, replace _n_ with `_n_ /2`.
  2. If _n_ is odd, you can replace _n_ with either `_n_ + 1` or `_n_ - 1`.

What is the minimum number of replacements needed for _n_ to become 1?

 **Example 1:**

    
    
     **Input:**
    8
    
    **Output:**
    3
    
    **Explanation:**
    8 - > 4 -> 2 -> 1
    

**Example 2:**

    
    
    **Input:**
    7
    
    **Output:**
    4
    
    **Explanation:**
    7 - > 8 -> 4 -> 2 -> 1
    or
    7 -> 6 -> 3 -> 2 -> 1
    

### Solution 1
I really think it should be tagged medium because there are many subtleties
and good understanding of binary arithmetic is required.

The first step towards solution is to realize that you're allowed to remove
the LSB only if it's zero. And to reach the target as fast as possible,
removing digits is the best way to go. Hence, even numbers are better than
odd. This is quite obvious.

What is not so obvious is what to do with odd numbers. One may think that you
just need to remove as many 1's as possible to increase the evenness of the
number. Wrong! Look at this example:

    
    
    111011 -> 111010 -> 11101 -> 11100 -> 1110 -> 111 -> 1000 -> 100 -> 10 -> 1
    

And yet, this is not the best way because

    
    
    111011 -> 111100 -> 11110 -> 1111 -> 10000 -> 1000 -> 100 -> 10 -> 1
    

See? Both `111011 -> 111010` and `111011 -> 111100` remove the same number of
1's, but the second way is better.

So, we just need to remove as many 1's as possible, doing +1 in case of a tie?
Not quite. The infamous test with n=3 fails for that strategy because `11 ->
10 -> 1` is better than `11 -> 100 -> 10 -> 1`. Fortunately, that's the only
exception (or at least I can't think of any other, and there are none in the
tests).

So the logic is:

  1. If `n` is even, halve it.
  2. If `n=3` or `n-1` has less 1's than `n+1`, decrement `n`.
  3. Otherwise, increment `n`.

Here is an example of such a solution in Java:

    
    
    public int integerReplacement(int n) {
        int c = 0;
        while (n != 1) {
            if ((n & 1) == 0) {
                n >>>= 1;
            } else if (n == 3 || Integer.bitCount(n + 1) > Integer.bitCount(n - 1)) {
                --n;
            } else {
                ++n;
            }
            ++c;
        }
        return c;
    }
    

Of course, doing `bitCount` on every iteration is not the best way. It is
enough to examine the last two digits to figure out whether incrementing or
decrementing will give more 1's. Indeed, if a number ends with 01, then
certainly decrementing is the way to go. Otherwise, if it ends with 11, then
certainly incrementing is at least as good as decrementing (`*011 -> *010 /
*100`) or even better (if there are three or more 1's). This leads to the
following solution:

    
    
    public int integerReplacement(int n) {
        int c = 0;
        while (n != 1) {
            if ((n & 1) == 0) {
                n >>>= 1;
            } else if (n == 3 || ((n >>> 1) & 1) == 0) {
                --n;
            } else {
                ++n;
            }
            ++c;
        }
        return c;
    }
    

An alternative approach to intuitive algorithm was very well put by @dettier
in a [discussion](https://discuss.leetcode.com/topic/58330/is-this-greedy-
solution-correct/6): you should create as many trailing zeroes as you can.
This way you can avoid the tie-breaking trap (there can be no ties), but
you'll still have to handle the n=3 exception separately.


### Solution 2
When n is even, the operation is fixed. The procedure is unknown when it is
odd. When n is odd it can be written into the form n = 2k+1 (k is a non-
negative integer.). That is, n+1 = 2k+2 and n-1 = 2k. Then, (n+1)/2 = k+1 and
(n-1)/2 = k. So one of (n+1)/2 and (n-1)/2 is even, the other is odd. And the
"best" case of this problem is to divide as much as possible. Because of that,
always pick n+1 or n-1 based on if it can be divided by 4. The only special
case of that is when n=3 you would like to pick n-1 rather than n+1.

    
    
    public int integerReplacement(int n) {
        if (n == Integer.MAX_VALUE) return 32; //n = 2^31-1;
        int count = 0;
        while (n > 1){
            if (n % 2 == 0) n  /= 2;
            else{
                if ( (n + 1) % 4 == 0 && (n - 1 != 2) ) n++;
                else n--;
            }
            count++;
        }
        return count;
    }


### Solution 3
Denote f(n) the minimum number of jumps from n to 1.  
By definition, we have the recurrence  
f(1) = 0, f(2n) = 1 + f(n), f(2n + 1) = min(f(2n) + 1, f(2n + 2) + 1).  
First notice that this sequence is well defined because f(2n + 2) = f(n + 1) +
1, so f(2n + 1) = min(f(2n) + 1, f(n + 1) + 2). Every element is defined by
some element before it.  
We want to show (*):  
If n % 4 = 3 and n != 3, then f(n) = f(n + 1) + 1.  
If n % 4 = 1 or n = 3, then f(n) = f(n - 1) + 1.  
This gives us an O(log n) time, O(1) space solution.

    
    
    class Solution(object):
        def integerReplacement(self, n):
            rtn = 0
            while n > 1:
                rtn += 1
                if n % 2 == 0:
                    n //= 2
                elif n % 4 == 1 or n == 3:
                    n -= 1
                else:
                    n += 1
            return rtn
    

In this code, n will drop to at most n / 2 in at most 2 iterations, so the
number of iterations is at most 2 * log(n). In each iteration, the time
complexity is constant. So the overall time complexity is O(log n). The space
complexity is obviously 1. Correctness is guaranteed by (*).

Lemma 1. f(k+1) <= f(k) + 1  
Prove by induction:  
f(2) = 1 <= 0 + 1 = f(1) + 1  
Assume this hold for any 1 <= k' < k,  
If k is even, f(k + 1) = min(f(k) + 1, f(k + 2) + 1) <= f(k) + 1;  
If k is odd, denote k = 2l + 1 (l >= 1), then f(k + 1) = f(2l + 2) = 1 + f(l +
1) <= 1 + 1 + f(l) = 1 + f(2l) = 1 + f(k - 1). Also, f(k + 1) = 1 + f(l + 1) =
f(2l + 2) = f(k + 1) <= f(k + 1) + 1. Hence, f(k + 1) <= min(f(k - 1) + 1, f(k
+ 1) + 1) = f(k) <= f(k) + 1.

Lemma 2. f(k) <= 1 + f(k + 1), k >= 1  
Prove by induction:  
f(1) = 0 <= 1 + f(2)  
Assume this hold for any 1 <= k' < k,  
If k is odd, f(k) = min(1 + f(k - 1), 1 + f(k + 1)) <= 1 + f(k + 1)  
If k is even, denote k = 2l (l >= 1), then f(k) = f(2l) = 1 + f(l)  
1 + f(l) <= 3 + f(l) = 2 + f(2l) = 1 + (1 + f(2l))  
1 + f(l) <= 1 + 1 + f(l + 1) <= 3 + f(l + 1) = 2 + f(2l + 2) = 1 + (1 + f(2l +
2))  
=> f(k) = 1 + f(l) <= 1 + min(1 + f(2l), 1 + f(2l + 2)) = 1 + f(2l + 1) = 1 +
f(k + 1).

Proof of (*):

  1. If n % 4 = 3 and n != 3, denote n = 4k + 3 where k >= 1.  
f(n - 1) = f(4k + 2) = 1 + f(2k + 1) = 1 + min(f(2k) + 1, f(2k + 2) + 1) =
min(f(2k) + 2, f(2k + 2) + 2)  
f(2k) + 2 = f(k) + 3 >= f(k + 1) + 2 = 1 + f(2k + 2)  
and f(2k + 2) + 2 > f(2k + 2) + 1, so f(n - 1) >= 1 + f(2k + 2) = f(4k + 4) =
f(n + 1) => f(n) = min(f(n - 1) + 1, f(n + 1) + 1) = f(n + 1) + 1.

  2. If n = 3, it's obvious that f(3) = min(f(2) + 1, f(2) + 2) = f(2) + 1.

  3. If n % 4 = 1 and n > 1, denote n = 4k + 1 where k >= 1.  
f(n - 1) = f(4k) = 1 + f(2k)  
1 + f(2k) < 2 + f(2k)  
1 + f(2k) = 2 + f(k) <= 3 + f(k + 1) = 2 + f(2k + 2)  
=> f(n - 1) = 1 + f(2k) <= min(2 + f(2k), 2 + f(2k + 2)) = 1 + min(f(2k) + 1,
f(2k + 2) + 1) = 1 + f(2k + 1) = f(4k + 2) = f(n + 1)  
=> f(n) = min(f(n - 1) + 1, f(n + 1) + 1) = f(n - 1) + 1.



