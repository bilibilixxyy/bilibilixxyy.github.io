---
layout: post
title: 209. Minimum Size Subarray Sum
---
### Question
Given an array of **n** positive integers and a positive integer **s** , find
the minimal length of a **contiguous** subarray of which the sum ≥ **s**. If
there isn't one, return 0 instead.

 **Example:  **

    
    
     **Input:** s = 7, nums = [2,3,1,2,4,3]
    **Output:** 2
    **Explanation:** the subarray [4,3] has the minimal length under the problem constraint.

 **Follow up:**

If you have figured out the _O_ ( _n_ ) solution, try coding another solution
of which the time complexity is _O_ ( _n_ log _n_ ).

### Solution 1
    
    
    public int minSubArrayLen(int s, int[] a) {
      if (a == null || a.length == 0)
        return 0;
      
      int i = 0, j = 0, sum = 0, min = Integer.MAX_VALUE;
      
      while (j < a.length) {
        sum += a[j++];
        
        while (sum >= s) {
          min = Math.min(min, j - i);
          sum -= a[i++];
        }
      }
      
      return min == Integer.MAX_VALUE ? 0 : min;
    }


### Solution 2
    
    
    public class Solution {
        public int minSubArrayLen(int s, int[] nums) {
            return solveNLogN(s, nums);
        }
        
        private int solveN(int s, int[] nums) {
            int start = 0, end = 0, sum = 0, minLen = Integer.MAX_VALUE;
            while (end < nums.length) {
                while (end < nums.length && sum < s) sum += nums[end++];
                if (sum < s) break;
                while (start < end && sum >= s) sum -= nums[start++];
                if (end - start + 1 < minLen) minLen = end - start + 1;
            }
            return minLen == Integer.MAX_VALUE ? 0 : minLen;
        }
    
        private int solveNLogN(int s, int[] nums) {
            int[] sums = new int[nums.length + 1];
            for (int i = 1; i < sums.length; i++) sums[i] = sums[i - 1] + nums[i - 1];
            int minLen = Integer.MAX_VALUE;
            for (int i = 0; i < sums.length; i++) {
                int end = binarySearch(i + 1, sums.length - 1, sums[i] + s, sums);
                if (end == sums.length) break;
                if (end - i < minLen) minLen = end - i;
            }
            return minLen == Integer.MAX_VALUE ? 0 : minLen;
        }
        
        private int binarySearch(int lo, int hi, int key, int[] sums) {
            while (lo <= hi) {
               int mid = (lo + hi) / 2;
               if (sums[mid] >= key){
                   hi = mid - 1;
               } else {
                   lo = mid + 1;
               }
            }
            return lo;
        }
    }
    

Since the given array contains only positive integers, the subarray sum can
only increase by including more elements. Therefore, you don't have to include
more elements once the current subarray already has a sum large enough. This
gives the linear time complexity solution by maintaining a minimum window with
a two indices.

As to NLogN solution, logN immediately reminds you of binary search. In this
case, you cannot sort as the current order actually matters. How does one get
an ordered array then? Since all elements are positive, the cumulative sum
must be strictly increasing. Then, a subarray sum can expressed as the
difference between two cumulative sum. Hence, given a start index for the
cumulative sum array, the other end index can be searched using binary search.


### Solution 3
The `O(n)` solution is to use two pointers: `l` and `r`. First we move `r`
until we get a `sum >= s`, then we move `l` to the right until `sum < s`. In
this process, store the minimum length between `l` and `r`. Since each element
in `nums` will be visited by `l` and `r` for at most once. This algorithm is
of `O(n)` time.

    
    
    class Solution {
    public:
        int minSubArrayLen(int s, vector<int>& nums) {
            int l = 0, r = 0, n = nums.size(), sum = 0, len = INT_MAX;
            while (r < n) {
                sum += nums[r++];
                while (sum >= s) {
                    len = min(len, r - l);
                    sum -= nums[l++];
                }
            }
            return len == INT_MAX ? 0 : len;
        }
    };
    

Then comes the `O(nlogn)` solution. This less efficient one turns out to be
more difficult to come up with.

First, we maintain an array of accumulated sums of elements in `nums`
according to the following two equations.

  1. `sums[0] = 0`
  2. `sums[i] = nums[0] + ... + nums[i - 1]` for `i > 0`

Then, for each `sums[i] >= s`, we search for the first `sums[j] > sums[i] - s
(j < i)` using binary search. In this case, we also have `sums[j - 1] <=
sums[i] - s`. If we plug in the definition for `sums`, we have

  * `nums[0] + ... + nums[j - 1] > nums[0] + ... + nums[j - 1] + nums[j] + ... + nums[i - 1] - s`
  * `nums[0] + ... + nums[j - 2] <= nums[0] + ... + nums[j - 2] + nums[j - 1] + ... + nums[i - 1] - s`

If we minus the left-hand side from both inequalities, we have

  * `0 > nums[j] + ... + nums[i - 1] - s`
  * `0 <= nums[j - 1] + ... + nums[i - 1] - s`

So, we have `nums[j - 1] + ... + nums[i - 1] >= s` but `nums[j] + ... + nums[i
- 1] < s`. So `nums[j-1..i-1]` is the shortest subarray with sum not less than
`s` **ending at`i - 1`**. After traversing all possible `i`, we will find out
the shortest subarray with sum not less than `s`.

By the way, a `0` is added to the head of `sums` to account for cases like
`nums = [3], s = 3`.

    
    
    class Solution {
    public:
        int minSubArrayLen(int s, vector<int>& nums) {
            int n = nums.size(), len = INT_MAX;
            vector<int> sums(n + 1, 0);
            for (int i = 1; i <= n; i++) {
                sums[i] = sums[i - 1] + nums[i - 1];
            }
            for (int i = n; i >= 0 && sums[i] >= s; i--) {
                int j = upper_bound(sums.begin(), sums.end(), sums[i] - s) - sums.begin();
                len = min(len, i - j + 1);
            }
            return len == INT_MAX ? 0 : len;
        }
    };
    



