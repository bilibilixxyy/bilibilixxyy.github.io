---
layout: post
title: 1145. Number of Submatrices That Sum to Target
---
### Question
Given a `matrix`, and a `target`, return the number of non-empty submatrices
that sum to target.

A submatrix `x1, y1, x2, y2` is the set of all cells `matrix[x][y]` with `x1
<= x <= x2` and `y1 <= y <= y2`.

Two submatrices `(x1, y1, x2, y2)` and `(x1', y1', x2', y2')` are different if
they have some coordinate that is different: for example, if `x1 != x1'`.



 **Example 1:**

    
    
     **Input:** matrix = [[0,1,0],[1,1,1],[0,1,0]], target = 0
    **Output:** 4
    **Explanation:** The four 1x1 submatrices that only contain 0.
    

**Example 2:**

    
    
    **Input:** matrix = [[1,-1],[-1,1]], target = 0
    **Output:** 5
    **Explanation:** The two 1x2 submatrices, plus the two 2x1 submatrices, plus the 2x2 submatrix.
    



 ** **Note:****

  1. `1 <= matrix.length <= 300`
  2. `1 <= matrix[0].length <= 300`
  3. `-1000 <= matrix[i] <= 1000`
  4. `-10^8 <= target <= 10^8`

### Solution 1
[Screenshot](https://www.youtube.com/watch?v=fqoHZmRBpC8)  
Leetcode has internal error,  
so I cut the screenshot when I finish this one and didn't continue.

##  **Intuition**

For each row, calculate the prefix sum.  
For each pair of columns,  
calculate the accumulated sum of rows.  
Now this problem is same to, "Find the Subarray with Target Sum".  
  

##  **Complexity**

Time O(N^3), Space O(N)  
  

 **Java**

    
    
         public int numSubmatrixSumTarget(int[][] A, int target) {
            int m = A.length, n = A[0].length;
            for (int i = 0; i < m; i++)
                for (int j = 1; j < n; j++)
                    A[i][j] += A[i][j - 1];
            int res = 0;
            for (int i = 0; i < n; i++) {
                for (int j = i; j < n; j++) {
                    Map<Integer, Integer> counter = new HashMap<>();
                    counter.put(0, 1);
                    int cur = 0;
                    for (int k = 0; k < m; k++) {
                        cur += A[k][j] - (i > 0 ? A[k][i - 1] : 0);
                        res += counter.getOrDefault(cur - target, 0);
                        counter.put(cur, counter.getOrDefault(cur, 0) + 1);
                    }
                }
            }
            return res;
        }
    

**C++**

    
    
         int numSubmatrixSumTarget(vector<vector<int>>& A, int target) {
            int m = A.size(), n = A[0].size();
            for (int i = 0; i < m; i++)
                for (int j = 1; j < n; j++)
                    A[i][j] += A[i][j - 1];
            int res = 0;
            for (int i = 0; i < n; i++) {
                for (int j = i; j < n; j++) {
                    unordered_map<int, int> counter;
                    counter[0] = 1;
                    int cur = 0;
                    for (int k = 0; k < m; k++) {
                        cur += A[k][j] - (i > 0 ? A[k][i - 1] : 0);
                        res += counter[cur - target];
                        counter[cur]++;
                    }
                }
            }
            return res;
        }
    

**Python:**

    
    
            m, n = len(A), len(A[ 0])
            for row in A:
                for i in xrange(n - 1):
                    row[i + 1] += row[i]
            res = 0
            for i in xrange(n):
                for j in xrange(i, n):
                    c = collections.Counter({0: 1})
                    cur = 0
                    for k in xrange(m):
                        cur += A[k][j] - (A[k][i - 1] if i > 0 else 0)
                        res += c[cur - target]
                        c[cur] += 1
            return res
    


### Solution 2
**Explaination:**  
Check out how to solve for 1D array `560. Subarray Sum Equals K` in O(n) time.

The solution for 1D array uses running prefix sum.

  * We know that to get sum of a subarray `[j,i]` we can calculate using `SUM[0,i] - SUM [0,j-1]`.
  * If this calculation = target then we have a subarray who's sum equals target and ends at `i`.
  * Hence at any point `i` we need to find number of occurence of `runningSum - target` since `x + target = runningSum`.
  * We start moving from 1st element to the last and keep adding the value to running sum.
  * We also keep a hash map maintaining number of such sums occured.
  * Thus at any point `i` we have sums from [0,i) stored in hashmap where `i` ranges from `0` to `i` excluding `i`. eg: at `i=3`, we have `SUM[0,0`] , `SUM[0,1]` and `SUM[0,2]`.
  * At each index `i` we can then query the hashmap to find out number of occurences of `runningSum-target` to get number of subarrays ending at `i`.

Now since we know how to find number of subarrays who's sum equals target for
1D array.  
We can convert a 2D matrix of values to 1D matrix whose values equal to that
of 2D. Such that they have combination of all rows and apply the same
technique.  
eg: [[1,2,3,4,5],[2,6,7,8,9]]

We can break this down to 1 2x5 matrix and 2 1x5 matrix  
Thus the 1D value matrices are:

  * We start row wise
  * 1st 1x5 matrix is matrix[0] itself [1,2,3,4,5].
  * We then add second row to previous 1D matrix and get [3,8,10,12,14].
  * We then move on to make matrices with 2nd row of the original matrix as their first row.
  * Since 2nd row is the last we end up with last 1D matrix of [2,6,7,8,9].

We use the same technique for each 1D matrix created in above steps and keep
adding the result for these individual 1D arrays and return that as the result
in the end.

If you notice in the case of 1D array you get results for subarrays of all
lengths starting from any point.  
so you are checking rectangles of 1x1, 1x2, 1x3 ..... upto length.  
When you apply this to a 2D->1D array at each `i` you are checking rectangles
of all sizes starting from `i`.  
which looks like ( (1x1, 1x2 ...upto length of row then 2x1, 2x2 ...upto
length of a row) .... upto length of columns) for each row in original matrix
as their 1st row, hence the result includes all combinations of submatrices.

 **Note:** You can make this a little bit faster by sacrificing space and
precalculating all prefix sums in the 2D matrix and storing them as in `304.
Range Sum Query 2D - Immutable` so that we have a constant lookup for
calculating sums, this way we avoid recalculating sums of overlapping rows.

 **Solution:**

    
    
     class Solution {
    public:
        int result=0,target;
        void get_result(vector<int>& nums)                          //Get number of subarrays that sum to target.
        {
            int sum=0;
            unordered_map<int,int> map;
            map[0]++;
            for(int &i:nums)
            {
                sum+=i;
                result+=map[sum-target];       //get number of subarrays who's sum equals target and end at i and add result to global result.
                map[sum]++;                    //Add the occurence of running sum to map.
            }
        }
        int numSubmatrixSumTarget(vector<vector<int>>& matrix, int target) 
        {
            this->target=target;
            vector<int> row(matrix[0].size());
            for(int i=0;i<matrix.size();i++)                    //Convert 2D array to 1D by row.
            {
                fill(row.begin(),row.end(),0);                  //Clear vector to start the row with i as starting row.
                for(int j=i;j<matrix.size();j++)
                {
                    for(int x=0;x<matrix[0].size();x++)         //Add next row
                        row[x]+=matrix[j][x];
                    get_result(row);
                }
            }
            return result;
        }
    };
    


### Solution 3
  * Counting all sub-arrays with a given sum k for 1-Dimensional array can be used to reduce the time complexity.
  * We fix the left and right columns one by one and count sub-arrays for every left and right column pair.
  * The sum of elements in every row from left to right and store these sums in a temporary array say temp[].
  * So temp[i] indicates sum of elements from left to right in row i.
  * Count sub-arrays in temp[] having sum k.
  * Sum up all the counts for each temp[] with different left and right column pairs.

    
    
    public int numSubmatrixSumTarget(int[][] matrix, int target) {
    
            int m = matrix.length;
            int n = matrix[0].length;
    
            int[] temp = new int[m];
            int ans=0;
            for (int left = 0;left<n;left++) {
                Arrays.fill(temp,0);
                for (int right=left;right<n;right++) {
                    for (int i=0;i<m;i++) {
                        temp[i]+=matrix[i][right];
                    }
                    ans+= subCount(temp,target);
                }
            }
            return ans;
        }
    
        private int subCount(int[] temp,int target) {
            HashMap<Integer, Integer> prevSum = new HashMap<>();
    
            int res = 0;
            int currentSum = 0;
    
            for (int t : temp) {
    
                currentSum += t;
                if (currentSum == target) {
                    res++;
                }
                if (prevSum.containsKey(currentSum - target)) {
                    res += prevSum.get(currentSum - target);
                }
                prevSum.merge(currentSum, 1, (a, b) -> a + b);
            }
    
            return res;
        }
    



