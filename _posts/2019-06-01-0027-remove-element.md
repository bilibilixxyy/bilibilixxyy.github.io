---
layout: post
title: 27. Remove Element
---
### Question
Given an array _nums_ and a value _val_ , remove all instances of that value
[**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) and return
the new length.

Do not allocate extra space for another array, you must do this by **modifying
the input array[in-place](https://en.wikipedia.org/wiki/In-place_algorithm)**
with O(1) extra memory.

The order of elements can be changed. It doesn't matter what you leave beyond
the new length.

 **Example 1:**

    
    
    Given _nums_ = **[3,2,2,3]** , _val_ = **3** ,
    
    Your function should return length = **2** , with the first two elements of _nums_ being **2**.
    
    It doesn't matter what you leave beyond the returned length.
    

**Example 2:**

    
    
    Given _nums_ = **[0,1,2,2,3,0,4,2]** , _val_ = **2** ,
    
    Your function should return length = **5** , with the first five elements of _nums_ containing   **0** , **1** , **3** , **0** , and  **4**.
    
    Note that the order of those five elements can be arbitrary.
    
    It doesn't matter what values are set beyond  the returned length.

**Clarification:**

Confused why the returned value is an integer but your answer is an array?

Note that the input array is passed in by **reference** , which means
modification to the input array will be known to the caller as well.

Internally you can think of this:

    
    
    // **nums** is passed in by reference. (i.e., without making a copy)
    int len = removeElement(nums, val);
    
    // any modification to **nums** in your function would be known by the caller.
    // using the length returned by your function, it prints the first **len** elements.
    for (int i = 0; i  < len; i++) {
        print(nums[i]);
    }

### Solution 1
    
    
    int removeElement(int A[], int n, int elem) {
        int begin=0;
        for(int i=0;i<n;i++) if(A[i]!=elem) A[begin++]=A[i];
        return begin;
    }


### Solution 2
public class Solution {

    
    
    public int removeElement(int[] A, int elem) {
       int m = 0;    
       for(int i = 0; i < A.length; i++){
           
           if(A[i] != elem){
               A[m] = A[i];
               m++;
           }
       }
       
       return m;
    }
    

}


### Solution 3
    
    
    int removeElement(vector<int>& nums, int val) {
        int cnt = 0;
        for(int i = 0 ; i < nums.size() ; ++i) {
            if(nums[i] == val)
                cnt++;
            else
                nums[i-cnt] = nums[i];
        }
        return nums.size()-cnt;
    }



