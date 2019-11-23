---
layout: post
title: 457. Circular Array Loop
---
### Question
You are given a **circular** array `nums` of positive and negative integers.
If a number _k_ at an index is positive, then move forward _k_ steps.
Conversely, if it's negative (- _k_ ), move backward _k_  steps. Since the
array is circular, you may assume that the last element's next element is the
first element, and the first element's previous element is the last element.

Determine if there is a loop (or a cycle) in `nums`. A cycle must start and
end at the same index and the cycle's length > 1\. Furthermore, movements in a
cycle must all follow a single direction. In other words, a cycle must not
consist of both forward and backward movements.



 **Example 1:**

    
    
     **Input:** [2,-1,1,2,2]
    **Output:** true
    **Explanation:** There is a cycle, from index 0 - > 2 -> 3 -> 0. The cycle's length is 3.
    

**Example 2:**

    
    
    **Input:** [-1,2]
    **Output:** false
    **Explanation:** The movement from index 1 - > 1 -> 1 ... is not a cycle, because the cycle's length is 1. By definition the cycle's length must be greater than 1.
    

**Example 3:**

    
    
    **Input:** [-2,1,-1,-2,-2]
    **Output:** false
    **Explanation:** The movement from index 1 - > 2 -> 1 -> ... is not a cycle, because movement from index 1 -> 2 is a forward movement, but movement from index 2 -> 1 is a backward movement. All movements in a cycle must follow a single direction.



 **Note:**

  1. -1000 ≤ nums[i] ≤ 1000
  2. nums[i] ≠ 0
  3. 1 ≤ nums.length ≤ 5000



 **Follow up:**

Could you solve it in **O(n)** time complexity and   **O(1)** extra space
complexity?

### Solution 1
Just think it as finding a loop in Linkedlist, except that loops with only 1
element do not count. Use a slow and fast pointer, slow pointer moves 1 step a
time while fast pointer moves 2 steps a time. If there is a loop (fast ==
slow), we return true, else if we meet element with different directions, then
the search fail, we set all elements along the way to 0. Because 0 is fail for
sure so when later search meet 0 we know the search will fail.

    
    
    public class Solution {
        public boolean circularArrayLoop(int[] nums) {
            int n = nums.length;
            for (int i = 0; i < n; i++) {
                if (nums[i] == 0) {
                    continue;
                }
                // slow/fast pointer
                int j = i, k = getIndex(i, nums);
                while (nums[k] * nums[i] > 0 && nums[getIndex(k, nums)] * nums[i] > 0) {
                    if (j == k) {
                        // check for loop with only one element
                        if (j == getIndex(j, nums)) {
                            break;
                        }
                        return true;
                    }
                    j = getIndex(j, nums);
                    k = getIndex(getIndex(k, nums), nums);
                }
                // loop not found, set all element along the way to 0
                j = i;
                int val = nums[i];
                while (nums[j] * val > 0) {
                    int next = getIndex(j, nums);
                    nums[j] = 0;
                    j = next;
                }
            }
            return false;
        }
        
        public int getIndex(int i, int[] nums) {
            int n = nums.length;
            return i + nums[i] >= 0? (i + nums[i]) % n: n + ((i + nums[i]) % n);
        }
    }
    


### Solution 2
For example, starting at index 1, nums[1] is 1, move 1 step forward to index
2. Then nums[2] is -1, move back 1 step to index 1. The loop contains indices
1 and 2. Is this a valid loop?


### Solution 3
Hi, there might be an interesting problem underlying this question, but as
worded, it is terribly confusing. The vote ratio is one of the worst
(something like 90 up, 600 down). I feel like my time has just been wasted.
Can we send this question to the archive or rewrite it?



