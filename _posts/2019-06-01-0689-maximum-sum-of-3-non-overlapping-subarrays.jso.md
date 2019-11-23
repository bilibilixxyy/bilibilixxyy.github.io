---
layout: post
title: 689. Maximum Sum of 3 Non-Overlapping Subarrays
---
### Question
In a given array `nums` of positive integers, find three non-overlapping
subarrays with maximum sum.

Each subarray will be of size `k`, and we want to maximize the sum of all
`3*k` entries.

Return the result as a list of indices representing the starting position of
each interval (0-indexed). If there are multiple answers, return the
lexicographically smallest one.

 **Example:**

    
    
     **Input:** [1,2,1,2,6,7,5,1], 2
    **Output:** [0, 3, 5]
    **Explanation:** Subarrays [1, 2], [2, 6], [7, 5] correspond to the starting indices [0, 3, 5].
    We could have also taken [2, 1], but an answer of [1, 3, 5] would be lexicographically larger.
    



 **Note:**

  * `nums.length` will be between 1 and 20000.
  * `nums[i]` will be between 1 and 65535.
  * `k` will be between 1 and floor(nums.length / 3).

### Solution 1
The question asks for three non-overlapping intervals with maximum sum of all
3 intervals. If the middle interval is [i, i+k-1], where k <= i <= n-2k, the
left interval has to be in subrange [0, i-1], and the right interval is from
subrange [i+k, n-1].

So the following solution is based on DP.

    
    
    posLeft[i] is the starting index for the left interval in range [0, i];
    posRight[i] is the starting index for the right interval in range [i, n-1]; 
    

Then we test every possible starting index of middle interval, i.e. k <= i <=
n-2k, and we can get the corresponding left and right max sum intervals easily
from DP. And the run time is O(n).

Caution. In order to get lexicographical smallest order, when there are two
intervals with equal max sum, always select the left most one. So in the code,
the if condition is ">= tot" for right interval due to backward searching, and
"> tot" for left interval. Thanks to @lee215 for pointing this out!

    
    
    class Solution {
    public:
        vector<int> maxSumOfThreeSubarrays(vector<int>& nums, int k) {
            int n = nums.size(), maxsum = 0;
            vector<int> sum = {0}, posLeft(n, 0), posRight(n, n-k), ans(3, 0);
            for (int i:nums) sum.push_back(sum.back()+i);
           // DP for starting index of the left max sum interval
            for (int i = k, tot = sum[k]-sum[0]; i < n; i++) {
                if (sum[i+1]-sum[i+1-k] > tot) {
                    posLeft[i] = i+1-k;
                    tot = sum[i+1]-sum[i+1-k];
                }
                else 
                    posLeft[i] = posLeft[i-1];
            }
            // DP for starting index of the right max sum interval
            // caution: the condition is ">= tot" for right interval, and "> tot" for left interval
            for (int i = n-k-1, tot = sum[n]-sum[n-k]; i >= 0; i--) {
                if (sum[i+k]-sum[i] >= tot) {
                    posRight[i] = i;
                    tot = sum[i+k]-sum[i];
                }
                else
                    posRight[i] = posRight[i+1];
            }
            // test all possible middle interval
            for (int i = k; i <= n-2*k; i++) {
                int l = posLeft[i-1], r = posRight[i+k];
                int tot = (sum[i+k]-sum[i]) + (sum[l+k]-sum[l]) + (sum[r+k]-sum[r]);
                if (tot > maxsum) {
                    maxsum = tot;
                    ans = {l, i, r};
                }
            }
            return ans;
        }
    };
    

Java version

    
    
    class Solution {
        public int[] maxSumOfThreeSubarrays(int[] nums, int k) {
            int n = nums.length, maxsum = 0;
            int[] sum = new int[n+1], posLeft = new int[n], posRight = new int[n], ans = new int[3];
            for (int i = 0; i < n; i++) sum[i+1] = sum[i]+nums[i];
            // DP for starting index of the left max sum interval
            for (int i = k, tot = sum[k]-sum[0]; i < n; i++) {
                if (sum[i+1]-sum[i+1-k] > tot) {
                    posLeft[i] = i+1-k;
                    tot = sum[i+1]-sum[i+1-k];
                }
                else
                    posLeft[i] = posLeft[i-1];
            }
            // DP for starting index of the right max sum interval
           // caution: the condition is ">= tot" for right interval, and "> tot" for left interval
            posRight[n-k] = n-k;
            for (int i = n-k-1, tot = sum[n]-sum[n-k]; i >= 0; i--) {
                if (sum[i+k]-sum[i] >= tot) {
                    posRight[i] = i;
                    tot = sum[i+k]-sum[i];
                }
                else
                    posRight[i] = posRight[i+1];
            }
            // test all possible middle interval
            for (int i = k; i <= n-2*k; i++) {
                int l = posLeft[i-1], r = posRight[i+k];
                int tot = (sum[i+k]-sum[i]) + (sum[l+k]-sum[l]) + (sum[r+k]-sum[r]);
                if (tot > maxsum) {
                    maxsum = tot;
                    ans[0] = l; ans[1] = i; ans[2] = r;
                }
            }
            return ans;
        }
    }
    


### Solution 2
A greedy solution using three sliding windows where you keep track of the best
indexes/sums as you go.

 **O(n) time** : Since we're only going through the list once and using no
complex operations, this is O(n).  
 **O(1) space** : Just a fixed set of temp vars. We don't need the extra
arrays that the DP solutions have.

    
    
    class Solution:
        def maxSumOfThreeSubarrays(self, nums, k):
            """
            :type nums: List[int]
            :type k: int
            :rtype: List[int]
            """
    
            # Best single, double, and triple sequence found so far
            bestSeq = 0
            bestTwoSeq = [0, k]
            bestThreeSeq = [0, k, k*2]
    
            # Sums of each window
            seqSum = sum(nums[0:k])
            seqTwoSum = sum(nums[k:k*2])
            seqThreeSum = sum(nums[k*2:k*3])
    
            # Sums of combined best windows
            bestSeqSum = seqSum
            bestTwoSum = seqSum + seqTwoSum
            bestThreeSum = seqSum + seqTwoSum + seqThreeSum
    
            # Current window positions
            seqIndex = 1
            twoSeqIndex = k + 1
            threeSeqIndex = k*2 + 1
            while threeSeqIndex <= len(nums) - k:
                # Update the three sliding windows
                seqSum = seqSum - nums[seqIndex - 1] + nums[seqIndex + k - 1]
                seqTwoSum = seqTwoSum - nums[twoSeqIndex - 1] + nums[twoSeqIndex + k - 1]
                seqThreeSum = seqThreeSum - nums[threeSeqIndex - 1] + nums[threeSeqIndex + k - 1]
                
                # Update best single window
                if seqSum > bestSeqSum:
                    bestSeq = seqIndex
                    bestSeqSum = seqSum
    
                # Update best two windows
                if seqTwoSum + bestSeqSum > bestTwoSum:
                    bestTwoSeq = [bestSeq, twoSeqIndex]
                    bestTwoSum = seqTwoSum + bestSeqSum
    
                # Update best three windows
                if seqThreeSum + bestTwoSum > bestThreeSum:
                    bestThreeSeq = bestTwoSeq + [threeSeqIndex]
                    bestThreeSum = seqThreeSum + bestTwoSum
    
                # Update the current positions
                seqIndex += 1
                twoSeqIndex += 1
                threeSeqIndex += 1
    
            return bestThreeSeq
    


### Solution 3
This is a more general DP solution, and it is similar to that buy and sell
stock problem.

dp[i][j] stands for in i th sum, the max non-overlap sum we can have from 0 to
j  
id[i][j] stands for in i th sum, the first starting index for that sum.

    
    
    class Solution {
        public int[] maxSumOfThreeSubarrays(int[] nums, int k) {
            int[][] dp = new int[4][nums.length + 1];
            int sum = 0;
            int[] accu = new int[nums.length + 1];
            for(int i = 0; i < nums.length; i++) {
                sum += nums[i];
                accu[i] = sum;
            }
            int[][] id = new int[4][nums.length + 1];
            int max = 0, inId = 0;
            for(int i = 1; i < 4; i++) {
                for(int j = k-1 ; j < nums.length; j++) {
                    int tmpmax = j - k < 0 ? accu[j] : accu[j] - accu[j-k] + dp[i-1][j-k];
                    if(j - k >= 0) {
                        dp[i][j] = dp[i][j-1];
                        id[i][j] = id[i][j-1];
                    }
                    if(j > 0 && tmpmax > dp[i][j-1]) {
                        dp[i][j] = tmpmax;
                        id[i][j] = j-k+1;
                    }
                }
            }
            int[] res = new int[3];
            res[2] = id[3][nums.length-1];
            res[1] = id[2][res[2] - 1];
            res[0] = id[1][res[1] - 1];        
            return res;
        }
    }
    



