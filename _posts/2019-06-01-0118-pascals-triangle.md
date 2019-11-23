---
layout: post
title: 118. Pascal's Triangle
---
### Question
Given a non-negative integer  _numRows_ , generate the first _numRows_ of
Pascal's triangle.

![](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)  
In Pascal's triangle, each number is the sum of the two numbers directly above
it.

 **Example:**

    
    
     **Input:** 5
    **Output:**
    [
         [1],
        [1,1],
       [1,2,1],
      [1,3,3,1],
     [1,4,6,4,1]
    ]
    

### Solution 1
    
    
    public class Solution {
    public List<List<Integer>> generate(int numRows)
    {
    	List<List<Integer>> allrows = new ArrayList<List<Integer>>();
    	ArrayList<Integer> row = new ArrayList<Integer>();
    	for(int i=0;i<numRows;i++)
    	{
    		row.add(0, 1);
    		for(int j=1;j<row.size()-1;j++)
    			row.set(j, row.get(j)+row.get(j+1));
    		allrows.add(new ArrayList<Integer>(row));
    	}
    	return allrows;
    	
    }
    

}


### Solution 2
    
    
    def generate(self, numRows):
            res = [[1]]
            for i in range(1, numRows):
                res += [map(lambda x, y: x+y, res[-1] + [0], [0] + res[-1])]
            return res[:numRows]
    

explanation: Any row can be constructed using the offset sum of the previous
row. Example:

    
    
        1 3 3 1 0 
     +  0 1 3 3 1
     =  1 4 6 4 1


### Solution 3
two loops, one go through the row, one go through the column

database: pretty straight forward, ArrayList

calculate element value: K(i)(j)=K(i-1)(j-1)+K(i-1)(j) except for the first
and last element

    
    
    public class Solution {
        public List<List<Integer>> generate(int numRows) {
            List<List<Integer>> triangle = new ArrayList<List<Integer>>();
            if (numRows <=0){
                return triangle;
            }
            for (int i=0; i<numRows; i++){
                List<Integer> row =  new ArrayList<Integer>();
                for (int j=0; j<i+1; j++){
                    if (j==0 || j==i){
                        row.add(1);
                    } else {
                        row.add(triangle.get(i-1).get(j-1)+triangle.get(i-1).get(j));
                    }
                }
                triangle.add(row);
            }
            return triangle;
        }
    }



