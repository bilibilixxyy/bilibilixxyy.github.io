---
layout: post
title: 40. Combination Sum II
---
### Question
Given a collection of candidate numbers (`candidates`) and a target number
(`target`), find all unique combinations in `candidates` where the candidate
numbers sums to `target`.

Each number in `candidates` may only be used **once** in the combination.

 **Note:**

  * All numbers (including `target`) will be positive integers.
  * The solution set must not contain duplicate combinations.

 **Example 1:**

    
    
     **Input:** candidates =  [10,1,2,7,6,1,5], target = 8,
    **A solution set is:**
    [
      [1, 7],
      [1, 2, 5],
      [2, 6],
      [1, 1, 6]
    ]
    

**Example 2:**

    
    
    **Input:** candidates =  [2,5,2,1,2], target = 5,
    **A solution set is:**
    [
       [1,2,2],
      [5]
    ]
    

### Solution 1
    
    
     public List<List<Integer>> combinationSum2(int[] cand, int target) {
        Arrays.sort(cand);
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        List<Integer> path = new ArrayList<Integer>();
        dfs_com(cand, 0, target, path, res);
        return res;
    }
    void dfs_com(int[] cand, int cur, int target, List<Integer> path, List<List<Integer>> res) {
        if (target == 0) {
            res.add(new ArrayList(path));
            return ;
        }
        if (target < 0) return;
        for (int i = cur; i < cand.length; i++){
            if (i > cur && cand[i] == cand[i-1]) continue;
            path.add(path.size(), cand[i]);
            dfs_com(cand, i+1, target - cand[i], path, res);
            path.remove(path.size()-1);
        }
    }


### Solution 2
Combination Sum I

    
    
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> list = new ArrayList<>();
        Arrays.sort(candidates);
        backtrack(list, new ArrayList<Integer>(), candidates, target, 0);
        return list;
    }
    
    private void backtrack(List<List<Integer>> list, List<Integer> tempList, int[] cand, int remain, int start) {
        if (remain < 0) return; /** no solution */
        else if (remain == 0) list.add(new ArrayList<>(tempList));
        else{
            for (int i = start; i < cand.length; i++) { 
                tempList.add(cand[i]);
                backtrack(list, tempList, cand, remain-cand[i], i);
                tempList.remove(tempList.size()-1);
            } 
        }
    
    }
    

Combination Sum II

    
    
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
       List<List<Integer>> list = new LinkedList<List<Integer>>();
       Arrays.sort(candidates);
       backtrack(list, new ArrayList<Integer>(), candidates, target, 0);
       return list;
    }
    
    private void backtrack(List<List<Integer>> list, List<Integer> tempList, int[] cand, int remain, int start) {
       
       if(remain < 0) return; /** no solution */
       else if(remain == 0) list.add(new ArrayList<>(tempList));
       else{
          for (int i = start; i < cand.length; i++) {
             if(i > start && cand[i] == cand[i-1]) continue; /** skip duplicates */
             tempList.add(cand[i]);
             backtrack(list, tempList, cand, remain - cand[i], i+1);
             tempList.remove(tempList.size() - 1);
          }
       }
    }
    

Combination Sum III

    
    
    public List<List<Integer>> combinationSum3(int k, int n) {
        List<List<Integer>> list = new ArrayList<>();
        backtrack(list, new ArrayList<Integer>(), k, n, 1);
        return list;
    }
    
    private void backtrack(List<List<Integer>> list, List<Integer> tempList, int k, int remain, int start) {
        if(tempList.size() > k) return; /** no solution */
        else if(tempList.size() == k && remain == 0) list.add(new ArrayList<>(tempList));
        else{
            for (int i = start; i <= 9; i++) {
                tempList.add(i);
                backtrack(list, tempList, k, remain-i, i+1);
                tempList.remove(tempList.size() - 1);
            }
        }
    }


### Solution 3
    
    
    def combinationSum2(self, candidates, target):
        # Sorting is really helpful, se we can avoid over counting easily
        candidates.sort()                      
        result = []
        self.combine_sum_2(candidates, 0, [], result, target)
        return result
        
    def combine_sum_2(self, nums, start, path, result, target):
        # Base case: if the sum of the path satisfies the target, we will consider 
        # it as a solution, and stop there
        if not target:
            result.append(path)
            return
        
        for i in xrange(start, len(nums)):
            # Very important here! We don't use `i > 0` because we always want 
            # to count the first element in this recursive step even if it is the same 
            # as one before. To avoid overcounting, we just ignore the duplicates
            # after the first element.
            if i > start and nums[i] == nums[i - 1]:
                continue
    
            # If the current element is bigger than the assigned target, there is 
            # no need to keep searching, since all the numbers are positive
            if nums[i] > target:
                break
    
            # We change the start to `i + 1` because one element only could
            # be used once
            self.combine_sum_2(nums, i + 1, path + [nums[i]], 
                               result, target - nums[i])



