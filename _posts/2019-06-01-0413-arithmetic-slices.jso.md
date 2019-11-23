---
layout: post
title: 413. Arithmetic Slices
---
### Question
A sequence of number is called arithmetic if it consists of at least three
elements and if the difference between any two consecutive elements is the
same.

For example, these are arithmetic sequence:

    
    
    1, 3, 5, 7, 9
    7, 7, 7, 7
    3, -1, -5, -9

The following sequence is not arithmetic.

    
    
    1, 1, 2, 5, 7

  

A zero-indexed array A consisting of N numbers is given. A slice of that array
is any pair of integers (P, Q) such that 0 <= P < Q < N.

A slice (P, Q) of array A is called arithmetic if the sequence:  
A[P], A[p + 1], ..., A[Q - 1], A[Q] is arithmetic. In particular, this means
that P + 1 < Q.

The function should return the number of arithmetic slices in the array A.

  

**Example:**

    
    
    A = [1, 2, 3, 4]
    
    return: 3, for 3 arithmetic slices in A: [1, 2, 3], [2, 3, 4] and [1, 2, 3, 4] itself.
    

### Solution 1
    
    
     public int numberOfArithmeticSlices(int[] A) {
        int curr = 0, sum = 0;
        for (int i=2; i<A.length; i++)
            if (A[i]-A[i-1] == A[i-1]-A[i-2]) {
                curr += 1;
                sum += curr;
            } else {
                curr = 0;
            }
        return sum;
    }


### Solution 2
    
    
    class Solution {
    public:
        int numberOfArithmeticSlices(vector<int>& A) {
            int n = A.size();
            if (n < 3) return 0;
            vector<int> dp(n, 0); // dp[i] means the number of arithmetic slices ending with A[i]
            if (A[2]-A[1] == A[1]-A[0]) dp[2] = 1; // if the first three numbers are arithmetic or not
            int result = dp[2];
            for (int i = 3; i < n; ++i) {
                // if A[i-2], A[i-1], A[i] are arithmetic, then the number of arithmetic slices ending with A[i] (dp[i])
                // equals to:
                //      the number of arithmetic slices ending with A[i-1] (dp[i-1], all these arithmetic slices appending A[i] are also arithmetic)
                //      +
                //      A[i-2], A[i-1], A[i] (a brand new arithmetic slice)
                // it is how dp[i] = dp[i-1] + 1 comes
                if (A[i]-A[i-1] == A[i-1]-A[i-2]) 
                    dp[i] = dp[i-1] + 1;
                result += dp[i]; // accumulate all valid slices
            }
            return result;
        }
    };
    


### Solution 3
    
    
    public int numberOfArithmeticSlices(int[] A) {
            if(A == null || A.length < 3)
                return 0;
            int sum = 0;
            int len = 2;
    
            for(int i=2;i<A.length;i++) {
    
                // keep increasing the splice
                if(A[i] - A[i-1] == A[i-1] - A[i-2]) {
                    len++;
                }
                else {
                    if(len > 2) {
                        sum += calculateSlices(len);
                    }
                    // reset the length of new slice
                    len = 2;
                }
            }
            // add up the slice in the rear
            if(len>2)
                sum += calculateSlices(len);
    
            return sum;
        }
    
        private int calculateSlices(int n){
            return (n-1)*(n-2)/2;
        }
    



