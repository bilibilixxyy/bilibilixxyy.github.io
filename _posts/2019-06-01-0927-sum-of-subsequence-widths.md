---
layout: post
title: 927. Sum of Subsequence Widths
---
### Question
Given an array of integers `A`, consider all non-empty subsequences of `A`.

For any sequence S, let the  _width_  of S be the difference between the
maximum and minimum element of S.

Return the sum of the widths of all subsequences of A.

As the answer may be very large, **return the answer modulo 10^9 + 7**.



 **Example 1:**

    
    
     **Input:** [2,1,3]
    **Output:** 6
    **Explanation:** Subsequences are [1], [2], [3], [2,1], [2,3], [1,3], [2,1,3].
    The corresponding widths are 0, 0, 0, 1, 1, 2, 2.
    The sum of these widths is 6.
    



 **Note:**

  * `1 <= A.length <= 20000`
  * `1 <= A[i] <= 20000`

### Solution 1
The order in initial arrays doesn't matter,  
my first intuition is to sort the array.

For `A[i]`:  
There are `i` smaller numbers,  
so there are `2 ^ i` sequences in which `A[i]` is maximum.  
we should do `res += A[i] * (2 ^ i)`

There are `n - i - 1` bigger numbers,  
so there are `2 ^ (n - i - 1)` sequences in which `A[i]` is minimum.  
we should do `res -= A[i] * 2 ^ (n - i - 1)`

Done.

 **Time Complexity** :  
O(NlogN)

 **C++:**

    
    
         int sumSubseqWidths(vector<int> A) {
            sort(A.begin(), A.end());
            long c = 1, res = 0, mod = 1e9 + 7;
            for (int i = 0; i < A.size(); ++i, c = (c << 1) % mod)
                res = (res + A[i] * c - A[A.size() - i - 1] * c) % mod;
            return (res + mod) % mod;
        }
    

**Java:**

    
    
        public int sumSubseqWidths(int[] A) {
            Arrays. sort(A);
            long c = 1, res = 0, mod = (long)1e9 + 7;
            for (int i = 0; i < A.length; ++i, c = (c << 1) % mod)
                res = (res + A[i] * c - A[A.length - i - 1] * c) % mod;
            return (int)((res + mod) % mod);
    
        }
    

**1-line Python:**

    
    
        def sumSubseqWidths(self, A):
             return sum(((1 << i) - (1 << len(A) - i - 1)) * a for i, a in enumerate(sorted(A))) % (10**9 + 7)
    

**FAQ**  
 **Q. why do we plus mod before return?**  
A: in Cpp and Java, mod on negative number will still get a negative number.


### Solution 2
<https://www.youtube.com/watch?v=KpPDMI02LVQ>

Did a really bad job this time. One out-of-boundary issue bite me and I was
thinking there must be some LC bug all the time until I stopped screencast.


### Solution 3
We only care about the subsequences and max/min values, so the order of the
elements does not matter. Therefore, we sort the array at first.

For a sorted subarray A=[p,......,q], there are 2^(len(A)-2) subsequences
which has min value=p, and max value=q. Because we can choose any numbers
between p and q, there are 2^(len(A)-2) ways to choose.

For example, a given array is `[0,1,3,4,7]`.  
For the subarray `[1,3,4,7]`, there are 2^2 subsequences which has min value=1
and max value=7.

Then we got an O(N^2) solution:  
the answer is `sum((max(Ai)-min(Ai))×2^(len(Ai)-2))`, for all subarrays `Ai`
where `len(Ai) > 1`.  
For the example above, it is:

    
    
    1*2^0+3*2^1+4*2^2+7*2^3       // = x0
         +2*2^0+3*2^1+6*2^2       // = x1
               +1*2^0+4*2^1       // = x2
                     +3*2^0       // = x3
    

* * *

Of course the efficiency is not enough. Observe the table above↑, we can find
that

    
    
    x0-1*(2^0+2^1+2^2+2^3)=x1*2
    x1-2*(2^0+2^1+2^2)=x2*2
    x2-1*(2^0+2^1)=x3*2
    x3-3*(2^0)=0
    

we can then calculate x3, x2, x1, x0 one by one:

    
    
    x4=0;
    x3=x4*2 + (A4-A3)*(2^0)
    x2=x3*2 + (A3-A2)*(2^0+2^1)
    x1=x2*2 + (A2-A1)*(2^0+2^1+2^2)
    x0=x1*2 + (A1-A0)*(2^0+2^1+2^2+2^3)
    
    result=x4+x3+x2+x1+x0
    

Here is my code:

    
    
    #define M 1000000007
    #define ll long long
    class Solution {
    public:
        int sumSubseqWidths(vector<int>& A) {
            sort(A.begin(), A.end());
            vector<ll> diff;
            for (int i = 0; i < A.size() - 1; i++) diff.push_back(A[i + 1] - A[i]);
            ll result = 0;
            ll x = 0;
            ll sum2 = 1;
            int len = diff.size();
            reverse(diff.begin(), diff.end());
            for (int i = 0; i < len; i++) {
                x <<= 1;
                x += sum2 * diff[i];
                x %= M;
                sum2 <<= 1;
                sum2++;
                sum2 %= M;
                result += x;
                result %= M;
            }
            return result;
        }
    };
    



