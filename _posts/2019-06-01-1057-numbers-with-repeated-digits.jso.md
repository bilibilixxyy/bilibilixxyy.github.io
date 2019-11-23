---
layout: post
title: 1057. Numbers With Repeated Digits
---
### Question
Given a positive integer `N`, return the number of positive integers less than
or equal to `N` that have at least 1 repeated digit.



 **Example 1:**

    
    
     **Input:** 20
    **Output:** 1
    **Explanation:** The only positive number (<= 20) with at least 1 repeated digit is 11.
    

**Example 2:**

    
    
    **Input:** 100
    **Output:** 10
    **Explanation:** The positive numbers (<= 100) with atleast 1 repeated digit are 11, 22, 33, 44, 55, 66, 77, 88, 99, and 100.
    

**Example 3:**

    
    
    **Input:** 1000
    **Output:** 262
    



 **Note:**

  1. `1 <= N <= 10^9`

### Solution 1
##  **Intuition**

Count `res` the Number Without Repeated Digit  
Then the number with repeated digits = N - res

Similar as  
788\. Rotated Digits  
902\. Numbers At Most N Given Digit Set

##  **Explanation** :

  1. Transform `N + 1` to arrayList
  2. Count the number with digits < n
  3. Count the number with same prefix

For example,  
if `N = 8765`, `L = [8,7,6,6]`,  
the number without repeated digit can the the following format:  
`XXX`  
`XX`  
`X`  
`1XXX ~ 7XXX`  
`80XX ~ 86XX`  
`870X ~ 875X`  
`8760 ~ 8765`

##  **Time Complexity** :

the number of permutations `A(m,n)` is `O(1)`  
We count digit by digit, so it's `O(logN)`

  

 **Java:**

    
    
         public int numDupDigitsAtMostN(int N) {
            // Transform N + 1 to arrayList
            ArrayList<Integer> L = new ArrayList<Integer>();
            for (int x = N + 1; x > 0; x /= 10)
                L.add(0, x % 10);
    
            // Count the number with digits < N
            int res = 0, n = L.size();
            for (int i = 1; i < n; ++i)
                res += 9 * A(9, i - 1);
    
            // Count the number with same prefix
            HashSet<Integer> seen = new HashSet<>();
            for (int i = 0; i < n; ++i) {
                for (int j = i > 0 ? 0 : 1; j < L.get(i); ++j)
                    if (!seen.contains(j))
                        res += A(9 - i, n - i - 1);
                if (seen.contains(L.get(i))) break;
                seen.add(L.get(i));
            }
            return N - res;
        }
    
    
        public int A(int m, int n) {
            return n == 0 ? 1 : A(m, n - 1) * (m - n + 1);
        }
    

**Python:**

    
    
        def numDupDigitsAtMostN(self, N):
            L = map( int, str(N + 1))
            res, n = 0, len(L)
    
            def A(m, n):
                return 1 if n == 0 else A(m, n - 1) * (m - n + 1)
    
            for i in range(1, n): res += 9 * A(9, i - 1)
            s = set()
            for i, x in enumerate(L):
                for y in range(0 if i else 1, x):
                    if y not in s:
                        res += A(9 - i, n - i - 1)
                if x in s: break
                s.add(x)
            return N - res
    


### Solution 2
The number of non-repeated digits can be easily calculated with permutaiton.
We only need to exclude all the non-repeated digits to get the answer.

Let's first consider about the cases where N=10^k  
 **N=10**  
the free digits are marked as `*`, so we only need to consider about `*` and
`1*`

  * `*`: obviously all 1-digit numbers are non-repeated, so non-repeated number = 9
  * `1*`: we only need to consider about `1* <= 10`, so non-repeated number = 1

Thus, the result for N=10 is:  
`N - #non_repeat(*) - #non_repeat(1*) = 10 - 9 - 1 = 0`

 **N=100**  
the free digits are marked as `*`, so we only need to consider about `*`,
`**`, and `1**`

  * `*`: obviously all 1-digit numbers are non-repeated, so non-repeated number = 9
  * `**`: this can be calculated with permutation: leading digit has 9 options(1-9) and the last 1 digit has `10-1` options, thus the total permuation is `9 * permutation(9, 1)=81`. i.e: non-repeated number = 81
  * `1**`: we only need to consider about `1**<=100`, so non-repeated number =0

Thus, the result for N=100 is:  
`N - #non_repeat(*) - #non_repeat(**) - #non_repeat(1**) = 100 - 9 - 81 = 10`

 **N=1000**  
`#non_repeat(***) = 9 * permutation(9, 2) = 9 * 9 * 8 = 648`  
similarly, we can get:  
`N - #non_repeat(*) - #non_repeat(**) - #non_repeat(***) - #non_repeat(1***) =
1000 - 9 - 81 - 648 = 282`

Now, let's consider a more general case:  
 **N=12345**  
actually, we can get the count of non-repeated numbers by counting all non-
repeated numbers in following patterns:

    
    
        *
       **
      ***
     ****
    10***
    11*** (prefix repeated, skip)
    120**
    121** (prefix repeated, skip)
    122** (prefix repeated, skip)
    1230*
    1231* (prefix repeated, skip)
    1232* (prefix repeated, skip)
    1233* (prefix repeated, skip)
    12340
    12341 (prefix repeated, skip)
    12342
    12343
    12344 (prefix repeated, skip)
    12345
    

and use N to minus the count we will get the answer.

Reference implementation:

    
    
    # given number n, see whether n has repeated number
    def has_repeated(n):
        str_n = str(n)
        return len(set(str_n)) != len(str_n)
    
    def permutation(n, k):
        prod = 1
        for i in range(k):
            prod *= (n-i)
        return prod
    
    # calculate number of non-repeated n-digit numbers
    # note: the n-digit number can't start with 0
    # i.e: n_digit_no_repeat(2) calculates the non-repeated
    #   numbers in range [10, 99] (inclusive)
    def n_digit_no_repeat(n):
        if n == 1:
            return 9
        else:
            return  9 * permutation(9, n-1)
    
    class Solution(object):
        def numDupDigitsAtMostN(self, N):
            """
            :type N: int
            :rtype: int
            """        
            N_str = str(N)
            n_digit = len(N_str)
            digits = map(int, N_str)
            result = N - 1
            prefix = 0
            for i in range(1, n_digit):
                result -= n_digit_no_repeat(i)
            for i in range(n_digit):
                # when we fix the most significant digit, it 
                # can't be zero
                start = 0 if i else 1
                for j in range(start, digits[i]):
                    if has_repeated(prefix * 10 + j):
                        continue
                    if i != n_digit-1:
                        result -= permutation(10-i-1, n_digit-1-i)
                    else:
                        # optmized from `result -= has_repeated(prefix*10+j)`
                        result -= 1
                prefix = prefix*10 + digits[i]
            return result + has_repeated(N)
    


### Solution 3
<https://www.youtube.com/watch?v=yXKBREQbBOg>

enjoy :)



