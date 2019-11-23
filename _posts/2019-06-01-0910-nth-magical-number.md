---
layout: post
title: 910. Nth Magical Number
---
### Question
A positive integer is _magical_  if it is divisible by either A or B.

Return the N-th magical number.  Since the answer may be very large, **return
it modulo**`10^9 + 7`.



 **Example 1:**

    
    
     **Input:** N = 1, A = 2, B = 3
    **Output:** 2
    

**Example 2:**

    
    
    **Input:** N = 4, A = 2, B = 3
    **Output:** 6
    

**Example 3:**

    
    
    **Input:** N = 5, A = 2, B = 4
    **Output:** 10
    

**Example 4:**

    
    
    **Input:** N = 3, A = 6, B = 4
    **Output:** 8
    



 **Note:**

  1. `1 <= N <= 10^9`
  2. `2 <= A <= 40000`
  3. `2 <= B <= 40000`

### Solution 1
4 points to figure out:

  1. Get gcd (greatest common divisor) and lcm (least common multiple) of (A, B).  
`(a, b) = (A, B) while b > 0: (a, b) = (b, a % b)`  
then, gcd = a and lcm = A * B / a

  2. How many magic numbers `<= x` ?  
By inclusion exclusion principle, we have:  
`x / A + x / B - x / lcm`

  3. Set our binary search range  
Lower bound is `min(A, B)`, I **just** set `left = 2`.  
Upper bound is `N * min(A, B)`, I **just** set `right = 10 ^ 14`.

  4. binary search, find the smallest `x` that `x / A + x / B - x / lcm = N`

    
    
    while (l < r) {
        m = (l + r) / 2;
        if (m / A + m / B - m / (A * B / a) < N) // m too small
            l = m + 1;
        else // m may be too big
            r = m;
    }
    

**Time Complexity** :  
`O(log(10**14))`

**C++:**

    
    
         int nthMagicalNumber(int N, int A, int B) {
            long lcm = A * B / __gcd(A, B), l = 2, r = 1e14, mod = 1e9 + 7;
            while (l < r) {
                long m = (l + r) / 2;
                if (m / A + m / B - m / lcm < N) l = m + 1;
                else r = m;
            }
            return l % mod;
        }
    

**Java:**

    
    
         public int nthMagicalNumber(int N, int A, int B) {
            long a = A, b = B, tmp, l = 2, r = (long)1e14, mod = (long)1e9 + 7;
            while (b > 0) {
                tmp = a;
                a = b;
                b = tmp % b;
            }
            while (l < r) {
                long m = (l + r) / 2;
                if (m / A + m / B - m / (A * B / a) < N) l = m + 1;
                else r = m;
            }
            return (int)(l % mod);
        }
    

**Python:**

    
    
        def nthMagicalNumber(self, N, A, B):
            a, b = A, B
             while b: a, b = b, a % b
            l, r, lcm = 2, 10**14, A * B / a
            while l < r:
                m = (l + r) / 2
                if m / A + m / B - m / lcm < N: l = m + 1
                else: r = m
            return l % (10**9 + 7)
    


### Solution 2
Suppose A =2 and B = 3, then the lcm is 6. The list of magical number less or
equal to 6 is [2,3,4,6]. Then, the 1st to 4th magical number to [2,3,4,6], the
5th to 8th number is 6 added to [2,3,4,6] respectively, the 9th to 12nd number
is 6*2 added to [2,3,4,6] respectively, and so on.

So, the key here is to get all the magical number less or equal to the lcm of
A and B. Then, the Nth number can be obtained immediately.

    
    
    class Solution(object):
    	def gcd(self, x, y):
    		while y > 0:
    			x, y = y, x % y
    		return x
    
    	def lcm(self, x, y):
    		return x*y//self.gcd(x,y)
    
    	def nthMagicalNumber(self, N, A, B):
    		temp = self.lcm(A,B)
    		seq = {}
    		for i in range(1,temp//A+1):
    			seq[i*A] = 1
    		for i in range(1,temp//B+1):
    			seq[i*B] = 1
    		cand = sorted([key for key, value in seq.items()])
    		ans = ((N-1)//len(cand))*cand[-1] + cand[N%len(cand)-1]
    		return ans % (10**9+7)
    


### Solution 3
I have seen most of the solutions contain binary search or brute force search.
Actually we can solve it with pure math solution.  
First we need to find count of the LCM blocks as each LCM block contains fixed
number of magic numbers. For the remain part, instead of using brute force or
binary search. Actually there's linear relationship between count of magic
number (F(x)) and the number (x) in following formular:  
`f(x) = floor(x/A) + floor(x/B)`  
As within an LCD block, there's no overlapping between x/A and x/B.

If we plot this in a chart, it's very close to linear furmular:  
`f(x) = x/A + x/B.`  
But it's always below this line.  
following is the chart exmple for A=3, B=5  
![image](https://s3-lc-
upload.s3.amazonaws.com/users/jianwu/image_1532925395.png)

So solution to get the number within an LCM block is very simple:  
The minimum integer number great than: N / ( 1/A + 1 /B ), that can be divided
either by A or B.

Following is the code passes the test:

    
    
    import static java.lang.Math.ceil;
    import static java.lang.Math.min;
    
    class Solution {
     public int nthMagicalNumber(int N, int A, int B) {
        int MOD = 1_000_000_007;
        long lcm = A * B / gcd(A, B);
        long cntPerLcm = lcm / A + lcm / B - 1;
        long cntLcm = N / cntPerLcm;
        long remain = N % cntPerLcm;
        
        double nearest = remain / (1./A + 1./B);
        int remainIdx = (int)min(ceil(nearest / A) * A, ceil(nearest / B) * B);
        return (int)((cntLcm * lcm + remainIdx) % MOD);
      }
      
      public static int gcd(int A, int B) {
        return B == 0 ? A : gcd(B, A % B);
      }
    }
    



