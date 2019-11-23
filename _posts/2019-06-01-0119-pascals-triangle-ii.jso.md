---
layout: post
title: 119. Pascal's Triangle II
---
### Question
Given a non-negative index _k_  where _k_ ≤  33, return the _k_ th index row
of the Pascal's triangle.

Note that the row index starts from 0.

![](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)  
In Pascal's triangle, each number is the sum of the two numbers directly above
it.

 **Example:**

    
    
     **Input:** 3
    **Output:** [1,3,3,1]
    

**Follow up:**

Could you optimize your algorithm to use only _O_ ( _k_ ) extra space?

### Solution 1
The basic idea is to iteratively update the array from the end to the
beginning.

    
    
    class Solution {
    public:
        vector<int> getRow(int rowIndex) {
            vector<int> A(rowIndex+1, 0);
            A[0] = 1;
            for(int i=1; i<rowIndex+1; i++)
                for(int j=i; j>=1; j--)
                    A[j] += A[j-1];
            return A;
        }
    };


### Solution 2
    
    
    class Solution(object):
        def getRow(self, rowIndex):
            """
            :type rowIndex: int
            :rtype: List[int]
            """
            row = [1]
            for _ in range(rowIndex):
                row = [x + y for x, y in zip([0]+row, row+[0])]
            return row
    

Updated according to @greg-irl 's suggestion below. It runs 30% faster than
using `map()`


### Solution 3
    
    
      public List<Integer> getRow(int rowIndex) {
    	List<Integer> list = new ArrayList<Integer>();
    	if (rowIndex < 0)
    		return list;
    
    	for (int i = 0; i < rowIndex + 1; i++) {
    		list.add(0, 1);
    		for (int j = 1; j < list.size() - 1; j++) {
    			list.set(j, list.get(j) + list.get(j + 1));
    		}
    	}
    	return list;
    }



