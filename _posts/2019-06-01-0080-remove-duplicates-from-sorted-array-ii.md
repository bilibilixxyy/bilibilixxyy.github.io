---
layout: post
title: 80. Remove Duplicates from Sorted Array II
---
### Question
Given a sorted array _nums_ , remove the duplicates [**in-
place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that
duplicates appeared at most  _twice_ and return the new length.

Do not allocate extra space for another array, you must do this by **modifying
the input array[in-place](https://en.wikipedia.org/wiki/In-place_algorithm)**
with O(1) extra memory.

 **Example 1:**

    
    
    Given _nums_ = **[1,1,1,2,2,3]** ,
    
    Your function should return length = **5** , with the first five elements of _nums_ being **1, 1, 2, 2** and **3** respectively.
    
    It doesn't matter what you leave beyond the returned length.

**Example 2:**

    
    
    Given _nums_ = **[0,0,1,1,1,1,2,3,3]** ,
    
    Your function should return length = **7** , with the first seven elements of _nums_ being modified to   **0** , **0** , **1** , **1** , **2** , **3** and   **3** respectively.
    
    It doesn't matter what values are set beyond  the returned length.
    

**Clarification:**

Confused why the returned value is an integer but your answer is an array?

Note that the input array is passed in by **reference** , which means
modification to the input array will be known to the caller as well.

Internally you can think of this:

    
    
    // **nums** is passed in by reference. (i.e., without making a copy)
    int len = removeDuplicates(nums);
    
    // any modification to **nums** in your function would be known by the caller.
    // using the length returned by your function, it prints the first **len** elements.
    for (int i = 0; i  < len; i++) {
        print(nums[i]);
    }
    

### Solution 1
Same simple solution written in several languages. Just go through the numbers
and include those in the result that haven't been included twice already.

 **C++**

    
    
     int removeDuplicates(vector<int>& nums) {
        int i = 0;
        for (int n : nums)
            if (i < 2 || n > nums[i-2])
                nums[i++] = n;
        return i;
    }
    

**Java**

    
    
     public int removeDuplicates(int[] nums) {
        int i = 0;
        for (int n : nums)
            if (i < 2 || n > nums[i-2])
                nums[i++] = n;
        return i;
    }
    

**Python**

    
    
    def removeDuplicates(self, nums):
        i =  0
        for n in nums:
            if i < 2 or n > nums[i-2]:
                nums[i] = n
                i += 1
        return i
    

**Ruby**

    
    
     def remove_duplicates(nums)
        i = 0
        nums.each { |n| nums[(i+=1)-1] = n if i < 2 || n > nums[i-2] }
        i
    end


### Solution 2
I think both Remove Duplicates from Sorted Array I and II could be solved in a
consistent and more general way by allowing the duplicates to appear k times
(k = 1 for problem I and k = 2 for problem II). Here is my way: we need a
count variable to keep how many times the duplicated element appears, if we
encounter a different element, just set counter to 1, if we encounter a
duplicated one, we need to check this count, if it is already k, then we need
to skip it, otherwise, we can keep this element. The following is the
implementation and can pass both OJ:

    
    
    int removeDuplicates(int A[], int n, int k) {
    
                if (n <= k) return n;
    
                int i = 1, j = 1;
                int cnt = 1;
                while (j < n) {
                    if (A[j] != A[j-1]) {
                        cnt = 1;
                        A[i++] = A[j];
                    }
                    else {
                        if (cnt < k) {
                            A[i++] = A[j];
                            cnt++;
                        }
                    }
                    ++j;
                }
                return i;
    }
    

For more details, you can also see this post: [LeetCode Remove Duplicates from
Sorted Array I and II: O(N) Time and O(1) Space](http://tech-
wonderland.net/blog/leetcode-remove-duplicates-from-sorted-array-i-and-
ii.html)


### Solution 3
Question wants us to return the length of new array after removing duplicates
and that we don't care about what we leave beyond new length , hence we can
use `i` to keep track of the position and update the array.

* * *

Remove Duplicates from Sorted Array(no duplicates) :

    
    
    public int removeDuplicates(int[] nums) {
        int i = 0;
        for(int n : nums)
            if(i < 1 || n > nums[i - 1]) 
                nums[i++] = n;
        return i;
    }
    

Remove Duplicates from Sorted Array II (allow duplicates up to 2):

    
    
    public int removeDuplicates(int[] nums) {
       int i = 0;
       for (int n : nums)
          if (i < 2 || n > nums[i - 2])
             nums[i++] = n;
       return i;
    }



