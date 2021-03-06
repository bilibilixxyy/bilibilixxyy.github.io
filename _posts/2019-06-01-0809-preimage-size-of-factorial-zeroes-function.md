---
layout: post
title: 809. Preimage Size of Factorial Zeroes Function
---
### Question
Let `f(x)` be the number of zeroes at the end of `x!`. (Recall that `x! = 1 *
2 * 3 * ... * x`, and by convention, `0! = 1`.)

For example, `f(3) = 0` because 3! = 6 has no zeroes at the end, while `f(11)
= 2` because 11! = 39916800 has 2 zeroes at the end. Given `K`, find how many
non-negative integers `x` have the property that `f(x) = K`.

    
    
     **Example 1:**
    **Input:** K = 0
    **Output:** 5
    **Explanation:** 0!, 1!, 2!, 3!, and 4! end with K = 0 zeroes.
    
    **Example 2:**
    **Input:** K = 5
    **Output:** 0
    **Explanation:** There is no x such that x! ends in K = 5 zeroes.
    

**Note:**

  * `K` will be an integer in the range `[0, 10^9]`.

### Solution 1
Some **basic** observations:

  1. The number of trailing zeros of the factorial `x!` is given by the minimum of `num_2` and `num_5`, where the former is the total number of factor `2` of integers in the range `[1, x]`, while the latter is the total number of factor `5` of these integers. Since we always have more factors of `2` than `5`, the number of trailing zeros of `x!` will always be `num_5`.

  2. `num_5` of `x!` can be computed by summing up the count of integers in the range `[1, x]` that are integer multiples of `5`, `25`, `125`, ..., `5^k`, ..., etc. A simple implementation is as follows:
    
        private long numOfTrailingZeros(long x) {
        long res = 0;
    		
        for (; x > 0; x /= 5) {
            res += x/5;
        }
    		
        return res;
    }
    

  3. Given two non-negative integers `x` and `y`, if `x <= y`, then `numOfTrailingZeros(x) <= numOfTrailingZeros(y)`, which implies `numOfTrailingZeros` is a non-decreasing function of `x`.

Our first binary solution is based on the third observation where we search
the range of integers such that the `numOfTrailingZeros` function is evaluated
to `K`, as shown below.

* * *

**`Binary search solution ONE -- search for x range`** :

This solution takes advantage of the last observation mentioned above, where
we use binary search to find the largest integers `x` and `y` such that
`numOfTrailingZeros(x) <= K` and `numOfTrailingZeros(y) <= K-1`, respectively.
Then the factorial of all integers in the range `(y, x]` (left exclusive,
right inclusive) will have `K` trailing zeros. Therefore the total number of
non-negative integers whose factorials have `K` trailing zeros will be given
by `x - y`.

The following is the Java code for this solution, where the time complexity is
`O((logK)^2)` and space complexity is `O(1)`. Note that `x!` will always have
at least `x/5` trailing zeros, therefore, if `x` is the largest integer such
that `x!` has no more than `K` trailing zeros, then we have `x <= 5 * (K +
1)`, which can serve as the upper bound of our binary search.

    
    
    public int preimageSizeFZF(int K) {
        return (int)(binarySearch(K) - binarySearch(K - 1));
    }
        
    private long binarySearch(int K) {
        long l = 0, r =  5L * (K + 1);
        
        while (l <= r) {
            long m = l + (r - l) / 2;
            long k = numOfTrailingZeros(m);
            
            if (k <= K) {
                l = m + 1;
            } else {
                r = m - 1;
            }
        }
        
        return r;
    }
    

  

* * *

More **advanced** observations:

  1. Let `K = numOfTrailingZeros(x)`, then `K` will jump whenever `x` is an integer multiple of `5` (this is a direct result of the fact that the number of factor `5` in `x!` will change whenever `x` is an integer multiple of `5`). The step of the jump will be given by the number of factor `5` in `x` (no factorial). For example, `K` will jump by `1` step at `x = 5, 10, 15, 20`, but by `2` steps at `25`, as demonstrated in the following plot:

![image](https://s3-lc-
upload.s3.amazonaws.com/users/fun4leetcode/image_1520225872.png)

  2. Since the steps of the jump may be greater than `1`, not all integer values can be taken by `K`. For example, `K` cannot be `5` as the step at `x = 25` is two so `K` will jump from `4` directly to `6`, thus skip the middle value of `5` (see the red dashed line between two brown lines in the above plot). We shall call these skipped integers as invalid values for `K` and conclude there are **NO** integers `x` such that `x!` has `K` trailing zeros at these `K` values. Furthermore, for each valid `K` value, there will be exactly **FIVE** integers `x` such that `x!` has `K` trailing zeros (this is because if `x` is an integer multiple of `5`, then `x+1`, `x+2`, `x+3`, `x+4` will have no factor of `5`, thus the jump step at each of these points will be `0`, which implies `K` will remain the same in the range `[x, x+4]`). In summary, we conclude the number of integers `x` such that `x!` has `K` trailing zeros will be either `0` or `5`, where the former corresponds to invalid `K` values while the latter corresponds to valid `K` values.

  3. As a direct result of the above observation, all the valid `K` values will form an ordered list of ranges where each range consists of `5` continuous integers, i.e., the valid `K` values are: `[0, 5)`, `[6, 11)`, `[12, 17)`, ..., etc. The `K` values will break up whenever `x` is an integer multiple of `25` and the size (i.e., number of elements) of the gap between the two separated ranges will be given by the number of factor `5` in `x` minus `1` (note it's the number of factor minus `1`, not `x`, and is equivalent to the number of factor `5` in `x/5`).

The following three solutions are all based on the fact that the number of
integers `x` such that `x!` has `K` trailing zeros will be `0` when the given
`K` value is invalid, and wil be `5` if the given `K` value is valid. So all
we need to do is to determine whether the given `K` value is valid or not.

Solution **TWO** takes advantage of the fact that if the given `K` value is
valid, then there must exist at least one integer `x` such that `x!` has `K`
trailing zeros.

Solution **THREE** takes advantage of the third observation above and tries to
find the largest valid range with a start point no more than the given `K`.

Solution **FOUR** takes advantage of the recursive structure of the invalid
`K` values and tries to verify directly if the given `K` value falls into this
structure.

* * *

**`Binary search solution TWO -- search for x value`** :

This solution makes use of the aforementioned observation that the total
number of non-negative integers `x` such that `x!` has `K` trailing zeros will
be either `0` or `5`. So we can directly try binary search to find one such
integer. If it exists, the `K` value is valid so we return `5`; otherwise we
return `0`. The time and space complexities are the same as the first solution
above, except now we only need to do the binary search once instead of twice.

    
    
    public int preimageSizeFZF(int K) {
        for (long l = 0, r =  5L * (K + 1); l <= r;) {
            long m = l + (r - l) / 2;
            long k = numOfTrailingZeros(m);
            
            if (k < K) {
                l = m + 1;
            } else if (k > K) {
                r = m - 1;
            } else {
                return 5;
            }
        }
        
        return 0;
    }
    

  

* * *

**`Binary search solution THREE -- search for valid K values`** :

As mentioned above, the valid `K` values will form an ordered list of ranges
separated by gaps. We can order them as follows:

    
    
    index:      0        1           2      ...       m
    range:    [0, 5)   [6, 11)    [12, 17)  ...  [T_m, T_m + 5)
    gaps:  {}       {5}       {11}          ... 
    

If we take all valid ranges and gaps before index `m`, we know those values
will fill up the range `[0, T_m)`, in which there are `m * 5` valid values (we
have `m` valid ranges and each range contains `5` elements), and `sum(gap_i)`
invalid values (where `gap_i` is the number of elements in the `i-th` gap with
`0 <= i <= m`, and we define that the `i-th` gap is immediately preceding the
`i-th` range). So we obtain:

`T_m = m * 5 + sum(gap_i)`

Now to compute `T_m`, we need to evalutae `sum(gap_i)`. To evalutae
`sum(gap_i)`, we need to refer to its definition: the number of elements in
the `i-th` gap. We know that a gap is generated whenever `x` is an integer
multiple of `25` and the number of elements in the gap is given by the number
of factor `5` in `x/5`. Therefore the `i-th` gap corresponds to `x_i = i *
25`, and `sum(gap_i)` is equivalent to the total sum of factor `5` of integers
in the range `[1, 5*m]`, which by definition is given by `numOfTrailingZeros(m
* 5)`. So we arrive at:

`T_m = m * 5 + numOfTrailingZeros(m * 5)`

For the given `K`, we can do a binary search to find the largest index `m`
such that `T_m <= K`, then `K` will be valid if and only if it falls within
the `m-th` range. The following is the code for this solution, with time and
space complexities the same as the first solution.

  

    
    
    public int preimageSizeFZF(int K) {
        long l = 0, r = K/5;
            
        while (l <= r) {
            long m = l + (r - l) / 2;
            long k = m * 5 + numOfTrailingZeros(m * 5);
                
            if (k <= K) {
                l = m + 1;
            } else {
                r = m - 1;
            }
        }
    		
        return (K < r * 5 + numOfTrailingZeros(r * 5) + 5 ? 5 : 0);
    }
    

* * *

**`Binary search solution FOUR -- search for invalid K values`** :

In constrast to the previous solution, this solution tries to directly
identify the set containing all the invalid `K` values and check if the given
`K` value falls into this invalid set. To build the invalid set, we will
divide the `x` values into ranges of the form `(0, 5^m]` and find the skipped
values in the corresponding `K` ranges (recall that the skipping will happen
whenever `x` is an integer multiple of `25`), for example,

  1. `m = 1`: the corresponding `K` range is `[0, 1]` and no values in this range are skipped, so the invalid set is empty. We will denote it as `S1 = {}` with length `f1 = 0`.

  2. `m = 2`: the corresponding `K` range is `[0, 6]` and we do have one value that is skipped, which is `5`. So the invalid set will be `S2 = {5}` with length `f2 = 1`.

  3. `m = 3`: the corresponding `K` range is `[0, 31]` and we have multiple values that are skipped. In this case, the invalid set will be `S3 = {5, 11, 17, 23, 29, 30}` with length `f3 = 6`.

  4. ... and so on

Note that the invalid set of `K` values for the `x` range `(0, 5^m]` can be
constructed from that of `(0, 5^(m-1)]`. This is because `(0, 5^m]` can be
subdivided into five smaller ranges:

`(0, 5^(m-1)]`  
`(5^(m-1), 2 * 5^(m-1)]`  
`(2 * 5^(m-1), 3 * 5^(m-1)]`  
`(3 * 5^(m-1), 4 * 5^(m-1)]`  
`(4 * 5^(m-1), 5 * 5^(m-1)]`

All these subranges will contain the same number of skipped `K` values except
for the last one, which produces one additional invalid `K` value due to an
extra factor of `5` at `x = 5^m`. Let's take `m = 3` as an example, where the
`x` range is `(0, 125]`. The five subranges are `(0, 25]`, `(25, 50]`, `(50,
75]`, `(75, 100]`, `(100, 125]`. There will be one `K` value skipped for each
of the first four ranges: `(0, 25]`, `(25, 50]`, `(50, 75]`, `(75, 100]`, and
two `K` values skipped for the last range `(100, 125]` due to the extra factor
of `5` at `x = 125` (compared to `x = 25`).

So in general we have `f_m = 5 * f_(m-1) + 1`. Now to construct `S_m` from
`S_(m-1)`, we have to find the values skipped in each of the last four
subranges. Note that they are not just copies of `S_(m-1)`, but instead, will
be `S_(m-1)` shifted by some value (that is, a new set obtained by shifting
the elements in `S_(m-1)`). The shifting value will be an integer multiple of
`f_m`, so that we have:

    
    
    Subranges:                          Skipped values
    (0, 5^(m-1)]                        S_(m-1)
    (5^(m-1), 2 * 5^(m-1)]              S_(m-1) + f_m
    (2 * 5^(m-1), 3 * 5^(m-1)]          S_(m-1) + 2 * f_m
    (3 * 5^(m-1), 4 * 5^(m-1)]          S_(m-1) + 3 * f_m
    (4 * 5^(m-1), 5 * 5^(m-1)]          S_(m-1) + 4 * f_m
    

Therefore, `S_m` can be written in terms of `S_(m-1)` as:

`S_m = {S_(m-1)} U {S_(m-1) + f_m} U {S_(m-1) + 2 * f_m} U {S_(m-1) + 3 * f_m}
U {S_(m-1) + 4 * f_m} U {5*f_m}`.

where `U` means union operation and the last single element `5 * f_m` is due
to the extra factor of `5` at `x = 5^m`.

Okay, we have obtained the recurrence relations for `S_m`, but apparently we
cannot afford to compute its elements explicitly (which requires exponential
space and time). So how do we check if the given `K` value is in `S_m` or not?

Our strategy is simple: first check if `K` is the single element `5 * f_m`. If
not, we will take the modulo with respect to `f_m` and recursively check to
see if the remainder is in `S_(m-1)`. We can do this because if an element is
in the set `S_(m-1) + k * f_m`, then the remainder of the element with respect
to `f_m` will be in the set `S_(m-1)`.

So finally, here is the code for this solution. Note that the given `K` value
does not exceed `10^9` so it makes no sense to test `S_m` with size greater
than that, hence we start with the one with size right below `10^9`, which
happens to be `305175781`. The time complexity is then given by `O(logK)` and
space complexity `O(1)`.

  

    
    
    public int preimageSizeFZF(int K) {
        for (int f = 305175781; f >= 1; f = (f - 1) / 5) {
            if (K == 5 * f) return 0;
            K %= f;
        }
        
        return 5;
    }
    

  

Lastly I should point out that [johnsontau](https://leetcode.com/johnsontau/)
shared a post [here](https://leetcode.com/problems/preimage-size-of-factorial-
zeroes-function/discuss/117631/C++-O\(logn\)-math-solution-with-explanation)
with similar ideas. Go take a look if you like.


### Solution 2
    
    
    class Solution {
    public:
        int preimageSizeFZF(int K) {
            int sum[13]={1};
            for (int i=1; i<13; i++) sum[i]=sum[i-1]*5+1;
            for (int i=12; i>=0; i--) {
                if (K/sum[i]==5) return 0;
                K=K%sum[i];
            }
            return 5;
        }
    };
    

Let g(x)=f(x)-f(x-1), so f(x)=sum{g(x)}.  
We can find that  
g(x)=0 if x%5!=0,  
g(x)>=1 if x%5==0,  
g(x)>=2 if x%25==0,  
...  
List g(x) as follows:  
x: 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 ...  
g(x): 0 0 0 0 1 0 0 0 0 1 0 0 0 0 1 0 0 0 0 1 0 0 0 0 1 ...  
x: 5 10 15 20 25 30 35 40 45 50 55 60 65 70 75 80 85 90 95 100 105 110 115 120
125 ...  
g(x): 1 1 1 1 2 1 1 1 1 2 1 1 1 1 2 1 1 1 1 2 1 1 1 1 3 ...  
so g(x) will always repeat a sequnce for 5 times and +1 at the last number of
the sequence.  
Some K is/are skipped if g(x)>1.  
For example, when x=25, g(x)=2, K=5 is skipped(there is no x that f(x)=5), and
when x=125, g(x)=3, K=29, 30 are skipped.  
We can find that 5(=1*5), 11(=6*1+5), 17(=6*2+5), 23(=6*3+5), 29(=6*4+5),
30(=6*5), 36(=31+5), 42(=31+6+5), 48(=31+6*2+5), ... are skipped.  
Let me know if there are ambiguities.


### Solution 3
  1. when `n>=5`, the number of zeros in `n!` is the count of factors of 5. This function `nzero` has a complexity of `O(log(n))`.
  2. therefore, `(5K)!` should have more than `K` zeros, if `K >= 1`.
  3. use a binary search in `[1, 5K]` to find the smallest number `n`, so that `nzero(n) >= K`. This has a complexity of `O(log(5K))`.
  4. if 'nzero(n) == K', the numbers in range `[n, n + 5 - n % 5)` are all valid candidates. **As mentioned by @vikchernij, because the valid`n` should be always a multiple of 5, the result should be always `5` in this case.**
  5. otherwise, there is no valid numbers.
  6. the total complexity is `O(log(5K) * log(5K))`, which is very small.

    
    
    class Solution:
        def preimageSizeFZF(self, K):
            def nzero(n):
                f = 5
                cnt = 0
                while f <= n:
                    cnt += n // f
                    f *= 5
                return cnt
    
            if K == 0:
                return 5
    
            min = 1
            max = K * 5
            while min < max:
                mid = (min + max) // 2
                if nzero(mid) < K:
                    min = mid + 1
                else:
                    max = mid
    
            if nzero(min) != K:
                return 0
            else:
                # next = (min // 5 + 1) * 5
                # return next - min
                return 5
    



