---
layout: post
title: 1111. Minimum Score Triangulation of Polygon
---
### Question
Given `N`, consider a convex `N`-sided polygon with vertices labelled `A[0],
A[i], ..., A[N-1]` in clockwise order.

Suppose you triangulate the polygon into `N-2` triangles.  For each triangle,
the value of that triangle is the **product**  of the labels of the vertices,
and the _total score_ of the triangulation is the sum of these values over all
`N-2` triangles in the triangulation.

Return the smallest possible total score that you can achieve with some
triangulation of the polygon.



 **Example 1:**

    
    
     **Input:** [1,2,3]
    **Output:** 6
    **Explanation:** The polygon is already triangulated, and the score of the only triangle is 6.
    

**Example 2:**

![](https://assets.leetcode.com/uploads/2019/05/01/minimum-score-
triangulation-of-polygon-1.png)

    
    
    **Input:** [3,7,4,5]
    **Output:** 144
    **Explanation:** There are two triangulations, with possible scores: 3*7*5 + 4*5*7 = 245, or 3*4*5 + 3*4*7 = 144.  The minimum score is 144.
    

**Example 3:**

    
    
    **Input:** [1,3,1,4,1,5]
    **Output:** 13
    **Explanation:** The minimum score triangulation has score 1*1*3 + 1*1*4 + 1*1*5 + 1*1*1 = 13.
    



 **Note:**

  1. `3 <= A.length <= 50`
  2. `1 <= A[i] <= 100`

### Solution 1
# Intuition

If we pick a side of our polygon, it can form `n - 2` triangles. Each such
triangle forms 2 sub-polygons. We can analyze `n - 2` triangles, and get the
minimum score for sub-polygons using the recursion.  
![image](https://assets.leetcode.com/users/votrubac/image_1557470819.png)  
This is how this procedure looks for a sub-polygon (filled with diagonal
pattern above).

![image](https://assets.leetcode.com/users/votrubac/image_1557471328.png)

# Top-Down Solution

• Fix one side of the polygon i, j and move k within (i, j).  
• Calculate score of the i, k, j "orange" triangle.  
• Add the score of the "green" polygon i, k using recursion.  
• Add the score of the "blue" polygon k, j using recursion.  
• Use memoisation to remember minimum scores for each sub-polygons.

    
    
    int dp[50][50] = {};
    int minScoreTriangulation(vector<int>& A, int i = 0, int j = 0, int res = 0) {
      if (j == 0) j = A.size() - 1;
      if (dp[i][j] != 0) return dp[i][j];
      for (auto k = i + 1; k < j; ++k) {
        res = min(res == 0 ? INT_MAX : res, 
          minScoreTriangulation(A, i, k) + A[i] * A[k] * A[j] + minScoreTriangulation(A, k, j));
      }
      return dp[i][j] = res;
    }
    

# Bottom-Up Solution

At this point, it should not be hard to come up with a bottom-up solution. The
only trick here is to move `i` backward. This ensures "green" and "blue" sub-
polygons are processed before calculating min value for the entire polygon. It
happens this way naturally with the recursive solution.

    
    
    int minScoreTriangulation(vector<int>& A) {
      int dp[50][50] = {};
      for (int i = A.size() - 1; i >= 0; --i)
        for (int j = i + 1; j  < A.size();  ++j)
          for (auto k = i + 1; k < j; ++k)
            dp[i][j] = min(dp[i][j] == 0 ? INT_MAX : dp[i][j], dp[i][k] + A[i] * A[k] * A[j] + dp[k][j]);
      return dp[0][A.size() - 1];
    } 
    

# Complexity Analysis

Runtime: O(n ^ 3). This can be easily seen from the bottom-up solution.  
Memory: O(n ^ 2). We use a square matrix to store DP results.


### Solution 2
##  **Intuition**

The connected two points in polygon shares one common edge,  
these two points must be one and only one triangles.

  

##  **Explanation**

`dp[i][j]` means the minimum score to triangulate `A[i] ~ A[j]`,  
while there is edge connect `A[i]` and `A[j]`.

We enumerate all points `A[k]` with `i < k < j` to form a triangle.

The score of this triangulation is `dp[i][j], dp[i][k] + dp[k][j] + A[i] *
A[j] * A[k]`

  

 **Java, bottom-up:**

    
    
        public int minScoreTriangulation(int[] A) {
            int n = A. length;
            int[][] dp = new int[n][n];
            for (int d = 2; d < n; ++d) {
                for (int i = 0; i + d < n; ++i) {
                    int j = i + d;
                    dp[i][j] = Integer.MAX_VALUE;
                    for (int k = i + 1; k < j; ++k)
                        dp[i][j] = Math.min(dp[i][j], dp[i][k] + dp[k][j] + A[i] * A[j] * A[k]);
                }
            }
            return dp[0][n - 1];
        }
    

**Java, bottom-up, another loop version**

    
    
        public int minScoreTriangulation(int[] A) {
            int n = A. length;
            int[][] dp = new int[n][n];
            for (int j = 2; j < n; ++j) {
                for (int i = j - 2; i >= 0; --i) {
                    dp[i][j] = Integer.MAX_VALUE;
                    for (int k = i + 1; k < j; ++k)
                        dp[i][j] = Math.min(dp[i][j], dp[i][k] + dp[k][j] + A[i] * A[j] * A[k]);
                }
            }
            return dp[0][n - 1];
        }
    

**C++, bottom-up:**

    
    
        int minScoreTriangulation(vector<int>& A) {
            int n = A. size();
            vector<vector<int>> dp(n, vector<int>(n));
            for (int j = 2; j < n; ++j) {
                for (int i = j - 2; i >= 0; --i) {
                    dp[i][j] = INT_MAX;
                    for (int k = i + 1; k < j; ++k)
                        dp[i][j] = min(dp[i][j], dp[i][k] + dp[k][j] + A[i] * A[j] * A[k]);
                }
            }
            return dp[0][n - 1];
        }
    

**Python, bottom-up**

    
    
        def minScoreTriangulation(self, A):
            n = len(A)
            dp = [[ 0] * n for i in xrange(n)]
            for d in xrange(2, n):
                for i in xrange(n - d):
                    j = i + d
                    dp[i][j] = min(dp[i][k] + dp[k][j] + A[i] * A[j] * A[k] for k in xrange(i + 1, j))
            return dp[0][n - 1]
    

**Python, top-down:**

    
    
        def minScoreTriangulation(self, A):
            memo = {}
            def dp( i, j):
                if (i, j) not in memo:
                    memo[i, j] = min([dp(i, k) + dp(k, j) + A[i] * A[j] * A[k] for k in range(i + 1, j)] or [0])
                return memo[i, j]
            return dp(0, len(A) - 1)
    


### Solution 3
First, I feel like this was a hard problem. This problem is very similar to
Burst Ballons (<https://leetcode.com/problems/burst-balloons/>), which is
labeled as a hard. They are almost the exact same problem, so I don't
understand why this is medium. Now, moving on to the explanation.

The problem asks what is the minimum amount we can get from triangulating.
Once you pick your 3 points, you'll need to throw out the middle point (the
one between 2 other values in your array) because you have sliced the triangle
off the polygon. Once we have triangulated the triangle, the midpoint
dissapears from our array. If you need to imagine what this looks like in your
head, imagine a triangle being sliced off the polygon with each iteration.
This post has a great picture of what the slicing would look like.
<https://leetcode.com/problems/minimum-score-triangulation-of-
polygon/discuss/286754/C%2B%2BPython-O(n3)-DP-explanation-%2B-diagrams>.

For a beginner, it is NOT a good idea to start off by thinking how to solve
the problem just by using dynamic programming. You're going to get very
confused. You're going to see a few dynamic programming questions and somehow
memorize the tables invovled, then not understand how to solve another one.
You're going to think you can solve a DP problem, but your brain is tricking
yourself. Trust me, after I watched Tushar Roy's videos, I thought I could
solve DP problems, but all I was doing was subconciously memorizing solutions
and not understanding them.

Here are the steps you should consider BEFORE coming up with the dynamic
programming solution:  
 **1\. Recursive solution**  
 **2\. Memoize the recursive solution (top-down)**  
 **3\. Subproblem Order for DP (bottom-up)**

There are debates on whether recursion or DP is better, and it might be good
for you to mention that you know this in an interview.

Let's say you have an array or a bunch of data.

In problems where the base case is an empty string once you have processed
everything in your array, dynamic programming is the best solution because you
need to go all the way to an empty string in order to get an answer. If you
have a question where the base case is when your array is 9/10 full and you
don't need to know the answer to subproblems (ie you never need to go all the
way down to an empty string), a recursive memoized solution might be faster.
However, most of the problems I've seen on leetcode you need to go all the way
down to an empty string or very close. In this case you can only have 3 points
left, which means you need to go all the way down.

 **Step 1: Recursion**

Here is our recursive solution. Notice that we have defined our "subproblems"
to be the length of array. For each recursive call, we shorten our array by 1
value. This is the value we have picked to triangulate. Below is our recursive
function. For fun, I submitted it and of course got TLE. It failed on a test
case of length 20. Pretty bad right? It is because we're solving the same
subproblems over and over.

Note our base case is when we have an array of length 2. This is because we
need at least 3 points to triangulate in order to get points. We set a
variable `minnum` to float("Inf") as a pseudo-max. The `left` and `right`
variables are the two other corners of the triangle. `k` is the other corner.
I write `left` and `right` because it is left and right of k in our array.
Note that dfs(left,k) and dfs(right,k) are the points you get for the
triangluation BETWEEN, ie NOT INCLUDING, left, k and right. This means it
defines points you get from left+1 -> k-1 and k+1 -> right -1.

This is what each section of points should be imagined as:  
 **[left]** [left +1 - > k - 1] **[K]** [k + 1 - > right - 1] **[right]**

You are choosing points K, left, and right as the corners of your triangle. So
you need to know how many points you got from [left +1 -> k - 1] and [k + 1 ->
right - 1]. Since you have recursed on all of [left +1 -> k - 1] and [k + 1 ->
right - 1] to find the minimum number of points you can get, you know this.

Do you see how you are doing the same subproblem over and over and what it is?
It's the length of the subarray. In order to do this via dynamic programming,
we need to first start with an array of length 1, find it's minimum points
(only 1 answer there) and then keep building with arrays of size 2, 3, ...
n-1.

    
    
    class Solution(object):
        def minScoreTriangulation(self, A):
    		def dfs(left, right):
                if right - left + 1 < 3:
                    return 0
                minnum = float("Inf")
                for k in range(left+1, right):
                    minnum = min(minnum, A[left]*A[right]*A[k] + dfs(left, k) + dfs(k, right))
                return minnum
            return dfs(0, len(A) - 1)
    

**Step 2: Memoization**

Python 3 has an lru cache, which is pretty useful, so that's already memoized.
I'll have to add an actual memo 2D array later if this is not ok.

    
    
    from functools import lru_cache
    class Solution(object):
        def minScoreTriangulation(self, A):
    	@lru_cache(None)
    		def dfs(left, right):
                if right - left + 1 < 3:
                    return 0
                minnum = float("Inf")
                for k in range(left+1, right):
                    minnum = min(minnum, A[left]*A[right]*A[k] + dfs(left, k) + dfs(k, right))
                return minnum
            return dfs(0, len(A) - 1)
    

**Step 3: Dynamic Programming**

Here is where we get to dynamic programming. Do I think in an interview they
might be ok with you jumping directly to DP if you know what you're doing and
can solve the problem that way? Sure, but you might get asked a random
question you've never seen before so it's a good idea to do these steps.

So as we discussed above in the recursive section, the 1st subproblem we're
solving is if our array is only length 3. This means we only have one triangle
left to triangulate, so that's pretty easy right? If you are thinking about it
logically, we start from the end of the game, where you have only 3 points
left. This is the base case in our recursive function (well in our recursive
function, it's if our array has less than 3 points, but we could rewrite it to
return the product of the 3 last elements in our array).

This means that our dp table will be filled up in order of LENGTH of
subproblems. We will first go over all the subproblems of an array of length
3. Once we know all that, we can do subproblems with arrays of length 4, and
so on until we get our answer. Our answer should be dp[0][-1] because we want
to know the minimum amount of points we can score if we use EVERYTHING in the
array.

In case you are confused about the order of things getting filled out in the
array: you can see that we fill out the array diagonally. I have numbered the
order we fill out things in our dp array from 1 -> 10\. This is what the order
would look like for the [1,3,1,4,1,5] test case. You'll notice in each dp
array that we don't fill out the 1st 2 columns. This is because there will
always be at least 3 points left, so we start in col 3.

[0, 0, **1, 5, 8,10]**  
[0, 0, 0, **2, 6, 9]**  
[0, 0, 0, 0, **3, 7]**  
[0, 0, 0, 0, 0, **4]**  
[0, 0, 0, 0, 0, 0]  
[0, 0, 0, 0, 0, 0]

Below is the code. Our first for loop `for l in xrange(2, n):` is written so
we can solve the smallest subproblems first. We start from 2 because our
subproblem must be at LEAST length 3 so we have 3 points in our triangle. Our
2nd for loop is a corner we use and call left. We then create our right corner
by adding the length of our subproblem to left. Our k is the middle point in
our triangle and must fall between left and right (so for left we do left + 1,
remember in python that ranges do not include the last value, so we only go up
to right - 1 for k).

You can make your dp table n*(n-2) length, but that's going to get confusing
on indexes during your interview probably. It's going to require you check if
you are out of bounds. and random -2 in certain indexes.

We set dp[left][right] to float("Inf") as a pseudomin since every value must
be smaller.

    
    
    class Solution(object):
        def minScoreTriangulation(self, A):
            """
            :type A: List[int]
            :rtype: int
            """
            n = len(A)
            dp = [[0]*n for i in xrange(n)]
            for l in xrange(2, n):
                for left in xrange(0, n - l):
                    right = left + l
                    dp[left][right] = float("Inf")
                    for k in xrange(left + 1, right):
                        dp[left][right] = min(dp[left][right], dp[left][k] + dp[k][right] + A[left]*A[right]*A[k])
            return dp[0][-1]
    

Complexity: Runtime: O(N^3), Space: O(N^2)

Last words: I wrote this all out so I could be sure I understood it in case it
comes up in an interview. Do I think I could have gotten the DP in 45 minutes
for an interview? Before this problem: absolutely not. After it: I still don't
know. Honestly not sure. Hoping that the more I practice, the more I'll be
able to see this faster. The recursive solution was reasonably simply, but
that's brute force and got TLE with only 20 elements! I got confused on the 3
for loops in my DP solution when I was writing it. I had to print out the dp
array to see how it was getting filled out and tried a bunch of different
loops in order to get it correct. I should also note it took a few days of
passive thinking on it (like when I'm in the subway listening to music) for
all 3 steps (recursion, memoization, DP) to finally click, so if you read this
and don't understand it, just come back to it another day. Maybe your brain
needs to sleep on it like mine did.



