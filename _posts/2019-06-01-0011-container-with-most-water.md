---
layout: post
title: 11. Container With Most Water
---
### Question
Given _n_ non-negative integers _a 1_, _a 2_, ..., _a n _, where each
represents a point at coordinate ( _i_ , _a i_). _n_ vertical lines are drawn
such that the two endpoints of line _i_ is at ( _i_ , _a i_) and ( _i_ , 0).
Find two lines, which together with x-axis forms a container, such that the
container contains the most water.

 **Note:  **You may not slant the container and _n_ is at least 2.



![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg)

The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this
case, the max area of water (blue section) the container can contain is 49.



 **Example:**

    
    
     **Input:** [1,8,6,2,5,4,8,3,7]
    **Output:** 49

### Solution 1
The O(n) solution with proof by contradiction doesn't look intuitive enough to
me. Before moving on, read any
[example](https://leetcode.com/problems/container-with-most-
water/discuss/6100/Simple-and-clear-proofexplanation) of the algorithm first
if you don't know it yet.

Here's another way to see what happens in a matrix representation:

Draw a matrix where the row is the first line, and the column is the second
line. For example, say `n=6`.

In the figures below, `x` means we don't need to compute the volume for that
case: (1) On the diagonal, the two lines are overlapped; (2) The lower left
triangle area of the matrix is symmetric to the upper right area.

We start by computing the volume at `(1,6)`, denoted by `o`. Now if the left
line is shorter than the right line, then all the elements left to `(1,6)` on
the first row have smaller volume, so we don't need to compute those cases
(crossed by `---`).

    
    
      1 2 3 4 5 6
    1 x ------- o
    2 x x
    3 x x x 
    4 x x x x
    5 x x x x x
    6 x x x x x x
    

Next we move the left line and compute `(2,6)`. Now if the right line is
shorter, all cases below `(2,6)` are eliminated.

    
    
      1 2 3 4 5 6
    1 x ------- o
    2 x x       o
    3 x x x     |
    4 x x x x   |
    5 x x x x x |
    6 x x x x x x
    

And no matter how this `o` path goes, we end up only need to find the max
value on this path, which contains `n-1` cases.

    
    
      1 2 3 4 5 6
    1 x ------- o
    2 x x - o o o
    3 x x x o | |
    4 x x x x | |
    5 x x x x x |
    6 x x x x x x
    

Hope this helps. I feel more comfortable seeing things this way.


### Solution 2
Start by evaluating the widest container, using the first and the last line.
All other possible containers are less wide, so to hold more water, they need
to be higher. Thus, after evaluating that widest container, skip lines at both
ends that don't support a higher height. Then evaluate that new container we
arrived at. Repeat until there are no more possible containers left.

 **C++**

    
    
    int maxArea(vector<int>& height) {
        int water =  0;
        int i = 0, j = height.size() - 1;
        while (i < j) {
            int h = min(height[i], height[j]);
            water = max(water, (j - i) * h);
            while (height[i] <= h && i < j) i++;
            while (height[j] <= h && i < j) j--;
        }
        return water;
    }
    

**C**

A bit shorter and perhaps faster because I can use raw int pointers, but a bit
longer because I don't have `min` and `max`.

    
    
    int maxArea(int* heights, int n) {
        int water = 0, *i = heights, *j = i + n - 1;
        while (i < j) {
            int h = *i < *j ? *i : *j;
            int w = (j - i) * h;
            if (w > water) water = w;
            while (*i <= h && i < j) i++;
            while (*j <= h && i < j) j--;
        }
        return water;
    }


### Solution 3
I've seen some "proofs" for the common O(n) solution, but I found them very
confusing and lacking. Some even didn't explain anything but just used lots of
variables and equations and were like "Tada! See?". I think mine makes more
sense:

 **Idea / Proof:**

  1. The widest container (using first and last line) is a good candidate, because of its width. Its water level is the height of the smaller one of first and last line.
  2. All other containers are less wide and thus would need a higher water level in order to hold more water.
  3. The smaller one of first and last line doesn't support a higher water level and can thus be safely removed from further consideration.

 **Implementation:** (Python)

    
    
    class Solution:
        def maxArea(self, height):
             i, j = 0, len(height) - 1
            water = 0
            while i < j:
                water = max(water, (j - i) * min(height[i], height[j]))
                if height[i] < height[j]:
                    i += 1
                else:
                    j -= 1
            return water
    

**Further explanation:**

Variables `i` and `j` define the container under consideration. We initialize
them to first and last line, meaning the widest container. Variable `water`
will keep track of the highest amount of water we managed so far. We compute
`j - i`, the width of the current container, and `min(height[i], height[j])`,
the water level that this container can support. Multiply them to get how much
water this container can hold, and update `water` accordingly. Next remove the
smaller one of the two lines from consideration, as justified above in "Idea /
Proof". Continue until there is nothing left to consider, then return the
result.



