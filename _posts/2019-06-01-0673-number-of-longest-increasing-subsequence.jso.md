---
layout: post
title: 673. Number of Longest Increasing Subsequence
---
### Question
Given an unsorted array of integers, find the number of longest increasing
subsequence.

 **Example 1:**  

    
    
     **Input:** [1,3,5,4,7]
    **Output:** 2
    **Explanation:** The two longest increasing subsequence are [1, 3, 4, 7] and [1, 3, 5, 7].
    

**Example 2:**  

    
    
    **Input:** [2,2,2,2,2]
    **Output:** 5
    **Explanation:** The length of longest continuous increasing subsequence is 1, and there are 5 subsequences' length is 1, so output 5.
    

**Note:** Length of the given array will be not exceed 2000 and the answer is
guaranteed to be fit in 32-bit signed int.

### Solution 1
The idea is to use two arrays `len[n]` and `cnt[n]` to record the maximum
length of Increasing Subsequence and the coresponding number of these sequence
which ends with `nums[i]`, respectively. That is:

`len[i]`: the length of the Longest Increasing Subsequence which ends with
`nums[i]`.  
`cnt[i]`: the number of the Longest Increasing Subsequence which ends with
`nums[i]`.

Then, the result is the sum of each `cnt[i]` while its corresponding `len[i]`
is the maximum length.

Java version:

    
    
    public int findNumberOfLIS(int[] nums) {
            int n = nums.length, res = 0, max_len = 0;
            int[] len =  new int[n], cnt = new int[n];
            for(int i = 0; i<n; i++){
                len[i] = cnt[i] = 1;
                for(int j = 0; j <i ; j++){
                    if(nums[i] > nums[j]){
                        if(len[i] == len[j] + 1)cnt[i] += cnt[j];
                        if(len[i] < len[j] + 1){
                            len[i] = len[j] + 1;
                            cnt[i] = cnt[j];
                        }
                    }
                }
                if(max_len == len[i])res += cnt[i];
                if(max_len < len[i]){
                    max_len = len[i];
                    res = cnt[i];
                }
            }
            return res;
        }
    

C++ version: (use `vector<pair<int, int>> dp` to combine `len[]` and `cnt[]`)

    
    
        int findNumberOfLIS(vector<int>& nums) {
            int n = nums.size(), res = 0, max_len = 0;
            vector<pair<int,int>> dp(n,{1,1});            //dp[i]: {length, number of LIS which ends with nums[i]}
            for(int i = 0; i<n; i++){
                for(int j = 0; j <i ; j++){
                    if(nums[i] > nums[j]){
                        if(dp[i].first == dp[j].first + 1)dp[i].second += dp[j].second;
                        if(dp[i].first < dp[j].first + 1)dp[i] = {dp[j].first + 1, dp[j].second};
                    }
                }
                if(max_len == dp[i].first)res += dp[i].second;
                if(max_len < dp[i].first){
                    max_len = dp[i].first;
                    res = dp[i].second;
                }
            }
            return res;
        }
    


### Solution 2
The solution is based on DP.

    
    
    For a sequence of numbers,
    cnt[k] is total number of longest subsequence ending with nums[k];
    len[k] is the length of longest subsequence ending with nums[k];
    

Then we have following equations

    
    
    len[k+1] = max(len[k+1], len[i]+1) for all i <= k and nums[i] < nums[k+1];
    cnt[k+1] = sum(cnt[i]) for all i <= k and nums[i] < nums[k+1] and len[i] = len[k+1]-1;
    

Starting case and default case: cnt[0] = len[0] = 1;

    
    
    class Solution {
    public:
        int findNumberOfLIS(vector<int>& nums) {
            int n = nums.size(), maxlen = 1, ans = 0;
            vector<int> cnt(n, 1), len(n, 1);
            for (int i = 1; i < n; i++) {
                for (int j = 0; j < i; j++) {
                    if (nums[i] > nums[j]) {
                        if (len[j]+1 > len[i]) {
                            len[i] = len[j]+1;
                            cnt[i] = cnt[j];
                        }
                        else if (len[j]+1 == len[i]) 
                            cnt[i] += cnt[j];
                    }
                }
                maxlen = max(maxlen, len[i]);
            }
            // find the longest increasing subsequence of the whole sequence
           // sum valid counts
            for (int i = 0; i < n; i++) 
                if (len[i] == maxlen) ans += cnt[i];
            return ans;
        }
    };
    
    


### Solution 3
The idea is to modify classic [LIS solution which uses binary
search](https://en.wikipedia.org/wiki/Longest_increasing_subsequence) to find
the "insertion point" of a currently processed value. At `dyn[k]` we **don't
store** a single number representing the smallest value such that there exists
a LIS of length `k+1` as in classic LIS solution. Instead, at `dyn[k]` we
store all such values that were once endings of a `k+1` LIS (so we keep the
history as well).  
These values are held in the first part of the pairs in
`vector<pair<int,int>>` which we get by indexing `dyn` vector. So for example
in a pair `x = {a, b}` the first part -- `a`, indicates that there exists a
LIS of length `k+1` such that it ends with a value `a`. The second part --
`b`, represents the number of possible options for which LIS of length `k+1`
ends with a value equal to or greater than `a`. This is the place where we use
prefix sums.  
If we want to know how many options do we have to end a LIS of length `m` with
value `y`, we just binary search for the index `i` of a pair with first part
strictly less than `y` in `dyn[m-2]`. Then the number of options is
`dyn[m-2].back().second - dyn[m-2][i-1].second` or just `dyn[m-2].back()` if
`i` is `0`.  
That is the basic idea, the running time is O(NlogN), because we just do 2
binary searches for every element of the input. Space complexity is O(N), as
every element of the input will be contained in the `dyn` vector exactly once.  
Feel free to post any corrections or simpler explanations :)

    
    
    class Solution {
    public:
        int findNumberOfLIS(vector<int>& nums) {
            if (nums.empty())
                return 0;
            
            vector<vector<pair<int, int>>> dyn(nums.size() + 1);
            int max_so_far = 0;
            for (int i = 0; i < nums.size(); ++i) {
                // bsearch insertion point
                int l = 0, r = max_so_far;
                while (l < r) {
                    int mid = l + (r - l) / 2;
                    if (dyn[mid].back().first < nums[i]) {
                        l = mid + 1;
                    } else {
                        r = mid;
                    }
                }
                
                // bsearch number of options
                int options = 1;
                int row = l - 1;
                if (row >= 0) {
                    int l1 = 0, r1 = dyn[row].size();
                    while (l1 < r1) {
                        int mid = l1 + (r1 - l1) / 2;
                        if (dyn[row][mid].first < nums[i]) {
                            r1 = mid;
                        } else {
                            l1 = mid + 1;
                        }
                    }
                    
                    options = dyn[row].back().second;
                    options -= (l1 == 0) ? 0 : dyn[row][l1 - 1].second;
                }
                
                
                dyn[l].push_back({nums[i], (dyn[l].empty() ? options : dyn[l].back().second + options)});
                if (l == max_so_far) {
                    max_so_far++;
                }
            }
            
            return dyn[max_so_far-1].back().second;
            
        }
    };
    



