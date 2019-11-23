---
layout: post
title: 963. Three Equal Parts
---
### Question
Given an array `A` of `0`s and `1`s, divide the array into 3 non-empty parts
such that all of these parts represent the same binary value.

If it is possible, return **any** `[i, j]` with `i+1 < j`, such that:

  * `A[0], A[1], ..., A[i]` is the first part;
  * `A[i+1], A[i+2], ..., A[j-1]` is the second part, and
  * `A[j], A[j+1], ..., A[A.length - 1]` is the third part.
  * All three parts have equal binary value.

If it is not possible, return `[-1, -1]`.

Note that the entire part is used when considering what binary value it
represents.  For example, `[1,1,0]` represents `6` in decimal, not `3`.  Also,
leading zeros are allowed, so `[0,1,1]` and `[1,1]` represent the same value.



 **Example 1:**

    
    
     **Input:** [1,0,1,0,1]
    **Output:** [0,3]
    

**Example 2:**

    
    
    **Input:** [1,1,0,1,1]
    **Output:** [-1,-1]



 **Note:**

  1. `3 <= A.length <= 30000`
  2. `A[i] == 0` or `A[i] == 1`

### Solution 1
Algorithm:  
1) Count no. of 1's in the given array, say `countNumberOfOnes`.  
2) If no 1 is found ie. `countNumberOfOnes == 0`, just return `{0,size-1}`  
3) If `countNumberOfOnes % 3 != 0` , then we cannot partition the given array
for sure. This is because, there is no way to put equal no. of 1's in any
partition and hence, we will get different binary representations.  
4) Let's try to find if there is a valid partition possible now. We find the
first 1 in the given array and represent it's position by `start`.  
5) Also, we know that each partition must have `countNumberOfOnes/3` (for same
reason as given in step 3). Therefore, after finding the first 1, leave `k =
countNumberOfOnes/3` 1's for the first partition.  
6) Assign this position as `mid` that denotes the beginning of a possible
second partition.  
7) Further leave `k = countNumberOfOnes/3` 1's for this partition and assign
the beginning of last partition as `end`  
8) Now, all we need to do is verify whether all the partitions have same
values in them. This can be done by iterating through to the end of the array.  
9) If `end` doesn't reach the end of the array, we find a mismatch and hence,
we need to return `{-1, -1}`  
10) Otherwise, we have found our partition, return `{start-1,mid}`

Time Complexity: `O(n)`  
Space Complexity: `O(1)`

    
    
    static int x=[](){ios::sync_with_stdio(false); cin.tie(NULL); return 0;}();
    
    class Solution {
    public:
        vector<int> threeEqualParts(vector<int>& A) {
    	    // Count no of 1's in the given array
            int countNumberOfOnes = 0;
            for(int c: A)
                if(c == 1)                  
                    countNumberOfOnes++;
    								
    	    // If no 1 is found, that means we can return ans as {0, size-1}
            if(countNumberOfOnes == 0)      
                return {0, A.size()-1};
    						
            // If no of 1's is not a multiple of 3, then we can never find a possible partition since
            // there will be atkeast one partition that will have different no of 1's and hence
            // different binary representation
            // For example,
            // Given :
            // 0000110  110  110 
            //       |  |    |
            //       i       j
            // Total no of ones = 6
            // 0000110         110      110
            //     |           |        |
            //     start       mid      end
            // Match starting from first 1, and putting 2 more pointers by skipping k 1's
            
            if(countNumberOfOnes % 3 != 0)            
                return {-1, -1};
    						
            // find size of each partition
            int k = countNumberOfOnes/3;
            int i;
            
            // find the first 1 in the array
            for(i=0;i<A.size(); i++)
                if(A[i] == 1)
                    break;
            int start = i;
            
            // find (k+1)th 1 in the array
            int count1 = 0;
            for(i=0;i<A.size(); i++)
            {
                if(A[i] == 1)
                    count1++;
                if(count1 == k + 1)
                    break;
            }
            int mid = i;
            
            //find (2*k +1)th 1 in the array
            count1= 0;
            for(i=0;i<A.size(); i++)
            {
                if(A[i] == 1)
                    count1++;
                if(count1 == 2*k + 1)
                    break;
            }
            int end = i;
            
            // Match all values till the end of the array
            while(end< A.size() && A[start] == A[mid] && A[mid] == A[end])
            {
                start++; mid++; end++;
            }
            
            // Return appropriate values if all the values have matched till the end
            if(end == A.size()) 
                return {start-1, mid};
            
            // otherwise, no such indices found
            return {-1, -1};
        }
    };
    
    

Code written during contest, may be optimized further. :-)


### Solution 2
    
    
    // The solution can be optimized to O(1) space, by not using StringBuilder, but only record the index of  the first one digit of the right part.
    
    class Solution {
        public int[] threeEqualParts(int[] A) {
            if (A == null || A.length < 3) return new int[] {-1, -1};
            int n = A.length;
            int cntOneBit = 0;
            
            for (int b : A) {
                if (b == 1) cntOneBit++;
            }
            if (cntOneBit % 3 != 0) return new int[] {-1, -1};
            
            int cnt = cntOneBit / 3;
            if (cnt == 0) return new int[] {0, n - 1};
        
            //construct the string using the right most part;
            int j = n - 1, cntR = 0;
            StringBuilder suffix = new StringBuilder();
            for (;j >= 0 && cntR < cnt; j--) {
                suffix.append(A[j]);
                if (A[j] == 1) cntR++;
             } 
        
            String target = suffix.reverse().toString();
            
            //matching the left part with target string, omit all leading zero
            int i = 0;
            while (A[i] == 0) i++;
        
            int k = 0;
            while (k < target.length()) {
                if (A[i + k] == target.charAt(k) - '0') k++;
                else return new int[] {-1, -1};
            }
            int left = i + k -1;
            
            //matching the middle part with target string, omit all leading zero
            i = i + k;
            while (A[i] == 0) i++;
            k = 0;
            while (k < target.length()) {
            if (A[i + k] == target.charAt(k) - '0') k++;
                else return new int[] {-1, -1};
            }    
            return new int[] {left, i + k}; 
        }
    }


### Solution 3
Key obseration is that three parts much have same number and pattern of 1s
except the leading part. My idea is to:

  1. count how many ones (if num%3!=0 return [-1,-1])
  2. search from right side to left, until we found num/3 1s. This index is not final answer, but it defines patten of 1s
  3. from feft, ignore leading 0s, and then match the pattern found in step 2, to get the first EndIndex
  4. do another matching to found second EndIndex

    
    
    public int[] threeEqualParts(int[] A) {
    		int numOne = 0;
    		for (int i: A){
    			if (i==1) numOne++;
    		}
            
            int[] noRes = {-1, -1};
            if (numOne == 0) return new int[]{0,2};
            if (numOne%3 != 0) return noRes;
            
            //find index of starting 1 of third string
            int idxThird=0;
    		int temp = 0;
            for (int i = A.length-1; i>=0; i--){
                if (A[i]==1){
                    temp++;
                    if (temp == numOne / 3){
                        idxThird = i;
                        break;
                    }
                } 
            }
            
            int res1 = findEndIdx(A, 0, idxThird);
            if (res1<0) return noRes;
            
            int res2 = findEndIdx(A, res1+1, idxThird);
            if (res2<0) return noRes;
            
            return new int[]{res1, res2+1};
        }
        
    	//right is the pattern to compare to.  
    	//return EndIdx of left pattern that matches right side.
        private int findEndIdx(int[] A, int left, int right){
            while (A[left]==0) left++;
            while (right < A.length){
                if (A[left]!=A[right]) return -1;
                left++;
                right++;            
            }
            return left-1;
        }
    



