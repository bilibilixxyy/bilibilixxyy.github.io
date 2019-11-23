---
layout: post
title: 891. Score After Flipping Matrix
---
### Question
We have a two dimensional matrix `A` where each value is `0` or `1`.

A move consists of choosing any row or column, and toggling each value in that
row or column: changing all `0`s to `1`s, and all `1`s to `0`s.

After making any number of moves, every row of this matrix is interpreted as a
binary number, and the score of the matrix is the sum of these numbers.

Return the highest possible score.



 **Example 1:**

    
    
     **Input:** [[0,0,1,1],[1,0,1,0],[1,1,0,0]]
    **Output:** 39
    **Explanation:** Toggled to [[1,1,1,1],[1,0,0,1],[1,1,1,1]].
    0b1111 + 0b1001 + 0b1111 = 15 + 9 + 15 = 39



 **Note:**

  1. `1 <= A.length <= 20`
  2. `1 <= A[0].length <= 20`
  3. `A[i][j]` is `0` or `1`.

### Solution 1
Assume `A` is `M * N`.

  1. `A[i][0]` is worth `1 << (N - 1)` points, more than the sum of (`A[i][1] + .. + A[i][N-1]`).  
We need to toggle all `A[i][0]` to `1`, here I toggle all lines for `A[i][0] =
0`.

  2. `A[i][j]` is worth `1 << (N - 1 - j)`  
For every col, I count the current number of `1`s.  
After step 1, `A[i][j]` becomes `1` if `A[i][j] == A[i][0]`.  
if `M - cur > cur`, we can toggle this column to get more `1`s.  
`max(M, M - cur)` will be the maximum number of `1`s that we can get.

 **Time Complexity** :  
O(MN)

 **C++:**

    
    
         int matrixScore(vector<vector<int>> A) {
            int M = A.size(), N = A[0].size(), res = (1 << (N - 1)) * M;
            for (int j = 1; j < N; j++) {
                int cur = 0;
                for (int i = 0; i < M; i++) cur += A[i][j] == A[i][0];
                res += max(cur, M - cur) * (1 << (N - j - 1));
            }
            return res;
        }
    

**Java:**

    
    
     class Solution {
        public int matrixScore(int[][] A) {
            int M = A.length, N = A[0].length, res = (1 << (N - 1)) * M;
            for (int j = 1; j < N; j++) {
                int cur = 0;
                for (int i = 0; i < M; i++) cur += A[i][j] == A[i][0] ? 1 : 0;
                res += Math.max(cur, M - cur) * (1 << (N - j - 1));
            }
            return res;
        }
    }
    

**Python:**

    
    
        def matrixScore(self, A):
            M, N = len(A), len(A[ 0])
            res = (1 << N - 1) * M
            for j in range(1, N):
                cur = sum(A[i][j] == A[i][0] for i in range(M))
                res += max(cur, M - cur) * (1 << N - 1 - j)
            return res
    


### Solution 2
First of all, thanks to @lee215 for providing an optimized code. My optimized
code is an exact replica of his solution. But, I would like to offer more
explanation to people who might find it difficult to understand. Hence, in
this post, I start from the thinking process, implementing the thought-process
to an un-optimized code and then improving it.

First of all, after looking at this question, the first thing which comes in
mind is simulation. But wait, the question doesn't state any restrictions,
because you can toggle as many rows and columns as you want and however times
as you want. Then it means, its practically impossible to simulate all
situations. So, what do we do next. Lets understand the structure of the
binary number.  
A binary number, for example is something like

    
    
    01010
    

Now, think for a moment, how can we maximize it? You guessed it right
probably, by maximizing the number of 1's, which are as left as possible. Now,
see below example,

    
    
    0111 and 1000
    

The first number is 7 and the second is 8. So, overall we decreased the number
of 1's, but the value has increased as we tried to get the 1 as left as
possible. Now you got it? Exactly, if the first bit is zero, flip the binary.
So, for our problem, we use this property (call it property 1).First thing
which we will do is try to flip all rows, whose first column is zero.  
Great, now lets look at another optimization. For a particular column, it
would be great if we could maximize the number of 1's. Why? Because, if you
think carefully, all are at same bit position. Hence, if the number of1's
increase, the total sum would increase.  
Hence, we use this second property(call it property 2). We scan through each
column, and check if the number of 1's in that column are less than or equal
to half the number of rows. If yes, then we flip the column to maximize the
number of 1's in that column. However, be careful to do this for all columns
except first, else you would break the first property.  
That's it. Now, I hope you would easily understand the below unoptimized code
which is written in `C++.`

    
    
    class Solution {
    public:
        void flipRow(vector<vector<int>>& A,int row,int n){
            for(int j = 0;j < n;j++){
                if(A[row][j] == 0)
                    A[row][j] = 1;
                else
                    A[row][j] = 0;
            }
        }
        
        void flipCol(vector<vector<int>>& A,int col,int m){
            for(int i = 0;i < m;i++){
                if(A[i][col] == 0)
                    A[i][col] = 1;
                else
                    A[i][col] = 0;
            }
        }
        
        
        int matrixScore(vector<vector<int>>& A) {
            int m = A.size();
            int n = A[0].size();
            vector<int> col(n,0);
            
            for(int i = 0;i < m;i++){
                if(A[i][0] == 0)
                    flipRow(A,i,n);
                for(int j = 0;j < n;j++){
                    if(A[i][j] == 1)
                        col[j]++;
                }
            }
            
            for(int j = 0;j < n;j++){
                if(col[j] <= m/2)
                    flipCol(A,j,m);
            }
            
            int result = 0,sum;
            for(vector<int> v : A){
                sum = 0;
                for(int j = v.size()-1;j >= 0 ;j--){
                    if(v[j] == 1)
                        sum += pow(2,v.size()-1-j);
                }
                result += sum;
            }
            
            return result;
        }
    };
    

But, if you carefully observe, we are not required to manipulate the matrix.
By this, we can avoid unnecessary calculations.

Now, see the below `Java` code. I will explain it later.

    
    
    class Solution {
        public int matrixScore(int[][] A) {
            int m = A.length;
            int n = A[0].length;
            int result = 0;
            
            result += (1 << (n-1))*m;
            
            for(int j = 1;j < n;j++){
                int same = 0;
                for(int i = 0;i < m;i++){
                    if(A[i][0] == A[i][j])
                        same++;
                }
                result += (1 << (n-1-j))*Math.max(same,m-same);
            }
            
            return result;
        }
    }
    

Firstly, we are sure to set all the first bits of all binary numbers as 1. So,
first lets calculate the value for them. This is what is done in

    
    
    result += (1 << (n-1))*m;
    

Next, we start from first column and check the number of values which are
same(0 or 1) for each row, with the first column element. Why? Because if they
are both 0, then would be naturally flipped to 1(from property 1). If they are
1, then the row would naturally wouldn't be flipped(again from property 1).
Hence, intuitively, you can consider the variable `same` to store the maximum
number of 1's we have currently.  
Now, as said before, we need to maximize `same`. intuitively, if `same` is the
number of 1's, `m-same` is the number of 0's. We take the maximum of two to
maximize the number of 1's, which will done by either keeping the column
unflipped(if currently, number of 1's are more than number of 0's) or by
flipping it as required.  
Finally, we add the value for those bit positions for the 1's in this line  
`result += (1 << (n-1-j))*Math.max(same,m-same);`  
and finally return the result.

I hope you understood the solution now.  
Please let me know if I am wrong somewhere or if its unclear.  
Cheers!!


### Solution 3
Optimize the total sum in two steps:

  1. Flip all rows that start with zero;
  2. Flip all columns where the number of zeros is larger than the number of ones;

    
    
    class Solution {
        public int matrixScore(int[][] A) {
            int N = A.length,
                M = A[0].length;
            
            // Optimize, step1: flip all rows starting with a zero
            for (int i = 0; i < N; ++i) {
                if (A[i][0] == 0) 
                    flipRow(A, i);
            }
            
            // Optimize, step 2: flip all columns where the number of zeros is larger than the number of ones
            for (int col = 1; col < M; ++col) {
                int sumCol = 0;
                for (int i = 0; i < N; ++i) 
                    sumCol += A[i][col];
                
                if (sumCol * 2 < N) 
                    flipCol(A, col);
            }
            
            // Count final sum
            int total = 0;
            for (int i = 0; i < N; ++i) 
                for (int j = 0; j < M; ++j)
                    total += A[i][j] * (1 << (M-j-1));
                
            return total;
        }
        
        void flipRow(int[][] a, int r) {
            for (int i = 0; i < a[r].length; ++i)
                a[r][i] = (a[r][i] ^ 1); 
        }
        
        void flipCol(int[][] a, int c) {
            for (int i = 0; i < a.length; ++i)
                a[i][c] = (a[i][c] ^ 1); 
        }
    }
    

### Intuition

In binary representation, the following holds true:

  * 100 > 011
  * 1000 > 0111
  * 10000 > 01111
  * etc.

That is, the first bit gives us more upside than all the rest bits combined.
Thus, first and foremost, we flip all rows that have the largest bit equal to
zero (step 1).  
Afterwards, we try to maximize what's left. We no longer want to flip rows, as
this would turn our largest and most valuable bits to zero. Instead, we go
over each column and see if it makes sense to flip it. Whenever the number of
zeros in a column is more than the number of ones, it makes sense to flip it.
That's what we do in step 2.

Basically, it's just a greedy approach.



