---
layout: post
title: 856. Consecutive Numbers Sum
---
### Question
Given a positive integer `N`, how many ways can we write it as a sum of
consecutive positive integers?

 **Example 1:**

    
    
     **Input:** 5
    **Output:** 2
    **Explanation:** 5 = 5 = 2 + 3

**Example 2:**

    
    
    **Input:** 9
    **Output:** 3
    **Explanation:** 9 = 9 = 4 + 5 = 2 + 3 + 4

**Example 3:**

    
    
    **Input:** 15
    **Output:** 4
    **Explanation:** 15 = 15 = 8 + 7 = 4 + 5 + 6 = 1 + 2 + 3 + 4 + 5

 **Note:**  `1 <= N <= 10 ^ 9`.

### Solution 1
The thought process goes like this- Given a number `N`, we can _possibly_
write it as a sum of 2 numbers, 3 numbers, 4 numbers and so on. Let's assume
the fist number in this series be `x`. Hence, we should have  
`x + (x+1) + (x+2)+...+ k terms = N`  
`kx + k*(k-1)/2 = N` implies  
`kx = N - k*(k-1)/2`  
So, we can calculate the RHS for every value of `k` and if it is a multiple of
`k` then we can construct a sum of `N` using `k` terms starting from `x`.  
Now, the question arises, till what value of `k` should we loop for? That's
easy. In the worst case, RHS should be greater than 0. That is  
`N - k*(k-1)/2 > 0` which implies  
`k*(k-1) < 2N` which can be approximated to  
`k*k < 2N ==> k < sqrt(2N)`  
Hence the overall complexity of the algorithm is `O(sqrt(N))`

 **PS** : OJ expects the answer to be 1 greater than the number of possible
ways because it considers `N` as being written as `N` itself. It's confusing
since they ask for sum of consecutive integers which implies atleast 2
numbers. But to please OJ, we should start `count` from 1.

    
    
    class Solution {
    public:
        int consecutiveNumbersSum(int N) {
            int count = 1;
            for( int k = 2; k < sqrt( 2 * N ); k++ ) {
                if ( ( N - ( k * ( k - 1 )/2) ) % k == 0) count++;
            }
            return count;
        }
    };
    


### Solution 2
N can be expressed as k + 1, k + 2, ..., k + i, where k is a positive integer;
therefore

N = k * i + (i + 1) * i / 2 =>  
N - (i + 1) * i / 2 = k * i, which implies that as long as N - (i + 1) * i / 2
is k times of i, we get a solution corresponding to i; Hence iteration of all
possible values of i, starting from 1, will cover all cases of the problem.

Since for i = 1, N can always be written as one number sequence: N, we can
start from i = 2, ans = 1.

    
    
        public int consecutiveNumbersSum(int N) {
            int ans = 1;
            for (int i = 2; i * (i + 1) / 2 <= N; ++i) {
                if ((N - i * (i + 1) / 2) % i == 0) ++ans;
            }
            return ans;
        }
    


### Solution 3
  * 引用自这个博客 <https://zhanghuimeng.github.io/post/leetcode-829-consecutive-numbers-sum/>
  * 实在看不下去了其他啰嗦的解法,就是等差数列好吗
  * 这道题的要求的另一种说法: 把N表示成一个等差数列（公差为1）的和
  * 我们不妨设这个数列的首项是x，项数为m，则这个数列的和就是[x + (x + (m-1))]m / 2 = mx + m(m-1)/2 = N
  * 接下来，一个很自然的想法就是，枚举m，通过上式判断对于相应的m是否存在合法的x。
  * x = ((N - m(m-1)/2)) / m
  * 显然枚举的复杂度是O(sqrt(N))。因为m能取到的最大值显然是sqrt(n)数量级的

    
    
    class Solution {
    public:
        int consecutiveNumbersSum(int N) {
            int ans = 0;
            for (int m = 1; ; m++) {
                int mx = N - m * (m-1) / 2;
                if (mx <= 0)
                    break;
                if (mx % m == 0)
                    ans++;
            }
            return ans;
        }
    };
    
    



