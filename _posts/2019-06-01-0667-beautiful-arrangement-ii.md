---
layout: post
title: 667. Beautiful Arrangement II
---
### Question
Given two integers `n` and `k`, you need to construct a list which contains
`n` different positive integers ranging from `1` to `n` and obeys the
following requirement:  
Suppose this list is [a1, a2, a3, ... , an], then the list [|a1 \- a2|, |a2 \-
a3|, |a3 \- a4|, ... , |an-1 \- an|] has exactly `k` distinct integers.

If there are multiple answers, print any of them.

 **Example 1:**  

    
    
     **Input:** n = 3, k = 1
    **Output:** [1, 2, 3]
    **Explanation:** The [1, 2, 3] has three different positive integers ranging from 1 to 3, and the [1, 1] has exactly 1 distinct integer: 1.
    

**Example 2:**  

    
    
    **Input:** n = 3, k = 2
    **Output:** [1, 3, 2]
    **Explanation:** The [1, 3, 2] has three different positive integers ranging from 1 to 3, and the [2, 1] has exactly 2 distinct integers: 1 and 2.
    

**Note:**  

  1. The `n` and `k` are in the range 1 <= k < n <= 104.

### Solution 1
if you have `n` number, the maximum `k` can be `n - 1`;  
if `n` is 9, max `k` is 8.  
This can be done by picking numbers interleavingly from head and tail,

    
    
    // start from i = 1, j = n;
    // i++, j--, i++, j--, i++, j--
    
    i: 1   2   3   4   5
    j:   9   8   7   6
    out: 1 9 2 8 3 7 4 6 5
    dif:  8 7 6 5 4 3 2 1
    

Above is a case where `k` is exactly `n - 1`  
When k is less than that, simply lay out the rest `(i, j)` in incremental  
order(all diff is 1). Say if k is 5:

    
    
         i++ j-- i++ j--  i++ i++ i++ ...
    out: 1   9   2   8    3   4   5   6   7
    dif:   8   7   6   5    1   1   1   1 
    

**C++**

    
    
     class Solution {
    public:
        vector<int> constructArray(int n, int k) {
            vector<int> res;
            for (int i = 1, j = n; i <= j; ) {
                if (k > 1) {
                    res.push_back(k-- % 2 ? i++ : j--);
                }
                else {
                    res.push_back(i++);
                }
            }
    
            return res;
        }
    };
    

**C++ Compact**

    
    
     class Solution {
    public:
        vector<int> constructArray(int n, int k) {
            vector<int> res;
            for (int i = 1, j = n; i <= j; )
                res.push_back(k > 1 ? (k-- % 2 ? i++ : j--) : i++;
            return res;
        }
    };
    

**Java**

    
    
     class Solution {
        public int[] constructArray(int n, int k) {
            int[] res = new int[n];
            for (int i = 0, l = 1, r = n; l <= r; i++)
                res[i] = k > 1 ? (k-- % 2 != 0 ? l++ : r--) : l++;
            return res;
        }
    }
    


### Solution 2
When `k = n-1`, a valid construction is `[1, n, 2, n-1, 3, n-2, ....]`. One
way to see this is, we need to have a difference of `n-1`, which means we need
`1` and `n` adjacent; then, we need a difference of `n-2`, etc.

This leads to the following idea: we will put `[1, 2, ...., n-k-1]` first, and
then we have `N = k+1` adjacent numbers left, of which we want `k` different
differences. This is just the answer above translated by `n-k-1`: we'll put
`[n-k, n, n-k+1, n-1, ....]` after.

    
    
    def constructArray(self, n, k):
        ans = range(1, n - k)
        for d in xrange(k+1):
            if d % 2 == 0:
                ans.append(n-k + d/2)
            else:
                ans.append(n - d/2)
    
        return ans
    


### Solution 3
1,n,2,n-1,3,n-2,4... ==> Diff: n-1, n-2, n-3, n-4, n-5...  
By following this pattern, k numbers will have k-1 distinct difference values;  
and all the rest numbers should have |ai - a_i-1| = 1;  
In total, we will have k-1+1 = k distinct values.

    
    
    class Solution {
        public int[] constructArray(int n, int k) {
            if(k>=n) return null;
            int[] arr = new int[n];
            int i = 0, small = 1, large = n;        
            while(i<k){ 
                arr[i++] = small++;
                if(i<k) arr[i++] = large--;
            }        
            if(k%2 == 0){ // k==2 ==> 1, 6, 5,4,3,2
                while(i<arr.length) arr[i++] = large--;
            } else { // k==3 ==> 1,6,2,3,4,5
                while(i<arr.length) arr[i++] = small++;
            }
            return arr;
        }
    }
    



