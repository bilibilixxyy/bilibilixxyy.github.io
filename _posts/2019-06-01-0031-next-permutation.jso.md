---
layout: post
title: 31. Next Permutation
---
### Question
Implement **next permutation** , which rearranges numbers into the
lexicographically next greater permutation of numbers.

If such arrangement is not possible, it must rearrange it as the lowest
possible order (ie, sorted in ascending order).

The replacement must be **[in-place](http://en.wikipedia.org/wiki/In-
place_algorithm)** and use only constant  extra memory.

Here are some examples. Inputs are in the left-hand column and its
corresponding outputs are in the right-hand column.

`1,2,3` → `1,3,2`  
`3,2,1` → `1,2,3`  
`1,1,5` → `1,5,1`

### Solution 1
According to
[Wikipedia](https://en.wikipedia.org/wiki/Permutation#Generation_in_lexicographic_order),
a man named Narayana Pandita presented the following simple algorithm to solve
this problem in the 14th century.

  1. Find the largest index `k` such that `nums[k] < nums[k + 1]`. If no such index exists, just reverse `nums` and done.
  2. Find the largest index `l > k` such that `nums[k] < nums[l]`.
  3. Swap `nums[k]` and `nums[l]`.
  4. Reverse the sub-array `nums[k + 1:]`.

    
    
    class Solution {
    public:
        void nextPermutation(vector<int>& nums) {
        	int n = nums.size(), k, l;
        	for (k = n - 2; k >= 0; k--) {
                if (nums[k] < nums[k + 1]) {
                    break;
                }
            }
        	if (k < 0) {
        	    reverse(nums.begin(), nums.end());
        	} else {
        	    for (l = n - 1; l > k; l--) {
                    if (nums[l] > nums[k]) {
                        break;
                    }
                } 
        	    swap(nums[k], nums[l]);
        	    reverse(nums.begin() + k + 1, nums.end());
            }
        }
    }; 
    

The above algorithm can also handle duplicates and thus can be further used to
solve [Permutations](https://leetcode.com/problems/permutations/) and
[Permutations II](https://leetcode.com/problems/permutations-ii/).


### Solution 2
My idea is for an array:

  1. Start from its last element, traverse backward to find the first one with index i that satisfy num[i-1] < num[i]. So, elements from num[i] to num[n-1] is reversely sorted.
  2. To find the next permutation, we have to swap some numbers at different positions, to minimize the increased amount, we have to make the highest changed position as high as possible. Notice that index larger than or equal to i is not possible as num[i,n-1] is reversely sorted. So, we want to increase the number at index i-1, clearly, swap it with the smallest number between num[i,n-1] that is larger than num[i-1]. For example, original number is 121543321, we want to swap the '1' at position 2 with '2' at position 7.
  3. The last step is to make the remaining higher position part as small as possible, we just have to reversely sort the num[i,n-1]

The following is my code:

    
    
    public void nextPermutation(int[] num) {
        int n=num.length;
        if(n<2)
            return;
        int index=n-1;        
        while(index>0){
            if(num[index-1]<num[index])
                break;
            index--;
        }
        if(index==0){
            reverseSort(num,0,n-1);
            return;
        }
        else{
            int val=num[index-1];
            int j=n-1;
            while(j>=index){
                if(num[j]>val)
                    break;
                j--;
            }
            swap(num,j,index-1);
            reverseSort(num,index,n-1);
            return;
        }
    }
    
    public void swap(int[] num, int i, int j){
        int temp=0;
        temp=num[i];
        num[i]=num[j];
        num[j]=temp;
    }
    
    public void reverseSort(int[] num, int start, int end){   
        if(start>end)
            return;
        for(int i=start;i<=(end+start)/2;i++)
            swap(num,i,start+end-i);
    }


### Solution 3
Using a simple example, we can derive the following steps:

    
    
    public void nextPermutation(int[] A) {
        if(A == null || A.length <= 1) return;
        int i = A.length - 2;
        while(i >= 0 && A[i] >= A[i + 1]) i--; // Find 1st id i that breaks descending order
        if(i >= 0) {                           // If not entirely descending
            int j = A.length - 1;              // Start from the end
            while(A[j] <= A[i]) j--;           // Find rightmost first larger id j
            swap(A, i, j);                     // Switch i and j
        }
        reverse(A, i + 1, A.length - 1);       // Reverse the descending sequence
    }
    
    public void swap(int[] A, int i, int j) {
        int tmp = A[i];
        A[i] = A[j];
        A[j] = tmp;
    }
    
    public void reverse(int[] A, int i, int j) {
        while(i < j) swap(A, i++, j--);
    }



