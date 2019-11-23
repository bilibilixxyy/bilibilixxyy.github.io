---
layout: post
title: 416. Partition Equal Subset Sum
---
### Question
Given a **non-empty** array containing **only positive integers** , find if
the array can be partitioned into two subsets such that the sum of elements in
both subsets is equal.

 **Note:**

  1. Each of the array element will not exceed 100.
  2. The array size will not exceed 200.



 **Example 1:**

    
    
    Input: [1, 5, 11, 5]
    
    Output: true
    
    Explanation: The array can be partitioned as [1, 5, 5] and [11].
    



**Example 2:**

    
    
    Input: [1, 2, 3, 5]
    
    Output: false
    
    Explanation: The array cannot be partitioned into equal sum subsets.
    

### Solution 1
This problem is essentially let us to find whether there are several numbers
in a set which are able to sum to a specific value (in this problem, the value
is sum/2).

Actually, this is a 0/1 knapsack problem, for each number, we can pick it or
not. Let us assume dp[i][j] means whether the specific sum j can be gotten
from the first i numbers. If we can pick such a series of numbers from 0-i
whose sum is j, dp[i][j] is true, otherwise it is false.

Base case: dp[0][0] is true; (zero number consists of sum 0 is true)

Transition function: For each number, if we don't pick it, dp[i][j] =
dp[i-1][j], which means if the first i-1 elements has made it to j, dp[i][j]
would also make it to j (we can just ignore nums[i]). If we pick nums[i].
dp[i][j] = dp[i-1][j-nums[i]], which represents that j is composed of the
current value nums[i] and the remaining composed of other previous numbers.
Thus, the transition function is dp[i][j] = dp[i-1][j] || dp[i-1][j-nums[i]]

talking is cheap:

    
    
    public boolean canPartition(int[] nums) {
        int sum = 0;
        
        for (int num : nums) {
            sum += num;
        }
        
        if ((sum & 1) == 1) {
            return false;
        }
        sum /= 2;
    
        int n = nums.length;
        boolean[][] dp = new boolean[n+1][sum+1];
        for (int i = 0; i < dp.length; i++) {
            Arrays.fill(dp[i], false);
        }
        
        dp[0][0] = true;
        
        for (int i = 1; i < n+1; i++) {
            dp[i][0] = true;
        }
        for (int j = 1; j < sum+1; j++) {
            dp[0][j] = false;
        }
        
        for (int i = 1; i < n+1; i++) {
            for (int j = 1; j < sum+1; j++) {
                dp[i][j] = dp[i-1][j];
                if (j >= nums[i-1]) {
                    dp[i][j] = (dp[i][j] || dp[i-1][j-nums[i-1]]);
                }
            }
        }
       
        return dp[n][sum];
    }
    

But can we optimize it? It seems that we cannot optimize it in time. But we
can optimize in space. We currently use two dimensional array to solve it, but
we can only use one dimensional array.

So the code becomes:

    
    
    public boolean canPartition(int[] nums) {
        int sum = 0;
        
        for (int num : nums) {
            sum += num;
        }
        
        if ((sum & 1) == 1) {
            return false;
        }
        sum /= 2;
        
        int n = nums.length;
        boolean[] dp = new boolean[sum+1];
        Arrays.fill(dp, false);
        dp[0] = true;
        
        for (int num : nums) {
            for (int i = sum; i > 0; i--) {
                if (i >= num) {
                    dp[i] = dp[i] || dp[i-num];
                }
            }
        }
        
        return dp[sum];
    }
    


### Solution 2
Note: as @zhoudayang2 pointed out, the question description has changed (max
array size 100 ==> 200). I have modified my code below according to the new
description, and also made it a bit easier to understand.

Time complexity O(n), size of the bitset is 1256 bytes

    
    
    class Solution {
    public:
        bool canPartition(vector<int>& nums) {
            const int MAX_NUM = 100;
            const int MAX_ARRAY_SIZE = 200;
            bitset<MAX_NUM * MAX_ARRAY_SIZE / 2 + 1> bits(1);
            int sum = 0;
            for (auto n : nums) {
                sum += n;
                bits |= bits << n;
            }
            return !(sum % 2) && bits[sum / 2];
        }
    };
    

It's possible to shorten the solution to 4 lines, by using std::accumulate(),
but that doesn't really make you type less or make it run faster though...

    
    
    class Solution {
    public:
        bool canPartition(vector<int>& nums) {
            bitset<10001> bits(1);
            int sum = accumulate(nums.begin(), nums.end(), 0);
            for (auto n : nums) bits |= bits << n;
            return !(sum & 1) && bits[sum >> 1];
        }
    };
    


### Solution 3
    
    
    public class Solution {
        public boolean canPartition(int[] nums) {
            // check edge case
            if (nums == null || nums.length == 0) {
                return true;
            }
            // preprocess
            int volumn = 0;
            for (int num : nums) {
                volumn += num;
            }
            if (volumn % 2 != 0) {
                return false;
            }
            volumn /= 2;
            // dp def
            boolean[] dp = new boolean[volumn + 1];
            // dp init
            dp[0] = true;
            // dp transition
            for (int i = 1; i <= nums.length; i++) {
                for (int j = volumn; j >= nums[i-1]; j--) {
                    dp[j] = dp[j] || dp[j - nums[i-1]];
                }
            }
            return dp[volumn];
        }
    }
    



