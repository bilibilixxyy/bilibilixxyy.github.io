---
layout: post
title: 88. Merge Sorted Array
---
### Question
Given two sorted integer arrays _nums1_ and _nums2_ , merge _nums2_ into
_nums1_ as one sorted array.

 **Note:**

  * The number of elements initialized in _nums1_ and _nums2_ are _m_ and _n_ respectively.
  * You may assume that _nums1_ has enough space (size that is greater or equal to _m_ \+ _n_ ) to hold additional elements from _nums2_.

 **Example:**

    
    
     **Input:**
    nums1 = [1,2,3,0,0,0], m = 3
    nums2 = [2,5,6],       n = 3
    
    **Output:**  [1,2,2,3,5,6]
    

### Solution 1
    
    
     class Solution {
    public:
        void merge(int A[], int m, int B[], int n) {
            int i=m-1;
    		int j=n-1;
    		int k = m+n-1;
    		while(i >=0 && j>=0)
    		{
    			if(A[i] > B[j])
    				A[k--] = A[i--];
    			else
    				A[k--] = B[j--];
    		}
    		while(j>=0)
    			A[k--] = B[j--];
        }
    };


### Solution 2
    
    
    def merge(self, nums1, m, nums2, n):
            while m > 0 and n > 0:
                if nums1[m-1] >= nums2[n-1]:
                    nums1[m+n-1] = nums1[m-1]
                    m -= 1
                else:
                    nums1[m+n-1] = nums2[n-1]
                    n -= 1
            if n > 0:
                nums1[:n] = nums2[:n]


### Solution 3
This code relies on the simple observation that once all of the numbers from
`nums2` have been merged into `nums1`, the rest of the numbers in `nums1` that
were not moved are already in the correct place.

    
    
    class Solution {
    public:
        void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
            int i = m - 1, j = n - 1, tar = m + n - 1;
            while (j >= 0) {
                nums1[tar--] = i >= 0 && nums1[i] > nums2[j] ? nums1[i--] : nums2[j--];
            }
        }
    };



