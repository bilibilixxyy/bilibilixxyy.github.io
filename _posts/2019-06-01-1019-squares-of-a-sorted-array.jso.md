---
layout: post
title: 1019. Squares of a Sorted Array
---
### Question
Given an array of integers `A` sorted in non-decreasing order, return an array
of the squares of each number, also in sorted non-decreasing order.



 **Example 1:**

    
    
     **Input:** [-4,-1,0,3,10]
    **Output:** [0,1,9,16,100]
    

**Example 2:**

    
    
    **Input:** [-7,-3,2,3,11]
    **Output:** [4,9,9,49,121]
    



 **Note:**

  1. ` 1 <= A.length <= 10000`
  2. `-10000 <= A[i] <= 10000`
  3. `A` is sorted in non-decreasing order.

### Solution 1
    
    
     class Solution {
        public int[] sortedSquares(int[] A) {
            int n = A.length;
            int[] result = new int[n];
            int i = 0, j = n - 1;
            for (int p = n - 1; p >= 0; p--) {
                if (Math.abs(A[i]) > Math.abs(A[j])) {
                    result[p] = A[i] * A[i];
                    i++;
                } else {
                    result[p] = A[j] * A[j];
                    j--;
                }
            }
            return result;
        }
    }
    


### Solution 2
    
    
    def sortedSquares(self, A):
    	answer = collections.deque()
    	l, r = 0, len(A) - 1
    	while l <= r:
    		left, right = abs(A[l]), abs(A[r])
    		if left > right:
    			answer.appendleft(left * left)
    			l += 1
    		else:
    			answer.appendleft(right * right)
    			r -= 1
    	return list(answer)
    

The question boils down to understanding that if we look at the magnitude of
the elements in the array, both ends "slide down" and converge towards the
center of the array. With that understanding, we can use two pointers, one at
each end, to iteratively collect the larger square to a list. However,
collecting the larger square in a list with `list`'s `append`, results in
elements sorted in descending order. To circumvent this, we need to append to
the left of the list. Using a `collections.deque()` allows us to append
elements to the left of `answer` in O(1) time, maintaining the required
increasing order.

**Alternative without deque or list reversal**

    
    
     def sortedSquares(self, A):
    	answer = [0] * len(A)
    	l, r = 0, len(A) - 1
    	while l <= r:
    		left, right = abs(A[l]), abs(A[r])
    		if left > right:
    			answer[r - l] = left * left
    			l += 1
    		else:
    			answer[r - l] = right * right
    			r -= 1
    	return answer
    

We first declare a list of length, `len(A)` then add the larger square from
the back of the list, denoted by the index `r - l`.


### Solution 3
We find the first non-negative element in the array. From there, we move
'negative' pointer (`np`) towards zero, and 'positive' pointer (`pp`) towards
the end of the array, selecting the next square.

    
    
    vector<int> sortedSquares(vector<int>& A) {
      vector<int> res;
      int pvt = lower_bound(begin(A), end(A), 0) - begin(A);
      for (int pp = pvt, pn = pvt - 1; pp < A.size() || pn >= 0;) {
        if (abs(pn >= 0 ? A[pn] : INT_MAX) < abs(pp < A.size() ? A[pp] : INT_MAX))
          res.push_back(pow(A[pn--], 2));
        else res.push_back(pow(A[pp++], 2));
      }
      return res;
    }
    

In a way, we are going _inside out_ and filling our result vector from the
beginning. Alternativelly, we can go _outside in_ and fill the result in the
reverse order:

    
    
     vector<int> sortedSquares(vector<int>& A) {
      vector<int> res(A.size());
      for (int pn = 0, pp = A.size() - 1, pos = A.size() - 1; pn <= pp; --pos)
        res[pos] = pow(abs(A[pn]) < abs(A[pp]) ? A[pp--] : A[pn++], 2);
      return res;
    }  
    

Java version:

    
    
    public int[] sortedSquares(int[] A) {
      int[] res = new int[A.length];
      for (int pn = 0, pp = A.length - 1, pos = A.length - 1; pn <= pp; --pos)
        res[pos] = (int)Math.pow(Math.abs(A[pn]) < Math.abs(A[pp]) ? A[pp--] : A[pn++], 2);
      return res;
    }
    



