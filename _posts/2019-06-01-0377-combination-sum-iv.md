---
layout: post
title: 377. Combination Sum IV
---
### Question
Given an integer array with all positive numbers and no duplicates, find the
number of possible combinations that add up to a positive integer target.

 **Example:**

    
    
     _ **nums**_ = [1, 2, 3]
    _**target**_ = 4
    
    The possible combination ways are:
    (1, 1, 1, 1)
    (1, 1, 2)
    (1, 2, 1)
    (1, 3)
    (2, 1, 1)
    (2, 2)
    (3, 1)
    
    Note that different sequences are counted as different combinations.
    
    Therefore the output is _**7**_.
    



 **Follow up:**  
What if negative numbers are allowed in the given array?  
How does it change the problem?  
What limitation we need to add to the question to allow negative numbers?

 **Credits:**  
Special thanks to [@pbrother](https://leetcode.com/pbrother/) for adding this
problem and creating all test cases.

### Solution 1
Think about the recurrence relation first. How does the # of combinations of
the `target` related to the # of combinations of numbers that are smaller than
the `target`?

So we know that `target` is the sum of numbers in the array. Imagine we only
need one more number to reach target, this number can be any one in the array,
right? So the # of combinations of `target`, `comb[target] = sum(comb[target -
nums[i]]), where 0 <= i < nums.length, and target >= nums[i]`.

In the example given, we can actually find the # of combinations of 4 with the
# of combinations of 3(4 - 1), 2(4- 2) and 1(4 - 3). As a result, `comb[4] =
comb[4-1] + comb[4-2] + comb[4-3] = comb[3] + comb[2] + comb[1]`.

Then think about the base case. Since if the target is 0, there is only one
way to get zero, which is using 0, we can set `comb[0] = 1`.

EDIT: The problem says that target is a positive integer that makes me feel
it's unclear to put it in the above way. Since `target == 0` only happens when
in the previous call, target = nums[i], we know that this is the only
combination in this case, so we return 1.

Now we can come up with at least a recursive solution.

    
    
    public int combinationSum4(int[] nums, int target) {
        if (target == 0) {
            return 1;
        }
        int res = 0;
        for (int i = 0; i < nums.length; i++) {
            if (target >= nums[i]) {
                res += combinationSum4(nums, target - nums[i]);
            }
        }
        return res;
    }
    

Now for a DP solution, we just need to figure out a way to store the
intermediate results, to avoid the same combination sum being calculated many
times. We can use an array to save those results, and check if there is
already a result before calculation. We can fill the array with -1 to indicate
that the result hasn't been calculated yet. 0 is not a good choice because it
means there is no combination sum for the target.

    
    
    private int[] dp;
    
    public int combinationSum4(int[] nums, int target) {
        dp = new int[target + 1];
        Arrays.fill(dp, -1);
        dp[0] = 1;
        return helper(nums, target);
    }
    
    private int helper(int[] nums, int target) {
        if (dp[target] != -1) {
            return dp[target];
        }
        int res = 0;
        for (int i = 0; i < nums.length; i++) {
            if (target >= nums[i]) {
                res += helper(nums, target - nums[i]);
            }
        }
        dp[target] = res;
        return res;
    }
    

EDIT: The above solution is top-down. How about a bottom-up one?

    
    
    public int combinationSum4(int[] nums, int target) {
        int[] comb = new int[target + 1];
        comb[0] = 1;
        for (int i = 1; i < comb.length; i++) {
            for (int j = 0; j < nums.length; j++) {
                if (i - nums[j] >= 0) {
                    comb[i] += comb[i - nums[j]];
                }
            }
        }
        return comb[target];
    }
    


### Solution 2
[1,1,2] and [2, 1, 1] are two different combinations, never heard this
before...  
Mr. Permutation is unhappy and angry~


### Solution 3
 **Firstly, the Combination Sum 1 & 4 are similar, and Combination Sum 2 & 3
are similar !!! Combination Sum 3 is the special case of Combination Sum 2,
and the Combination Sum 4 return the count while Combination Sum return all
the combination !**  
 **k sum problem is a good extension for the combination sum problem, k-sum 1
is to return the count while k-sum 2 return all the possible results**

  *  ** **DP: when solve the problem return the count****

  *  **DFS : for return all the possible result**

 **Update@08/05/2016 :**

There are 2 more interesting problem, let us check it now !

[ **k Sum 1 & 2**](http://www.lintcode.com/en/problem/k-sum/)

  1. return the count Given n distinct positive integers, integer k (k <= n) and a number target. Find k numbers where sum is target. Calculate how many solutions there are?
  2. return alll

 **Solution to ksum-1 :**

    
    
     class Solution {
    public:
        /**
         * @param A: an integer array.
         * @param k: a positive integer (k <= length(A))
         * @param target: a integer
         * @return an integer
         */
        int kSum(vector<int> A, int k, int target) {
            // wirte your code here
            const int n = A.size();
            /** dp[i][j][target] : # of ways to start from vector[0..i-1], choose j elements to sum to target **/
            vector<vector<vector<int>>> dp(n + 1, vector<vector<int>>(k + 1, vector<int>(target + 1, 0)));
            
            for(int i = 1; i <= n; i++) {
                if(A[i-1] <= target) {
                    for(int j = i; j <= n; j++) {
                        dp[j][1][A[i-1]] =1;
                    }
                }
            }
            /** for position i, we can choose it or not **/
            for(int i = 1; i <= n; i++) {
                for(int j = min(i, k); j > 1; j--) {
                    for(int p = 1; p <= target; p++) {
                        dp[i][j][p] = dp[i - 1][j][p];
                        if(p - A[i - 1] >= 0) {
                            dp[i][j][p] += dp[i - 1][j - 1][p - A[i - 1]];
                        }
                    }
                }
            }
            return dp[n][k][target];
        }
    };
    

**Update @ 2016/09/07**  
There is a more easy to grasp solution :

    
    
     class Solution {
    public:
        /**
         * @param A: an integer array.
         * @param k: a positive integer (k <= length(A))
         * @param target: a integer
         * @return an integer
         */
    int kSum(vector<int> A, int k, int target) {
            // wirte your code here
            const int n = A.size();
            /** dp[i][j][target] : # of ways to start from vector[0..i-1], choose j elements to sum to target **/
            vector<vector<vector<int>>> dp(n + 1, vector<vector<int>>(k + 1, vector<int>(target + 1, 0)));
            
            for(int i = 0; i < A.size(); i++) {
                dp[i][0][0] = 1;
            }
            /** for position i, we can choose it or not **/
            for(int i = 1; i <= n; i++) {
                for(int j =1; j <= k; j++) {
                    for(int p = 1; p <= target; p++) {
                        if (j > i) dp[i][j][p] = 0; 
                        else dp[i][j][p] = dp[i-1][j][p];
                        if(p - A[i - 1] >= 0) {
                            dp[i][j][p] += dp[i - 1][j - 1][p - A[i - 1]];
                        }
                    }
                }
            }
            return dp[n][k][target];
        }
    };
    

**Solution to ksum-2 :**

    
    
     class Solution {
    public:
        /**
         * @param A: an integer array.
         * @param k: a positive integer (k <= length(A))
         * @param target: a integer
         * @return a list of lists of integer
         */
        vector<vector<int>> kSumII(vector<int> A, int k, int target) {
            vector<vector<int>> ans;
            vector<int> curr;
            helper(A, k, 0, target, curr, ans);
            return ans;
        }
    
        void helper(vector<int> A, int k, int start, int target, vector<int>& curr, vector<vector<int>> & ans) {
            if (k < 0 || target < 0) {
                return;
            }
    
            if (k == 0 && target == 0) {
                ans.emplace_back(curr);
                return;
            }
    
            for (int i = start; i <= A.size() - k; i++) {
                curr.emplace_back(A[i]);
                helper(A, k - 1, i + 1, target - A[i], curr, ans);
                curr.pop_back();
            }
        }
    };
    

_**[Problem 39 Given a set of candidate numbers (C) and a target number (T),
find all unique combinations in C where the candidate numbers sums to T. The
same repeated number may be chosen from C unlimited number of
times.](https://leetcode.com/problems/combination-sum/)**_

    
    
     class Solution {
    public:
        vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
            sort(candidates.begin(), candidates.end());
            vector<vector<int>> result;
            vector<int> combination;
            dfs(candidates, target, result, combination, 0);
            return result;
        }
        
        void dfs(vector<int>& nums, int target, vector<vector<int>>& result, vector<int>& combination, int begin) {
            if (!target) {
                result.push_back(combination);
                return;
            }
            for (int i = begin; i < nums.size() && target >= nums[i]; i++) {
                combination.push_back(nums[i]);
                dfs(nums, target - nums[i], result, combination, i);
                combination.pop_back();
            }
        }
    };
    

_**[Problem 40 Given a collection of candidate numbers (C) and a target number
(T), find all unique combinations in C where the candidate numbers sums to T.
Each number in C may only be used once in the
combination.](https://leetcode.com/problems/combination-sum-ii/)**_

    
    
     class Solution {
    public:
        vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
            sort(candidates.begin(), candidates.end());
            vector<vector<int>> result;
            vector<int> combination;
            dfs(candidates, target, result, combination, 0);
            return result;
        }
        
        void dfs(vector<int>& nums, int target, vector<vector<int>>& result, vector<int>& combination, int begin) {
            if (!target) {
                result.push_back(combination);
                return;
            }
            for (int i = begin; i < nums.size() && target >= nums[i]; i++) {
                combination.push_back(nums[i]);
                //combinationSum1 : dfs(nums, target - nums[i], result, combination, i);
                dfs(nums, target - nums[i], result, combination, i + 1);
                combination.pop_back();
                //combinationSum1 : no this line to filter the duplicate cases 
                while (i < nums.size() && nums[i] == nums[i+1]) i++;
            }
        }
    };
    

_**[Problem 216 Find all possible combinations of k numbers that add up to a
number n, given that only numbers from 1 to 9 can be used and each combination
should be a unique set of numbers.](https://leetcode.com/problems/combination-
sum-iii/)**_

This problem is different from the 1 & 2, our choice is constrained to be
[1,9], and our target is valid, then our bigest number is just sum from 1 to
9, our result is that number 1 to 9 can only be choosed for one time. All in
all, this problem is a special case of the Combination Problem 2

    
    
    class Solution {
    public:
        vector<vector<int>> combinationSum3(int k, int n) {
            vector<vector<int>> result;
            vector<int> path;
            dfs(1, path, result, k, n);
            return result;
        }
        
        void dfs(int pos, vector<int>& path, vector<vector<int>>& result, int k, int n) {
            //cut edge
            if (n < 0) return;
            //valid cases
            if (n == 0 && k == path.size()) result.push_back(path);
            for (int i = pos; i <= 9; i++) {
                path.push_back(i);
                dfs(i + 1, path, result, k, n - i);
                path.pop_back();
            }
        }
    };
    

_**[Problem 377 Given an integer array with all positive numbers and no
duplicates, find the number of possible combinations that add up to a positive
integer target.](https://leetcode.com/problems/combination-sum-iv/)**_

This problem is just similar to the Combination Problem 1 , we only need to
return the count but not all the possible result .

  * dp[i] : record the possible combination count to sum to target value of i

    
    
    class Solution {
    public:
        int combinationSum4(vector<int>& nums, int target) {
            vector<int> dp(target + 1);
            dp[0] = 1;
            sort (nums.begin(), nums.end());
            for (int i = 1; i <= target; i++) {
                for (auto num : nums) {
                    if (i < num) break;
                    dp[i] += dp[i - num];
                }
            }
            return dp.back();
        }
    };
    

[_**Problem 77 Given two integers n and k, return all possible combinations of
k numbers out of 1 ... n.**_](https://leetcode.com/problems/combinations/)

This problem is the almost same as the problem Combination sum 2, only with
different ending conditions !

    
    
    class Solution {
    public:
        vector<vector<int> > combine(int n, int k) {
            vector<vector<int> > res;
            vector<int> path;
            dfs(1, path, res, n, k);
            return res;
        }
        void dfs(int pos, vector<int> &path, vector<vector<int> > &res, int n, int k) {
            if (path.size() == k) res.push_back(path);
            else {
                for (int i = pos; i <= n; ++i) {
                    path.push_back(i);
                    dfs(i + 1, path, res, n, k);
                    path.pop_back();
                }
            }
        }
    };
    

_**[Problem 17 Letter Combination Given a digit string, return all possible
letter combinations that the number could represent. A mapping of digit to
letters (just like on the telephone buttons) is given
below.](https://leetcode.com/problems/letter-combinations-of-a-phone-
number/)**_

    
    
     class Solution {
    public:
        vector<string> letterCombinations(string digits) {
            vector<string> res;
            if (digits.empty()) return res;
            vector<string> dict{"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
            dfs(digits, dict, 0, "", res);
            return res;
        }
        void dfs(string digits, vector<string>& dict, int pos, string path, vector<string> &res) {
            if (pos == digits.size()) res.push_back(path);
            else {
                string str = dict[digits[pos] - '0'];
                for (int i = 0; i < str.size(); ++i) {
                    path.push_back(str[i]);
                    dfs(digits, dict, pos + 1, path, res);
                    path.pop_back();
                }
            }
        }
    };
    

_**Problem 254 Write a function that takes an integer n and return all
possible combinations of its factors. Note: Each combination's factors must be
sorted ascending, for example: The factors of 2 and 6 is [2, 6], not [6, 2].
You may assume that n is always positive.**_

    
    
     class Solution {
    public:
        vector<vector<int>> getFactors(int n) {
            vector<vector<int>> result;
            vector<int> path;
            helper(n, 2, path, result);
            return result;
        }
        void helper(int remain, int start, vector<int> path, vector<vector<int>> &result) {
            if (remain == 1) {
                if (path.size() > 1) result.push_back(path);
            } else {
                for (int i = start; i <= remain; ++i) {
                    if (remain % i == 0) {
                        path.push_back(i);
                        helper(remain / i, i, path, result);
                        path.pop_back();
                    }
                }
            }
        }
    };
    



