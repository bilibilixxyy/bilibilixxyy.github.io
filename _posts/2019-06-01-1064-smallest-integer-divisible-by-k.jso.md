---
layout: post
title: 1064. Smallest Integer Divisible by K
---
### Question
Given a positive integer `K`, you need find the **smallest**  positive integer
`N` such that `N` is divisible by `K`, and `N` only contains the digit **1**.

Return the length of `N`.  If there is no such `N`, return -1.



 **Example 1:**

    
    
     **Input:** 1
    **Output:** 1
    **Explanation:** The smallest answer is N = 1, which has length 1.

**Example 2:**

    
    
    **Input:** 2
    **Output:** -1
    **Explanation:** There is no such positive integer N divisible by 2.

**Example 3:**

    
    
    **Input:** 3
    **Output:** 3
    **Explanation:** The smallest answer is N = 111, which has length 3.



 **Note:**

  * `1 <= K <= 10^5`

### Solution 1
For a given K, we evaluate `1 % K, 11 % K, 111 % K, ..., 11...1 (K '1's) % K`.

  * If any remainder is 0, then the current number is the smallest integer divisible by `K`.
  * If none of the remainders is 0, then at some point, there must be some duplicate remainders (due to [Pigeonhole principle](https://en.wikipedia.org/wiki/Pigeonhole_principle)), as the `K` remainders can only take at most `K-1` different values excluding 0. In this case, no number with the pattern 1...1 is divisible by `K`. This is because once a remainder has a duplicate, the next remainder will be in a loop, as the previous remainder determines the next_mod, i.e., `next_mod = (10 * prev_mod + 1) % K`. Therefore, we will never see remainder==0.

A simple example is when K is 6. Once we see 1111 % 6 = 1, we immediately know
11111 % 6 will be 5, since 1 % 6 = 1 and 11 % 6 = 5. Therefore, there will be
no such number that is divisible by 6.

  * 1 % 6 = 1
  * 11 % 6 = 5
  * 111 % 6 = 3
  * 1111 % 6 = 1
  * 11111 % 6 = 5
  * 111111 % 6 = 3

Also, it is easy to see that for any number whose last digit is not in `{1, 3,
7, 9}`, we should return `-1`.

    
    
    class Solution:
        def smallestRepunitDivByK(self, K: int) -> int:
            if K % 10 not in {1, 3, 7, 9}: return -1
            mod, mod_set = 0, set()
            for length in range(1, K + 1):
                mod = (10 * mod + 1) % K
                if mod == 0: return length
                if mod in mod_set: return -1
                mod_set.add(mod)
            return -1
    


### Solution 2
## **Intuition** :

Let's say the final result has `N` digits of 1.  
If `N` exist, `N <= K`, just do a brute force check.  
Also if `K % 2 == 0`, return -1, because 111....11 can't be even.  
Also if `K % 5 == 0`, return -1, because 111....11 can't end with 0 or 5.  
  

##  **Explanation**

For different `N`, we calculate the remainder of mod `K`.  
It has to use the remainder for these two reason:

  1. Integer overflow
  2. The division operation for big integers, is **NOT** `O(1)`, it's actually depends on the number of digits..

##  **Prove**

Why 5 is a corner case? It has a reason and we can find it.  
Assume that `N = 1` to `N = K`, if there isn't `111...11 % K == 0`  
There are at most `K - 1` different remainders: `1, 2, .... K - 1`.

So this is a pigeon holes problem:  
There must be at least 2 same remainders.

Assume that,  
`f(N) ≡ f(M)`, `N > M`  
`f(N - M) * 10 ^ M ≡ 0`  
`10 ^ M ≡ 0, mod K`  
so that K has factor 2 or factor 5.

Proof by contradiction，  
`If (K % 2 == 0 || K % 5 == 0) return -1;`  
otherwise, there must be a solution `N <= K`.

##  **Time Complexity** :

Time `O(K)`, Space `O(1)`

  

 **Java:**

    
    
         public int smallestRepunitDivByK(int K) {
            if (K % 2 == 0 || K % 5 == 0) return -1;
            int r = 0;
            for (int N = 1; N <= K; ++N) {
                r = (r * 10 + 1) % K;
                if (r == 0) return N;
            }
            return -1;
        }
    

**C++:**

    
    
         int smallestRepunitDivByK(int K) {
            for (int r = 0, N = 1; N <= K; ++N)
                if ((r = (r * 10 + 1) % K) == 0)
                    return N;
            return -1;
        }
    

**Python:**

    
    
        def smallestRepunitDivByK(self, K):
             if K % 2 == 0 or K % 5 == 0: return -1
            r = 0
            for N in xrange(1, K + 1):
                r = (r * 10 + 1) % K
                if not r: return N
    


### Solution 3
This problem is the second problem from Leetcode Contest 129. Among all the
top-25 ranked participants, only 10 of them bounded their loop in K
iterations. Thus, we have the strong motivation to mathematically prove the
title of this post and bound our execution in K iterations. Otherwise, most
people basically set an upper bound as 10^5 according to the note or just
write a "while True" loop.

If this is true, the code in Python can be very elegant like this.

    
    
    def smallestRepunitDivByK(self, K: int) -> int:    
        if K % 2 ==0 or K % 5==0:
            return -1       
        module = 1
        for Ndigits in range(1, K+1):
            if module % K == 0:
                return Ndigits
            module = (module * 10 + 1) % K
        return 1234567890 # This line never executes
    

Proof:  
It is obvious that if K is the multiple of 2 or multiple of 5, N is definitely
not multiple of K because the ones digit of N is 1. Thus, return -1 for this
case.

If K is neither multiple of 2 nor multiple of 5, we have this theorem.

Theorem: there must be one number from the K-long candidates list [1, 11, 111,
..., 111..111(with K ones)], which is the multiple of K.

Why? Let's think in the opposite way. Is it possible that none of the K
candidates is the multiple of K?

If true, then the module of these K candidate numbers (mod K) must be in [1,
.., K-1] (K-1 possible module values). Thus, there must be 2 candidate numbers
with the same module. Let's denote these two numbers as N_i with i ones, and
N_j with j ones and i<j.

Thus N_j-N_i = 1111...1000...0 with (j-i) ones and i zeros. N_j-N_i = 111..11
(j-i ones) * 100000..000 (i zeros). We know that N_i and N_j have the same
module of K. Then N_j-N_i is the multiple of K. However, 100000..000 (i zeros)
does not contain any factors of K (K is neither multiple of 2 nor multiple of
5). Thus, 111..11 (j-i ones) is the multiple of K. This is contradictory to
our assumption that all K candidates including 111..11 (j-i ones) are not
multiple of K.

Finally, we know that there is at least one number, from the first K
candidates, which is the multiple of K.



