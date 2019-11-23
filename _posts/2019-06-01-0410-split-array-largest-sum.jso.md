---
layout: post
title: 410. Split Array Largest Sum
---
### Question
Given an array which consists of non-negative integers and an integer _m_ ,
you can split the array into _m_ non-empty continuous subarrays. Write an
algorithm to minimize the largest sum among these _m_ subarrays.

 **Note:**  
If _n_ is the length of array, assume the following constraints are satisfied:

  * 1 ≤ _n_ ≤ 1000
  * 1 ≤ _m_ ≤ min(50, _n_ )

 **Examples:**

    
    
    Input:
    **nums** = [7,2,5,10,8]
    **m** = 2
    
    Output:
    18
    
    Explanation:
    There are four ways to split **nums** into two subarrays.
    The best way is to split it into **[7,2,5]** and **[10,8]** ,
    where the largest sum among the two subarrays is only 18.
    

### Solution 1
  1. The answer is between maximum value of input array numbers and sum of those numbers.
  2. Use binary search to approach the correct answer. We have `l = max number of array; r = sum of all numbers in the array;`Every time we do `mid = (l + r) / 2;`
  3. Use greedy to narrow down left and right boundaries in binary search.  
3.1 Cut the array from left.  
3.2 Try our best to make sure that the sum of numbers between each two cuts
(inclusive) is large enough but still less than `mid`.  
3.3 We'll end up with two results: either we can divide the array into more
than m subarrays or we cannot.  
 **If we can** , it means that the `mid` value we pick is too small because
we've already tried our best to make sure each part holds as many non-negative
numbers as we can but we still have numbers left. So, it is impossible to cut
the array into m parts and make sure each parts is no larger than `mid`. We
should increase m. This leads to `l = mid + 1;`  
 **If we can't** , it is either we successfully divide the array into m parts
and the sum of each part is less than `mid`, or we used up all numbers before
we reach m. Both of them mean that we should lower `mid` because we need to
find the minimum one. This leads to `r = mid - 1;`

    
    
    public class Solution {
        public int splitArray(int[] nums, int m) {
            int max = 0; long sum = 0;
            for (int num : nums) {
                max = Math.max(num, max);
                sum += num;
            }
            if (m == 1) return (int)sum;
            //binary search
            long l = max; long r = sum;
            while (l <= r) {
                long mid = (l + r)/ 2;
                if (valid(mid, nums, m)) {
                    r = mid - 1;
                } else {
                    l = mid + 1;
                }
            }
            return (int)l;
        }
        public boolean valid(long target, int[] nums, int m) {
            int count = 1;
            long total = 0;
            for(int num : nums) {
                total += num;
                if (total > target) {
                    total = num;
                    count++;
                    if (count > m) {
                        return false;
                    }
                }
            }
            return true;
        }
    }
    

  * list item


### Solution 2
First thing first, below is the code:

    
    
    class Solution {
    private:
        bool doable (const vector<int>& nums, int cuts, long long max) {
            int acc = 0;
            for (num : nums) {
                // This step is unnecessary for this problem. I didn't discard this line because I want doable function more generalized.
                if (num > max) return false;
                else if (acc + num <= max) acc += num;
                else {
                    --cuts;
                    acc = num;
                    if (cuts < 0) return false;
                }
            }
            return true;
        }
        
    public:
        int splitArray(vector<int>& nums, int m) {
            long long left = 0, right = 0;
            for (num : nums) {
                left = max(left, (long long)num);
                right += num;
            }
            
            while (left < right) {
                long long mid = left + (right - left) / 2;
                if (doable(nums, m - 1, mid)) right = mid;
                else left = mid + 1;
            }
            return left;
        }
    };
    

* * *

## Introduction to this problem:

We can break this problem into two smaller problems:

  * Given an array ( _A_ ), number of cuts ( _CUTS_ ), and the **Largest sum of sub-arrays** ( _MAX_ ). **Can you use at most _CUTS_ cuts to segment array _A_ into _CUTS + 1_ sub-arrays, such that the sum of each sub-array is smaller or equal to _MAX_**?
  * Given a lower bound ( _left_ ), an upper bound ( _right_ ), an unknown bool array ( _B_ ), and an API uses _i_ as input and tells you whether _B[i]_ is true. If we know there exists an index _k_ , **that _B[i]_ is false when i  < k, and _B[i]_ is true when i  >= k**. What is the fastest way to **find this _k_ (the lower bound)?**

* * *

## Solution to the first sub-problem (Skip this part if you already knew how
to solve 1st sub-problem):

For the first question, we can follow these steps:

  * For each element in the array, if its value is larger than _MAX_ , we know it's not possible to cut this array into groups that the sum of all groups are smaller than _MAX_. (Reason is straightforward, if _A_ is [10, 2, 3, 5] and _MAX_ is 6, even you have 3 cuts by which you can cut _A_ as [[10], [2], [3], [5]], the group containing 10 will still be larger than 6).
  * Use **greedy algorithm** to cut _A_. Use an **accumulator _ACC_** to store the sum of the currently processed group, and process elements in _A_ one by one. For each element _num_ , if we add _num_ with _ACC_ and the new sum is still no larger than _MAX_ , we **update _ACC_ to _ACC + num_** , which means we can **merge _num_ into the current group**. If not, we must **use a cut before _num_ to segment this array** , then _num_ will be the first element in the new group.
  * If we **didn't go through _A_ but already used up all cuts** , then it's not possible only using _CUTS_ cuts to segment this array into groups to make sure **sum of each sub-array** is smaller than _MAX_. Otherwise, if we can reach the end of _A_ with cuts left (or use exactly _CUTS_ cuts). It's possible to do so.

Then the first question is solved.

## Solution to the second sub-problem(Skip this part if you already knew how
to solve 2nd sub-problem):

  * The array _B_ will be something like [false, false, ..., false, true, true, ..., true]. We want to find **the index of the first true**.
  * Use **binary search** to find this _k_. Keep a value _mid_ , **mid = (left + right) / 2**. If B[mid] = false, then move the search range to the upper half of the original search range, a.k.a **left = mid + 1** , otherwise move search range to the lower half, a.k.a **right = mid**.

* * *

## Why this algorithm is correct...

  * No matter how we cut the array _A_ , the **Largest sum of sub-arrays** will fall into a range [left, right]. **_Left_ is the value of the largest element in this array. _right_ is the sum of this array.** (e.g., Given array [1, 2, 3, 4, 5], if we have 4 cuts and cut it as [[1], [2], [3], [4], [5]], the **Largest sum of sub-arrays** is 5, it cannot be smaller. And if we have 0 cut, and the only sub-array is [[1, 2, 3, 4, 5]], the **Largest sum of sub-arrays** is 15, it cannot be larger).
  * However, we cannot decide the number of cuts ( _CUTS_ ), this is an given constraint. But we know there must be a magic number _k_ , which is the smallest value of the **Largest sum of sub-arrays** when given _CUTS_ cuts. When the **Largest sum of sub-arrays** is larger than _k_ , we can always find a way to cut _A_ within _CUTS_ cuts. When the **Largest sum of sub-arrays** is smaller than _k_ , there is no way to do this.

## Example

For example, given array _A_ **[1, 2, 3, 4, 5]**. We can use **2** cuts.

  * No matter how many cuts are allowed, the range of the possible value of the **Largest sum of sub-arrays** is [5, 15].
  * When given 2 cuts, we can tell the magic number _k_ here is 6, the result of segmentation is [[1, 2, 3], [4], [5]].
  * When **Largest sum of sub-arrays** is in range [6, 15], we can always find a way to cut this array within two cuts. You can have a try.
  * However, when **Largest sum of sub-arrays** is in range [5, 5], there is no way to do this.
  * This mapped this problem into the second sub-problem. Bool array _B_ here is [5:false, 6:true, 7:true, 8:true, ..., 15:true]. We want to find the **index _i_ of the first true in _B_ , which is the answer of this entire question**, and by solving the first sub-problem, we have an API that can tell us **given an _i_ ( _Largest sum of sub-arrays_ ), whether _B[i]_ is true (whether we can find a way to cut _A_ to satisfy the constraint)**.

**Below is the code with comment, just in case you don't have time to read the
explanations above.**

    
    
    class Solution {
     private:
        /* 
            Params:
                nums - The input array; 
                cuts - How many cuts are available (cuts = #groups - 1); 
                max - The maximum of the (sum of elements in one group);
            Rtn:
                Whether we can use at most 'cuts' number of cuts to segment the entire array, 
                such that the sum of each group will not exceed 'max'.
         */
        bool doable (const vector<int>& nums, int cuts, long long max) {
            
            // 'acc' is the temporary accumulator for the currently processed group.
            
            int acc = 0;
            for (num : nums) {
                
                // If the current processed element in this array is larger than 'max', we cannot segment the array.
                // (Reason is straightforward, if 'nums' is [10, 2, 3, 5] and 'max' is 6, even you can have 3 cuts
                // (by which you can cut array as [[10], [2], [3], [5]]), the group containing 10 will be larger than 6, 
                //  there is no way to do this).
                // Ps: This step is unnecessary in this solution. Because 'left' in the splitArray() function can assure 
                // 'max' will be larger than every single element. I just want to write a generalized doable() function :)
                
                if (num > max) return false;
                
                // If the (sum of the currently processed group) + (current element) is smaller than max, we can add current 
                // element into this group.
                
                else if (acc + num <= max) acc += num;
                
                // If not, we will make a cut before this element, and this element will be the first element in the new group.
                
                else {
                    --cuts;
                    acc = num;
                    
                    // If we've used up all cuts, this means this 'max' is not doable.
                    if (cuts < 0) return false;
                }
            }
            
            // If we can reach here, this means we've used at most 'cuts' cut to segment the array, and the sum of each groups is
            // not larger than 'max'. Yeah!
            return true;
        }
        
    public:
        int splitArray(vector<int>& nums, int m) {
            // Use long long to avoid overflow.
            long long left = 0, right = 0;
            // The smallest possible value ('left') is the the value of the largest element in this array.
            // The largest possible value ('right') is the sum of all elements in this array.
            for (num : nums) {
                left = max(left, (long long)num);
                right += num;
            }
            
            // Use binary search, find the lower bound of the possible (minimum sum of groups within m - 1 cuts).
            while (left < right) {
                long long mid = left + (right - left) / 2;
                if (doable(nums, m - 1, mid)) right = mid;
                else left = mid + 1;
            }
            return left;
        }
    };
    


### Solution 3
DP solution. This is obviously not as good as the binary search solutions; but
it did pass OJ.

`dp[s,j]` is the solution for splitting subarray `n[j]...n[L-1]` into `s`
parts.

`dp[s+1,i] = min{ max(dp[s,j], n[i]+...+n[j-1]) }, i+1 <= j <= L-s`

This solution does not take advantage of the fact that the numbers are non-
negative (except to break the inner loop early). That is a loss. (On the other
hand, it can be used for the problem containing arbitrary numbers)

    
    
    public int splitArray(int[] nums, int m)
    {
        int L = nums.length;
        int[] S = new int[L+1];
        S[0]=0;
        for(int i=0; i<L; i++)
            S[i+1] = S[i]+nums[i];
    
        int[] dp = new int[L];
        for(int i=0; i<L; i++)
            dp[i] = S[L]-S[i];
    
        for(int s=1; s<m; s++)
        {
            for(int i=0; i<L-s; i++)
            {
                dp[i]=Integer.MAX_VALUE;
                for(int j=i+1; j<=L-s; j++)
                {
                    int t = Math.max(dp[j], S[j]-S[i]);
                    if(t<=dp[i])
                        dp[i]=t;
                    else
                        break;
                }
            }
        }
    
        return dp[0];
    }



