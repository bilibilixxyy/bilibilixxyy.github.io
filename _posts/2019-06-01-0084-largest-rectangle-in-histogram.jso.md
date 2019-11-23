---
layout: post
title: 84. Largest Rectangle in Histogram
---
### Question
Given _n_ non-negative integers representing the histogram's bar height where
the width of each bar is 1, find the area of largest rectangle in the
histogram.



![](https://assets.leetcode.com/uploads/2018/10/12/histogram.png)  
Above is a histogram where width of each bar is 1, given height =
`[2,1,5,6,2,3]`.



![](https://assets.leetcode.com/uploads/2018/10/12/histogram_area.png)  
The largest rectangle is shown in the shaded area, which has area = `10` unit.



 **Example:**

    
    
     **Input:** [2,1,5,6,2,3]
    **Output:** 10
    

### Solution 1
For any bar `i` the maximum rectangle is of width `r - l - 1` where r - is the
last coordinate of the bar to the **right** with height `h[r] >= h[i]` and l -
is the last coordinate of the bar to the **left** which height `h[l] >= h[i]`

So if for any `i` coordinate we know his utmost higher (or of the same height)
neighbors to the right and to the left, we can easily find the largest
rectangle:

    
    
    int maxArea = 0;
    for (int i = 0; i < height.length; i++) {
        maxArea = Math.max(maxArea, height[i] * (lessFromRight[i] - lessFromLeft[i] - 1));
    }
    

The main trick is how to effectively calculate `lessFromRight` and
`lessFromLeft` arrays. The trivial solution is to use **O(n^2)** solution and
for each `i` element first find his left/right heighbour in the second inner
loop just iterating back or forward:

    
    
     for (int i = 1; i < height.length; i++) {              
        int p = i - 1;
        while (p >= 0 && height[p] >= height[i]) {
            p--;
        }
        lessFromLeft[i] = p;              
    }
    

The only line change shifts this algorithm from **O(n^2)** to **O(n)**
complexity: we don't need to rescan each item to the left - we can reuse
results of previous calculations and "jump" through indices in quick manner:

    
    
    while (p >=  0 && height[p] >= height[i]) {
          p = lessFromLeft[p];
    }
    

Here is the whole solution:

    
    
    public static int largestRectangleArea(int[] height) {
        if (height == null || height.length == 0) {
            return 0;
        }
        int[] lessFromLeft = new int[height.length]; // idx of the first bar the left that is lower than current
        int[] lessFromRight = new int[height.length]; // idx of the first bar the right that is lower than current
        lessFromRight[height.length - 1] = height.length;
        lessFromLeft[0] = -1;
    
        for (int i = 1; i < height.length; i++) {
            int p = i - 1;
    
            while (p >= 0 && height[p] >= height[i]) {
                p = lessFromLeft[p];
            }
            lessFromLeft[i] = p;
        }
    
        for (int i = height.length - 2; i >= 0; i--) {
            int p = i + 1;
    
            while (p < height.length && height[p] >= height[i]) {
                p = lessFromRight[p];
            }
            lessFromRight[i] = p;
        }
    
        int maxArea = 0;
        for (int i = 0; i < height.length; i++) {
            maxArea = Math.max(maxArea, height[i] * (lessFromRight[i] - lessFromLeft[i] - 1));
        }
    
        return maxArea;
    }


### Solution 2
For explanation, please see <http://www.geeksforgeeks.org/largest-rectangle-
under-histogram/>

    
    
    public class Solution {
        public int largestRectangleArea(int[] height) {
            int len = height.length;
            Stack<Integer> s = new Stack<Integer>();
            int maxArea = 0;
            for(int i = 0; i <= len; i++){
                int h = (i == len ? 0 : height[i]);
                if(s.isEmpty() || h >= height[s.peek()]){
                    s.push(i);
                }else{
                    int tp = s.pop();
                    maxArea = Math.max(maxArea, height[tp] * (s.isEmpty() ? i : i - 1 - s.peek()));
                    i--;
                }
            }
            return maxArea;
        }
    }
    

OP's Note: Two years later I need to interview again. I came to this problem
and I couldn't understand this solution. After reading the explanation through
the link above, I finally figured this out again.  
Two key points that I found helpful while understanding the solution:

  1. Do push all heights including 0 height.
  2. `i - 1 - s.peek()` uses the starting index where `height[s.peek() + 1] >= height[tp]`, because the index on top of the stack right now is the first index left of `tp` with height smaller than tp's height.


### Solution 3
I push a sentinel node back into the end of height to make the code logic more
concise.

    
    
      class Solution {
        public:
            int largestRectangleArea(vector<int> &height) {
                
                int ret = 0;
                height.push_back(0);
                vector<int> index;
                
                for(int i = 0; i < height.size(); i++)
                {
                    while(index.size() > 0 && height[index.back()] >= height[i])
                    {
                        int h = height[index.back()];
                        index.pop_back();
                        
                        int sidx = index.size() > 0 ? index.back() : -1;
                        if(h * (i-sidx-1) > ret)
                            ret = h * (i-sidx-1);
                    }
                    index.push_back(i);
                }
                
                return ret;
            }
        };



