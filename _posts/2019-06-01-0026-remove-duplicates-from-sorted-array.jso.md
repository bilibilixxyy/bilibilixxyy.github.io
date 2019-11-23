---
layout: post
title: 26. Remove Duplicates from Sorted Array
---
### Question
Given a sorted array _nums_ , remove the duplicates [**in-
place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that each
element appear only _once_ and return the new length.

Do not allocate extra space for another array, you must do this by **modifying
the input array[in-place](https://en.wikipedia.org/wiki/In-place_algorithm)**
with O(1) extra memory.

 **Example 1:**

    
    
    Given _nums_ = **[1,1,2]** ,
    
    Your function should return length = **2** , with the first two elements of _nums_ being **1** and **2** respectively.
    
    It doesn't matter what you leave beyond the returned length.

**Example 2:**

    
    
    Given _nums_ = **[0,0,1,1,1,2,2,3,3,4]** ,
    
    Your function should return length = **5** , with the first five elements of _nums_ being modified to   **0** , **1** , **2** , **3** , and  **4** respectively.
    
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
    
    
    int count = 0;
    for(int i = 1; i < n; i++){
        if(A[i] == A[i-1]) count++;
        else A[i-count] = A[i];
    }
    return n-count;


### Solution 2
    
    
    class Solution {
        public:
        int removeDuplicates(int A[], int n) {
            if(n < 2) return n;
            int id = 1;
            for(int i = 1; i < n; ++i) 
                if(A[i] != A[i-1]) A[id++] = A[i];
            return id;
        }
    };


### Solution 3
I don't like old-style indexed looping. I much prefer the "enhanced" (Java) /
"range-based" (C++) loops, they make things much cleaner.

* * *

 **C++**

    
    
     int removeDuplicates(vector<int>& nums) {
        int i = 0;
        for (int n : nums)
            if (!i || n > nums[i-1])
                nums[i++] = n;
        return i;
    }
    

And to not need the `!i` check in the loop:

    
    
    int removeDuplicates(vector<int>& nums) {
        int i = !nums.empty();
        for (int n : nums)
            if (n > nums[i-1])
                nums[i++] = n;
        return i;
    }
    

* * *

**Java**

    
    
     public int removeDuplicates(int[] nums) {
        int i = 0;
        for (int n : nums)
            if (i == 0 || n > nums[i-1])
                nums[i++] = n;
        return i;
    }
    

And to not need the `i == 0` check in the loop:

    
    
    public int removeDuplicates(int[] nums) {
        int i = nums.length > 0 ? 1 : 0;
        for (int n : nums)
            if (n > nums[i-1])
                nums[i++] = n;
        return i;
    }



