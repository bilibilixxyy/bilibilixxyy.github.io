---
layout: post
title: 47. Permutations II
---
### Question
Given a collection of numbers that might contain duplicates, return all
possible unique permutations.

 **Example:**

    
    
     **Input:** [1,1,2]
    **Output:**
    [
      [1,1,2],
      [1,2,1],
      [2,1,1]
    ]
    

### Solution 1
Use an extra boolean array " boolean[] used" to indicate whether the value is
added to list.

Sort the array "int[] nums" to make sure we can skip the same value.

when a number has the same value with its previous, we can use this number
only if his previous is used

    
    
    public class Solution {
        public List<List<Integer>> permuteUnique(int[] nums) {
            List<List<Integer>> res = new ArrayList<List<Integer>>();
            if(nums==null || nums.length==0) return res;
            boolean[] used = new boolean[nums.length];
            List<Integer> list = new ArrayList<Integer>();
            Arrays.sort(nums);
            dfs(nums, used, list, res);
            return res;
        }
    
        public void dfs(int[] nums, boolean[] used, List<Integer> list, List<List<Integer>> res){
            if(list.size()==nums.length){
                res.add(new ArrayList<Integer>(list));
                return;
            }
            for(int i=0;i<nums.length;i++){
                if(used[i]) continue;
                if(i>0 &&nums[i-1]==nums[i] && !used[i-1]) continue;
                used[i]=true;
                list.add(nums[i]);
                dfs(nums,used,list,res);
                used[i]=false;
                list.remove(list.size()-1);
            }
        }
    }


### Solution 2
    
    
    class Solution {
    public:
        void recursion(vector<int> num, int i, int j, vector<vector<int> > &res) {
            if (i == j-1) {
                res.push_back(num);
                return;
            }
            for (int k = i; k < j; k++) {
                if (i != k && num[i] == num[k]) continue;
                swap(num[i], num[k]);
                recursion(num, i+1, j, res);
            }
        }
        vector<vector<int> > permuteUnique(vector<int> &num) {
            sort(num.begin(), num.end());
            vector<vector<int> >res;
            recursion(num, 0, num.size(), res);
            return res;
        }
    };


### Solution 3
Very similar to Permutation I, see explanations in
<https://leetcode.com/discuss/19510/my-ac-simple-iterative-java-python-
solution>. To handle duplication, just avoid inserting a number after any of
its duplicates.

    
    
    def permuteUnique(self, nums):
        ans = [[]]
        for n in nums:
            new_ans = []
            for l in ans:
                for i in xrange(len(l)+1):
                    new_ans.append(l[:i]+[n]+l[i:])
                    if i<len(l) and l[i]==n: break              #handles duplication
            ans = new_ans
        return ans
    

Below is a backtracking solution:

    
    
        from collections import Counter
        def permuteUnique(self, nums):
            def btrack(path, counter):
                if len(path)==len(nums):
                    ans.append(path[:])
                for x in counter:  # dont pick duplicates
                    if counter[x] > 0:
                        path.append(x)
                        counter[x] -= 1
                        btrack(path, counter)
                        path.pop()
                        counter[x] += 1
            ans = []
            btrack([], Counter(nums))
            return ans
    



