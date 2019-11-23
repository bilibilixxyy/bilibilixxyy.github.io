---
layout: post
title: 1046. Max Consecutive Ones III
---
### Question
Given an array `A` of 0s and 1s, we may change up to `K` values from 0 to 1.

Return the length of the longest (contiguous) subarray that contains only 1s.



 **Example 1:**

    
    
     **Input:** A = [1,1,1,0,0,0,1,1,1,1,0], K = 2
    **Output:** 6
    **Explanation:**
    [1,1,1,0,0, _ **1** ,1,1,1,1, **1**_ ]
    Bolded numbers were flipped from 0 to 1.  The longest subarray is underlined.

**Example 2:**

    
    
    **Input:** A = [0,0,1,1,0,0,1,1,1,0,1,1,0,0,0,1,1,1,1], K = 3
    **Output:** 10
    **Explanation:**
    [0,0, _1,1, **1** , **1** ,1,1,1, **1** ,1,1_,0,0,0,1,1,1,1]
    Bolded numbers were flipped from 0 to 1.  The longest subarray is underlined.
    



 **Note:**

  1. `1 <= A.length <= 20000`
  2. `0 <= K <= A.length`
  3. `A[i]` is `0` or `1` 

### Solution 1
##  **Intuition**

Translation:  
Find the longest subarray with at most `K` zeros.

##  **Explanation**

For each `A[j]`, try to find the longest subarray.  
If `A[i] ~ A[j]` has zeros <= `K`, we continue to increment `j`.  
If `A[i] ~ A[j]` has zeros > `K`, we increment `i`.

 **Java:**

    
    
         public int longestOnes(int[] A, int K) {
            int i = 0, j;
            for (j = 0; j < A.length; ++j) {
                if (A[j] == 0) K--;
                if (K < 0 && A[i++] == 0) K++;
            }
            return j - i;
        }
    

**C++:**

    
    
        int longestOnes(vector<int>& A, int K) {
            int  i = 0, j;
            for (j = 0; j < A.size(); ++j) {
                if (A[j] == 0) K--;
                if (K < 0 && A[i++] == 0) K++;
            }
            return j - i;
        }
    

**Python:**

    
    
        def longestOnes(self, A, K):
            i =  0
            for j in xrange(len(A)):
                K -= 1 - A[j]
                if K < 0:
                    K += 1 - A[i]
                    i += 1
            return j - i + 1
    

## **Update**

Suggested by @migeater, we don't need to reduce the the size of sliding
window.  
as we want to find the maximum window.


### Solution 2
I think the question is a simplified version of [424\. Longest Repeating
Character Replacement](https://leetcode.com/problems/longest-repeating-
character-replacement/)  
My solution is changed from [Java 12 lines O(n) sliding window solution with
explanation](https://leetcode.com/problems/longest-repeating-character-
replacement/discuss/91271/Java-12-lines-O\(n\)-sliding-window-solution-with-
explanation)

    
    
    public int longestOnes(int[] A, int K) {
            int zeroCount=0,start=0,res=0;
            for(int end=0;end<A.length;end++){
                if(A[end] == 0) zeroCount++;
                while(zeroCount > K){
                    if(A[start] == 0) zeroCount--;
                    start++;
                }
                res=Math.max(res,end-start+1);
            }
            return res;
        }
    


### Solution 3
**Intuition**  
Let the total number of zeroes be `countZeroes`.

 **Case 1** `k` is greater than or equal to `countZeroes`

  * In this case, we apply the greedy approach, and convert all the zeroes to ones, eventually converting the entire array to 1s, (Why is this the best choice?), Because if we indeed left out any zero, we can still improve upon the count of ones by converting it to 1, becuase it is permissible.

 **Case 2** `k` is less than `countZeros`

  *  **Claim** We should convert `k` consecutive zeroes to ones.
  *  **Proof** Suppose we do not convert `k` consecutive zeroes to ones. This implies that in an optimal selection, there would be at least 2 `disjoint clusters` , separated by atleast one zero. These clusters can convert all of its zeroes to ones. Now, since we are only allowed to take contigous subarray, we can only take the best possible outcome of the two, WLOG, say cluster 1 gives you a larger number of ones (after all operations). Now, is this really optimal? Clearly no, since we can always improve by converting the zeroes to ones which separates the 2 clusters instead of wasting the move on the 2nd cluster. Hence, we have a contradiction. Therefore, in the optimal strategy, we need to convert `k` consecutive zeroes to ones.

 **windowSize** We define the window size as the number of zeroes contained in
that window

  * Clearly we need to find the window which contains the maximum number of ones and has size atmost `k`. We can then convert all the zeroes in the window to get an amount equal to `windowSize`\+ `amountOfPreviousOnes`.

 **Algorithm**

  * Create a deque to hold the array elements (values). Create variables to store the count of zeroes, count of ones, and the best answer so far.
  * Iterate over each element of the array. If the element is one, just push it in the back(as it cannot contribute to window size) and update the count. If the element is zero, update the window size and then push it to the back.
  * If the length of the window exceeds `k`, start contracting the window from the front. Since `1's` did not contribute to the size, keep popping them till you see a zero. As soon as you see a zero, pop it and the window size decreases by 1.
  * Update the maxPossibleResult so far in each iteration. All elements in the current window can be converted to 1.

 **Miscellaneous**

  * Updation of the result should be dones after confirming that the window size does not exceed `k`.
  * The return type of `dq.size()` is `size_t` and hence, we need to typecast it before calling `max`.

 **Time Complexity**

  * O(n)
  * Space complexity can be reduced to O(1) by using 2 pointers instead of deque. However, I've skipped that to avoid corner cases with indices.

    
    
    class Solution
    {
    public:
        int longestOnes(vector<int>& a, int k);
    };
    
    /* Returns the largest sum possible by flipping zeroes */
    int Solution :: longestOnes(vector<int>& a, int k)
    {
        // Dequeue to hold the array elements 
        deque<int> dq;
        int countOfOnes=0, windowSize=0, maxLength=0;
        
        /* windowSize is the number of zeroes in the deque */
        
        // Traverse the array and insert each element in the dequeue
        for(int i=0; i<a.size(); i++)
        {
            // If the element is one, insert it and increase the count of ones
            // If the element is zero, insert it and increase windowSize (by Definition)
            if(a[i]==1) dq.push_back(a[i]), countOfOnes++;
            else        dq.push_back(a[i]), windowSize++;
            
            // If windowSize exceeds the maximum permissible, contract the window from the left
            if(windowSize > k)
            {   
                // Keep contracting till you get zero, as 1 has no contribution to size
                while(dq.front()==1)
                    dq.pop_front(), countOfOnes--;
                
                // Now you have reached a zero, pop it to actually reduce the windowSize
                dq.pop_front();
                windowSize --;
            }
            
            // All elements in the deque can be converted to ones, since windwoSize<=k */
            maxLength = max(maxLength, (int)dq.size());
        }
        
        // Return the maximum sum possible after flipping
        return maxLength;
    }
    

[JavaScript Implementation](https://github.com/Just-A-
Visitor/Coding/tree/master/LeetCode%20Contests/LeetCode%20Contest%20%23126/%231004%20Max%20Consecutive%20Ones%20III)



