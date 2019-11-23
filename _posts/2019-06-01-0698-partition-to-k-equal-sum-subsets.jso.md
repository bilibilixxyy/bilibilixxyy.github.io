---
layout: post
title: 698. Partition to K Equal Sum Subsets
---
### Question
Given an array of integers `nums` and a positive integer `k`, find whether
it's possible to divide this array into `k` non-empty subsets whose sums are
all equal.



 **Example 1:**

    
    
     **Input:** nums = [4, 3, 2, 3, 5, 2, 1], k = 4
    **Output:** True
    **Explanation:** It's possible to divide it into 4 subsets (5), (1, 4), (2,3), (2,3) with equal sums.
    



 **Note:**

  * `1 <= k <= len(nums) <= 16`.
  * `0 < nums[i] < 10000`.

### Solution 1
 **Update:** This question has been changed after the contest. It added the
special restriction `0 < nums[i] < 10000`. My solution here is without that
consideration.

Assume `sum` is the sum of `nums[]` . The dfs process is to find a subset of
`nums[]` which sum equals to `sum/k`. We use an array `visited[]` to record
which element in `nums[]` is used. Each time when we get a `cur_sum = sum/k`,
we will start from position `0` in `nums[]` to look up the elements that are
not used yet and find another `cur_sum = sum/k`.

An corner case is when `sum = 0`, my method is to use `cur_num` to record the
number of elements in the current subset. Only if `cur_sum = sum/k && cur_num
>0`, we can start another look up process.

Some test cases may need to be added in:  
`nums = {-1,1,0,0}, k = 4`  
`nums = {-1,1}, k = 1`  
`nums = {-1,1}, k = 2`  
`nums = {-1,1,0}, k = 2`  
...

Java version:

    
    
        public boolean canPartitionKSubsets(int[] nums, int k) {
            int sum = 0;
            for(int num:nums)sum += num;
            if(k <= 0 || sum%k != 0)return false;
            int[] visited = new int[nums.length];
            return canPartition(nums, visited, 0, k, 0, 0, sum/k);
        }
        
        public boolean canPartition(int[] nums, int[] visited, int start_index, int k, int cur_sum, int cur_num, int target){
            if(k==1)return true;
            if(cur_sum == target && cur_num>0)return canPartition(nums, visited, 0, k-1, 0, 0, target);
            for(int i = start_index; i<nums.length; i++){
                if(visited[i] == 0){
                    visited[i] = 1;
                    if(canPartition(nums, visited, i+1, k, cur_sum + nums[i], cur_num++, target))return true;
                    visited[i] = 0;
                }
            }
            return false;
        }
    

C++ version:

    
    
        bool canPartitionKSubsets(vector<int>& nums, int k) {
            int sum = 0;
            for(int num:nums)sum+=num;
            if(k <= 0 || sum%k != 0)return false;
            vector<int> visited(nums.size(), 0);
            return canPartition(nums, visited, 0, k, 0, 0, sum/k);
        }
        
        bool canPartition(vector<int>& nums, vector<int>& visited, int start_index, int k, int cur_sum, int cur_num, int target){
            if(k==1)return true;
            if(cur_sum == target && cur_num >0 )return canPartition(nums, visited, 0, k-1, 0, 0, target);
            for(int i = start_index; i<nums.size(); i++){
                if(!visited[i]){
                    visited[i] = 1;
                    if(canPartition(nums, visited, i+1, k, cur_sum + nums[i], cur_num++, target))return true;
                    visited[i] = 0;
                }
            }
            return false;
        }
    


### Solution 2
> **canPartitionKSubsets()** is true when there are exactly k subsets with sum
`sum / k` (each element can only be used once).

  * comparison between `curSum` and `targetSum` indicates whether we find a valid subset.
  * `k` restricts there are k subsets exactly.

> We can take it as a two-layer recursion.

    
    
    Outer recursion on k subsets:
    Base case: k == 0
    Recursive case: // Inner recursion on 1 subset
    				Base case: curSubsetSum > targetSubsetSum
    						   curSubsetSum == targetSubsetSum (return to outer recursion)
    				Recursive case: curSubsetSum < targetSubsetSum
    

> Since the order of numbers within a subset doesn't matter, we add
`innerStart` for the inner recursion to avoid duplicate calculations.

* * *
    
    
        public boolean canPartitionKSubsets(int[] nums, int k) {
            int sum = 0, maxNum = 0;
            for (int i = 0; i < nums.length; i++) {
                sum += nums[i];
                maxNum = Math.max(maxNum, nums[i]);
            }
            if (sum % k != 0 || maxNum > sum / k) return false;
            return canKSubsetsSum(nums, k, sum / k, 0, new boolean[nums.length], 0);
        }
        
        private boolean canKSubsetsSum(int[] nums, int k, int targetSum, int curSum, boolean[] visited, int innerStart) {
            if (k == 0) return true; // Outer base case for number of subsets.
            else if (curSum > targetSum) return false; // Inner base case for current subset sum.
            else if (curSum == targetSum) return canKSubsetsSum(nums, k - 1, targetSum, 0, visited, 0); // Inner base case for current subset sum.
            
            for (int i = innerStart; i < nums.length; i++) {
                if (!visited[i]) {
                    visited[i] = true;
                    if (canKSubsetsSum(nums, k, targetSum, curSum + nums[i], visited, i + 1)) return true;
                    visited[i] = false;
                }
            }
            
            return false;
        }
    

**(人 •͈ᴗ•͈)** Thanks for voting!


### Solution 3
Very classical question.  
Ref: <http://www.geeksforgeeks.org/partition-set-k-subsets-equal-sum/>

**C++**

    
    
         bool canPartitionKSubsets(vector<int>& nums, int K) {
            int N = nums.size();
            if (K == 1) return true;
            if (N < K) return false;
            int sum = 0;
            for (int i = 0; i < N; i++) sum += nums[i];
            if (sum % K != 0) return false;
    
            int subset = sum / K;
            int subsetSum[K];
            bool taken[N];
    
            for (int i = 0; i < K; i++) subsetSum[i] = 0;
            for (int i = 0; i < N; i++) taken[i] = false;
    
            subsetSum[0] = nums[N - 1];
            taken[N - 1] = true;
    
            return canPartitionKSubsets(nums, subsetSum, taken, subset, K, N, 0, N - 1);
        }
    
        bool canPartitionKSubsets(vector<int>& nums, int subsetSum[], bool taken[], int subset, int K, int N, int curIdx, int limitIdx) {
            if (subsetSum[curIdx] == subset) {
                if (curIdx == K - 2) return true;
                return canPartitionKSubsets(nums, subsetSum, taken, subset, K, N, curIdx + 1, N - 1);
            }
    
            for (int i = limitIdx; i >= 0; i--) {
                if (taken[i]) continue;
                int tmp = subsetSum[curIdx] + nums[i];
    
                if (tmp <= subset) {
                    taken[i] = true;
                    subsetSum[curIdx] += nums[i];
                    bool nxt = canPartitionKSubsets(nums, subsetSum, taken, subset, K, N, curIdx, i - 1);
                    taken[i] = false;
                    subsetSum[curIdx] -= nums[i];
                    if (nxt) return true;
                }
            }
            return false;
        }
    

**Java, 100%**

    
    
         public boolean canPartitionKSubsets(int[] A, int k) {
            if (k > A.length) return false;
            int sum = 0;
            for (int num : A) sum += num;
            if (sum % k != 0) return false;
            boolean[] visited = new boolean[A.length];
            Arrays.sort(A);
            return dfs(A, 0, A.length - 1, visited, sum / k, k);
        }
    
        public boolean dfs(int[] A, int sum, int st, boolean[] visited, int target, int round) {
            if (round == 0) return true;
            if (sum == target && dfs(A, 0, A.length - 1, visited, target, round - 1))
                return true;
            for (int i = st; i >= 0; --i) {
                if (!visited[i] && sum + A[i] <= target) {
                    visited[i] = true;
                    if (dfs(A, sum + A[i], i - 1, visited, target, round))
                        return true;
                    visited[i] = false;
                }
            }
            return false;
        }
    

**Python, another idea**

    
    
         def canPartitionKSubsets(self, A, k):
            if len(A) < k:
                return False
            ASum = sum(A)
            A.sort(reverse=True)
            if ASum % k != 0:
                return False
            target = [ASum / k] * k
    
            def dfs(pos):
                if pos == len(A): return True
                for i in xrange(k):
                    if target[i] >= A[pos]:
                        target[i] -= A[pos]
                        if dfs(pos + 1):
                            return True
                        target[i] += A[pos]
                return False
            return dfs(0)
    



