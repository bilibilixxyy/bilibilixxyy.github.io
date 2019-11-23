---
layout: post
title: 39. Combination Sum
---
### Question
Given a **set** of candidate numbers (`candidates`) **(without duplicates)**
and a target number (`target`), find all unique combinations in `candidates`
where the candidate numbers sums to `target`.

The **same** repeated number may be chosen from `candidates` unlimited number
of times.

 **Note:**

  * All numbers (including `target`) will be positive integers.
  * The solution set must not contain duplicate combinations.

 **Example 1:**

    
    
     **Input:** candidates = [2,3,6,7], target = 7,
    **A solution set is:**
    [
      [7],
      [2,2,3]
    ]
    

**Example 2:**

    
    
    **Input:** candidates = [2,3,5], target = 8,
    **A solution set is:**
    [
       [2,2,2,2],
      [2,3,3],
      [3,5]
    ]
    

### Solution 1
This structure might apply to many other backtracking questions, but here I am
just going to demonstrate Subsets, Permutations, and Combination Sum.

Subsets : <https://leetcode.com/problems/subsets/>

    
    
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> list = new ArrayList<>();
        Arrays.sort(nums);
        backtrack(list, new ArrayList<>(), nums, 0);
        return list;
    }
    
    private void backtrack(List<List<Integer>> list , List<Integer> tempList, int [] nums, int start){
        list.add(new ArrayList<>(tempList));
        for(int i = start; i < nums.length; i++){
            tempList.add(nums[i]);
            backtrack(list, tempList, nums, i + 1);
            tempList.remove(tempList.size() - 1);
        }
    }
    

Subsets II (contains duplicates) : <https://leetcode.com/problems/subsets-ii/>

    
    
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> list = new ArrayList<>();
        Arrays.sort(nums);
        backtrack(list, new ArrayList<>(), nums, 0);
        return list;
    }
    
    private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, int start){
        list.add(new ArrayList<>(tempList));
        for(int i = start; i < nums.length; i++){
            if(i > start && nums[i] == nums[i-1]) continue; // skip duplicates
            tempList.add(nums[i]);
            backtrack(list, tempList, nums, i + 1);
            tempList.remove(tempList.size() - 1);
        }
    } 
    

* * *

Permutations : <https://leetcode.com/problems/permutations/>

    
    
    public List<List<Integer>> permute(int[] nums) {
       List<List<Integer>> list = new ArrayList<>();
       // Arrays.sort(nums); // not necessary
       backtrack(list, new ArrayList<>(), nums);
       return list;
    }
    
    private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums){
       if(tempList.size() == nums.length){
          list.add(new ArrayList<>(tempList));
       } else{
          for(int i = 0; i < nums.length; i++){ 
             if(tempList.contains(nums[i])) continue; // element already exists, skip
             tempList.add(nums[i]);
             backtrack(list, tempList, nums);
             tempList.remove(tempList.size() - 1);
          }
       }
    } 
    

Permutations II (contains duplicates) :
<https://leetcode.com/problems/permutations-ii/>

    
    
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> list = new ArrayList<>();
        Arrays.sort(nums);
        backtrack(list, new ArrayList<>(), nums, new boolean[nums.length]);
        return list;
    }
    
    private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, boolean [] used){
        if(tempList.size() == nums.length){
            list.add(new ArrayList<>(tempList));
        } else{
            for(int i = 0; i < nums.length; i++){
                if(used[i] || i > 0 && nums[i] == nums[i-1] && !used[i - 1]) continue;
                used[i] = true; 
                tempList.add(nums[i]);
                backtrack(list, tempList, nums, used);
                used[i] = false; 
                tempList.remove(tempList.size() - 1);
            }
        }
    }
    

* * *

Combination Sum : <https://leetcode.com/problems/combination-sum/>

    
    
    public List<List<Integer>> combinationSum(int[] nums, int target) {
        List<List<Integer>> list = new ArrayList<>();
        Arrays.sort(nums);
        backtrack(list, new ArrayList<>(), nums, target, 0);
        return list;
    }
    
    private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, int remain, int start){
        if(remain < 0) return;
        else if(remain == 0) list.add(new ArrayList<>(tempList));
        else{ 
            for(int i = start; i < nums.length; i++){
                tempList.add(nums[i]);
                backtrack(list, tempList, nums, remain - nums[i], i); // not i + 1 because we can reuse same elements
                tempList.remove(tempList.size() - 1);
            }
        }
    }
    

Combination Sum II (can't reuse same element) :
<https://leetcode.com/problems/combination-sum-ii/>

    
    
    public List<List<Integer>> combinationSum2(int[] nums, int target) {
        List<List<Integer>> list = new ArrayList<>();
        Arrays.sort(nums);
        backtrack(list, new ArrayList<>(), nums, target, 0);
        return list;
        
    }
    
    private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, int remain, int start){
        if(remain < 0) return;
        else if(remain == 0) list.add(new ArrayList<>(tempList));
        else{
            for(int i = start; i < nums.length; i++){
                if(i > start && nums[i] == nums[i-1]) continue; // skip duplicates
                tempList.add(nums[i]);
                backtrack(list, tempList, nums, remain - nums[i], i + 1);
                tempList.remove(tempList.size() - 1); 
            }
        }
    } 
    

Palindrome Partitioning : <https://leetcode.com/problems/palindrome-
partitioning/>

    
    
    public List<List<String>> partition(String s) {
       List<List<String>> list = new ArrayList<>();
       backtrack(list, new ArrayList<>(), s, 0);
       return list;
    }
    
    public void backtrack(List<List<String>> list, List<String> tempList, String s, int start){
       if(start == s.length())
          list.add(new ArrayList<>(tempList));
       else{
          for(int i = start; i < s.length(); i++){
             if(isPalindrome(s, start, i)){
                tempList.add(s.substring(start, i + 1));
                backtrack(list, tempList, s, i + 1);
                tempList.remove(tempList.size() - 1);
             }
          }
       }
    }
    
    public boolean isPalindrome(String s, int low, int high){
       while(low < high)
          if(s.charAt(low++) != s.charAt(high--)) return false;
       return true;
    } 
    


### Solution 2
Accepted 16ms c++ solution use backtracking for [Combination
Sum](https://leetcode.com/problems/combination-sum/):

    
    
    class Solution {
    public:
        std::vector<std::vector<int> > combinationSum(std::vector<int> &candidates, int target) {
            std::sort(candidates.begin(), candidates.end());
            std::vector<std::vector<int> > res;
            std::vector<int> combination;
            combinationSum(candidates, target, res, combination, 0);
            return res;
        }
    private:
        void combinationSum(std::vector<int> &candidates, int target, std::vector<std::vector<int> > &res, std::vector<int> &combination, int begin) {
            if (!target) {
                res.push_back(combination);
                return;
            }
            for (int i = begin; i != candidates.size() && target >= candidates[i]; ++i) {
                combination.push_back(candidates[i]);
                combinationSum(candidates, target - candidates[i], res, combination, i);
                combination.pop_back();
            }
        }
    };
    

Accepted 12ms c++ solution use backtracking for [Combination Sum
II](https://leetcode.com/problems/combination-sum-ii/):

    
    
    class Solution {
    public:
        std::vector<std::vector<int> > combinationSum2(std::vector<int> &candidates, int target) {
            std::sort(candidates.begin(), candidates.end());
            std::vector<std::vector<int> > res;
            std::vector<int> combination;
            combinationSum2(candidates, target, res, combination, 0);
            return res;
        }
    private:
        void combinationSum2(std::vector<int> &candidates, int target, std::vector<std::vector<int> > &res, std::vector<int> &combination, int begin) {
            if (!target) {
                res.push_back(combination);
                return;
            }
            for (int i = begin; i != candidates.size() && target >= candidates[i]; ++i)
                if (i == begin || candidates[i] != candidates[i - 1]) {
                    combination.push_back(candidates[i]);
                    combinationSum2(candidates, target - candidates[i], res, combination, i + 1);
                    combination.pop_back();
                }
        }
    };
    

Accepted 0ms c++ solution use backtracking for [Combination Sum
III](https://leetcode.com/problems/combination-sum-iii/):

    
    
    class Solution {
    public:
        std::vector<std::vector<int> > combinationSum3(int k, int n) {
            std::vector<std::vector<int> > res;
            std::vector<int> combination;
            combinationSum3(n, res, combination, 1, k);
            return res;
        }
    private:
        void combinationSum3(int target, std::vector<std::vector<int> > &res, std::vector<int> &combination, int begin, int need) {
            if (!target) {
                res.push_back(combination);
                return;
            }
            else if (!need)
                return;
            for (int i = begin; i != 10 && target >= i * need + need * (need - 1) / 2; ++i) {
                combination.push_back(i);
                combinationSum3(target - i, res, combination, i + 1, need - 1);
                combination.pop_back();
            }
        }
    };
    


### Solution 3
    
    
    def combinationSum(self, candidates, target):
        res = []
        candidates.sort()
        self.dfs(candidates, target, 0, [], res)
        return res
        
    def dfs(self, nums, target, index, path, res):
        if target < 0:
            return  # backtracking
        if target == 0:
            res.append(path)
            return 
        for i in xrange(index, len(nums)):
            self.dfs(nums, target-nums[i], i, path+[nums[i]], res)



