---
layout: post
title: 866. Rectangle Overlap
---
### Question
A rectangle is represented as a list `[x1, y1, x2, y2]`, where `(x1, y1)` are
the coordinates of its bottom-left corner, and `(x2, y2)` are the coordinates
of its top-right corner.

Two rectangles overlap if the area of their intersection is positive.  To be
clear, two rectangles that only touch at the corner or edges do not overlap.

Given two (axis-aligned) rectangles, return whether they overlap.

 **Example 1:**

    
    
     **Input:** rec1 = [0,0,2,2], rec2 = [1,1,3,3]
    **Output:** true
    

**Example 2:**

    
    
    **Input:** rec1 = [0,0,1,1], rec2 = [1,0,2,1]
    **Output:** false
    

**Notes:**

  1. Both rectangles `rec1` and `rec2` are lists of 4 integers.
  2. All coordinates in rectangles will be between `-10^9 `and` 10^9`.

### Solution 1
 **Intuition** :  
Before we do it in 2D plane, let's try it in 1D.  
Given 2 segment `(left1, right1)`, `(left2, right2)`, how can we check whether
they overlap?  
If these two intervals overlap, it should exist an number `x`,

`left1 < x < right1 && left2 < x < right2`

`left1 < x < right2 && left2 < x < right1`

`left1 < right2 && left2 < right1`

This is the sufficient and necessary condition for two segments overlap.

 **Explanation** :  
For 2D, if two rectancle overlap both on `x` and `y`, they overlap in the
plane.

 **Time Complexity** :  
O(1)

 **C++/Java:**

    
    
     return rec1[0] < rec2[2] && rec2[0] < rec1[2] && rec1[1] < rec2[3] && rec2[1] < rec1[3];
    

**Python:**

    
    
     return rec1[0] < rec2[2] and rec2[0] < rec1[2] and rec1[1] < rec2[3] and rec2[1] < rec1[3]
    


### Solution 2
    
    
         Consider a 1D overlap (Interval problem).
         For an overlap to occur necessary condition is
         
                 x3,y2          x4,y2
                   |--------------|
         |--------------|
         x1,y1         x2,y1
                  
         x1 < x3 < x2  && x3 < x2 < x4 
         
         Or simplified - x1 < x4 && x3 < x2
         For 2D case use 1D conditions for both X and Y axes
         
         Case-1: Rec2 intersects with Rec1 on top right corner
    
                   ____________________x4,y4
                  |                   |
           _______|______x2,y2        |
          |       |______|____________|
          |      x3,y3   |
          |______________|
         x1,y1
                  
         Case-2: Rec2 intersects with Rec1 on top left corner
           ___________________  x4,y4
          |                   |
          |            _______|____________x2,y2
          |___________|_______|           |
        x3,y3         |                   | 
                      |___________________|
                    x1,y1
    
         Case-3: Rec2 intersects with Rec1 on bottom left corner
         
                   ____________________x2,y2
                  |                   |
           _______|______x4,y4        |
          |       |______|____________|
          |      x1,y1   |
          |______________|
         x3,y3
                  
         Case-4: Rec2 intersects with Rec1 on bottom right corner
         
           ___________________  x2,y2
          |                   |
          |            _______|____________x4,y4
          |___________|_______|           |
        x1,y1         |                   | 
                      |___________________|
                    x3,y3
        
        bool case1 = (x1 < x4 && x3 < x2 && y1 < y4 && y3 < y2); //top right intersection
        bool case2 = (x3 < x2 && x1 < x4 && y1 < y4 && y3 < y2); //top left intersection
        bool case3 = (x3 < x2 && x1 < x4 && y3 < y2 && y4 < y1); //bottom left intersection
        bool case4 = (x1 < x4 && x3 < x2 && y3 < y2 && y4 < y1); //bottom right intersection
        
        if you look carefully in all cases we have same 4 comparisons
    
    
    
    bool isRectangleOverlap(vector<int>& rec1, vector<int>& rec2) {
    	int x1 = rec1[0], y1 = rec1[1], x2 = rec1[2], y2 = rec1[3];
    	int x3 = rec2[0], y3 = rec2[1], x4 = rec2[2], y4 = rec2[3];
    	return (x1 < x4 && x3 < x2 && y1 < y4 && y3 < y2);
    }
    


### Solution 3
De Morgan's Law tells us that the following are equivalent. An easy way to
remember De Morgan's is by the saying to split the bar and change the
operation; that is to distribute negation and change the boolean operator in
this example.

    
    
        public boolean isRectangleOverlap(int[] rec1, int[] rec2) {
            return !(rec1[2] <= rec2[0] ||   // left
                     rec1[3] <= rec2[1] ||   // bottom
                     rec1[0] >= rec2[2] ||   // right
                     rec1[1] >= rec2[3]);    // top
        }
    
    
    
        public boolean isRectangleOverlap(int[] rec1, int[] rec2) {
            return   rec1[2] > rec2[0] &&   // left
                     rec1[3] > rec2[1] &&   // bottom
                     rec1[0] < rec2[2] &&   // right
                     rec1[1] < rec2[3];     // top
        }
    



