---
layout: post
title: 697. Degree of an Array
---
### Question
Given a non-empty array of non-negative integers `nums`, the **degree** of
this array is defined as the maximum frequency of any one of its elements.

Your task is to find the smallest possible length of a (contiguous) subarray
of `nums`, that has the same degree as `nums`.

 **Example 1:**  

    
    
     **Input:** [1, 2, 2, 3, 1]
    **Output:** 2
    **Explanation:** 
    The input array has a degree of 2 because both elements 1 and 2 appear twice.
    Of the subarrays that have the same degree:
    [1, 2, 2, 3, 1], [1, 2, 2, 3], [2, 2, 3, 1], [1, 2, 2], [2, 2, 3], [2, 2]
    The shortest length is 2. So return 2.
    

**Example 2:**  

    
    
    **Input:** [1,2,2,3,1,4,2]
    **Output:** 6
    

**Note:**

* `nums.length` will be between 1 and 50,000.
* `nums[i]` will be an integer between 0 and 49,999.

### Solution 1
The wording of the example's explanation does not make it clear if 2 is being
returned because it is highest degree, what number has the highest degree, or
"length of shortest contiguous subarray that has the same highest degree of
the original array"

Now that I understand the problem it makes sense, but I definitely think a
better example could have been chosen.

Also, the title has almost nothing to do the with the question, as evidenced
by the fact that the default function name is far from what the title is.

EDIT: Clearer example:

    
    
    Input: [1, 7, 7, 5, 7, 1]
    Output: 4
    

Explanation: The degree of this array is 3 because 7 appears the most of any
number. The shortest subarray that you can make that has a degree of 3 is
[7,7,5,7]. The length of that subarray is 4, so we return 4.


### Solution 2
    
    
     public int findShortestSubArray(int[] nums) {
            if (nums.length == 0 || nums == null) return 0;
            Map<Integer, int[]> map = new HashMap<>();
            for (int i = 0; i < nums.length; i++){
               if (!map.containsKey(nums[i])){
                   map.put(nums[i], new int[]{1, i, i});  // the first element in array is degree, second is first index of this key, third is last index of this key
               } else {
                   int[] temp = map.get(nums[i]);
                   temp[0]++;
                   temp[2] = i;
               }
            }
            int degree = Integer.MIN_VALUE, res = Integer.MAX_VALUE;
            for (int[] value : map.values()){
                if (value[0] > degree){
                    degree = value[0];
                    res = value[2] - value[1] + 1;
                } else if (value[0] == degree){
                    res = Math.min( value[2] - value[1] + 1, res);
                } 
            }
            return res;
        }
    


### Solution 3
I hardly find the reason to give a second pass, especilly for C++ and Java.  
Also the problem can be easily solved by only O(M) space, where M is the size
of numbers set.  
So I write this post to let you forget those multi-pass and O(N) space
solutions.

    
    
    loop only once on array {
        record the index of first occureence;
        update number counter;
        update result;
    }
    return result;
    

C++

    
    
        int findShortestSubArray(vector<int>& nums) {
            unordered_map<int, int> counter, first;
            int res = 0, degree = 0;
            for (int i = 0; i < nums.size(); ++i) {
                if (first.count(nums[i]) == 0) first[nums[i]] = i;
                if (++counter[nums[i]] > degree) {
                    degree = counter[nums[i]];
                    res = i - first[nums[i]] + 1;
                } else if (counter[nums[i]] == degree)
                    res = min(res, i - first[nums[i]] + 1);
            }
            return res;
        }
    

Jave:

    
    
            Map<Integer, Integer> counter = new HashMap<>(), first = new HashMap<>();
            int res = 0, degree = 0;
            for (int i = 0; i < nums.length; ++i) {
                first.putIfAbsent(nums[i], i);
                counter.put(nums[i], counter.getOrDefault(nums[i], 0) + 1);
                if (counter.get(nums[i]) > degree) {
                    degree = counter.get(nums[i]);
                    res = i - first.get(nums[i]) + 1;
                } else if (counter.get(nums[i]) == degree)
                    res = Math.min(res, i - first.get(nums[i]) + 1);
            }
            return res;
    

Python:

    
    
        def findShortestSubArray(self, nums):
            first, counter, res, degree = {}, {}, 0, 0
            for i, v in enumerate(nums):
                first.setdefault(v, i)
                counter[v] = counter.get(v, 0) + 1
                if counter[v] > degree:
                    degree = counter[v]
                    res = i - first[v] + 1
                elif counter[v] == degree:
                    res = min(res, i - first[v] + 1)
            return res
    



