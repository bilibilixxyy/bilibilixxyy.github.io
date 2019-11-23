---
layout: post
title: 216. Combination Sum III
---
### Question
Find all possible combinations of _**k**_ numbers that add up to a number
_**n**_ , given that only numbers from 1 to 9 can be used and each combination
should be a unique set of numbers.

 **Note:**

  * All numbers will be positive integers.
  * The solution set must not contain duplicate combinations.

 **Example 1:**

    
    
     **Input:** _**k**_ = 3, _**n**_ = 7
    **Output:** [[1,2,4]]
    

**Example 2:**

    
    
    **Input:** _**k**_ = 3, _**n**_ = 9
    **Output:** [[1,2,6], [1,3,5], [2,3,4]]
    

### Solution 1
    
    
      public List<List<Integer>> combinationSum3(int k, int n) {
        List<List<Integer>> ans = new ArrayList<>();
        combination(ans, new ArrayList<Integer>(), k, 1, n);
        return ans;
    }
    
    private void combination(List<List<Integer>> ans, List<Integer> comb, int k,  int start, int n) {
    	if (comb.size() == k && n == 0) {
    		List<Integer> li = new ArrayList<Integer>(comb);
    		ans.add(li);
    		return;
    	}
    	for (int i = start; i <= 9; i++) {
    		comb.add(i);
    		combination(ans, comb, k, i+1, n-i);
    		comb.remove(comb.size() - 1);
    	}
    }


### Solution 2
    
    
    class Solution {
    public:
      void combination(vector<vector<int>>& result, vector<int> sol, int k, int n) {
        if (sol.size() == k && n == 0) { result.push_back(sol); return ; }
        if (sol.size() < k) {
          for (int i = sol.empty() ? 1 : sol.back() + 1; i <= 9; ++i) {
            if (n - i < 0) break;
            sol.push_back(i);
            combination(result, sol, k, n - i);
            sol.pop_back();
          }
        }
      }
    
      vector<vector<int>> combinationSum3(int k, int n) {
        vector<vector<int>> result;
        vector<int> sol;
        combination(result, sol, k, n);
        return result;
      }
    };


### Solution 3
Used backtracking to solve this.  
Build an array to apply to "subset" template. Each time we add an element to
the "list", for the next step, target= target - num[i]. Since we have already
added one element, for the next step, we can only add k-1 elements. Since no
duplicated elements accept, for the next loop, the "start" should point to the
next index of current index. The `list.remove(list.size() - 1)` here means, we
need to change the element here. I know it is hard to understand it, let me
give you an example.  
When `k=3, n=9`, my answer works like this:  
[1]->[1,2]->[1,2,3]. Since now sum is not 9, no more backtracking, so after
`list.remove(list.size() - 1)`, it is [1,2]. Then next follows [1,2,4], sum is
not 9, repeat process above untill [1,2,6]. When go to next backtracking, the
list will be added to `result`, and for this list, no more backtracking.  
Now we can go back to a previous backtracking, which is [1,3]->[1,3,4], fail.
[1,4,]->[1,4,5], fail. And so one.  
So the point of `list.remove(list.size() - 1)` is, after each "fail" or
"success", since we don't need to do further attempts given such a condition,
we delete the last element, and then end current backtracking. Next step is,
add the next element to the deleted index, go on attempting.

If you have other questions, just reply me.

    
    
    public class Solution {
    public List<List<Integer>> combinationSum3(int k, int n) {
        int[] num = {1,2,3,4,5,6,7,8,9};
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        helper(result, new ArrayList<Integer>(), num, k, n,0);
        return result;
        }
    
    public void helper(List<List<Integer>> result, List<Integer> list, int[] num, int k, int target, int start){
        if (k == 0 && target == 0){
            result.add(new ArrayList<Integer>(list));
        } else {
            for (int i = start; i < num.length && target > 0 && k >0; i++){
                list.add(num[i]);
                helper(result, list, num, k-1,target-num[i],i+1);
                list.remove(list.size()-1);
            }
        }
    }
    

}



