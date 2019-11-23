---
layout: post
title: 462. Minimum Moves to Equal Array Elements II
---
### Question
Given a **non-empty** integer array, find the minimum number of moves required
to make all array elements equal, where a move is incrementing a selected
element by 1 or decrementing a selected element by 1.

You may assume the array's length is at most 10,000.

 **Example:**

    
    
     **Input:**
    [1,2,3]
    
    **Output:**
    2
    
    **Explanation:**
    Only two moves are needed (remember each move increments or decrements one element):
    
    [1,2,3]  = >  [2,2,3]  =>  [2,2,2]
    

### Solution 1
    
    
     public class Solution {
        public int minMoves2(int[] nums) {
            Arrays.sort(nums);
            int i = 0, j = nums.length-1;
            int count = 0;
            while(i < j){
                count += nums[j]-nums[i];
                i++;
                j--;
            }
            return count;
        }
    }


### Solution 2
    
    
    def minMoves2(self, nums):
        median = sorted(nums)[len(nums) / 2]
        return sum(abs(num - median) for num in nums)
    
    def minMoves2(self, nums):
        nums.sort()
        return sum(nums[~i] - nums[i] for i in range(len(nums) / 2))


### Solution 3
This solution relies on the fact that if we increment/decrement each element
to the median of all the elements, the optimal number of moves is necessary.
The median of all elements can be found in expected O(n) time using
QuickSelect (or deterministic O(n) time using Median of Medians).

    
    
    public int minMoves2(int[] A) {
        int sum = 0, median = quickselect(A, A.length/2+1, 0, A.length-1);
        for (int i=0;i<A.length;i++) sum += Math.abs(A[i] - median);
        return sum;
    }
    
    public int quickselect(int[] A, int k, int start, int end) {
        int l = start, r = end, pivot = A[(l+r)/2];
        while (l<=r) {
            while (A[l] < pivot) l++;
            while (A[r] > pivot) r--;
            if (l>=r) break;
            swap(A, l++, r--);
        }
        if (l-start+1 > k) return quickselect(A, k, start, l-1);
        if (l-start+1 == k && l==r) return A[l];
        return quickselect(A, k-r+start-1, r+1, end);
    }
    
    public void swap(int[] A, int i, int j) {
        int temp = A[i];
        A[i] = A[j];
        A[j] = temp;
    }
    



