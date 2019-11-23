---
layout: post
title: 830. Largest Triangle Area
---
### Question
You have a list of points in the plane. Return the area of the largest
triangle that can be formed by any 3 of the points.

    
    
     **Example:**
    **Input:** points = [[0,0],[0,1],[1,0],[0,2],[2,0]]
    **Output:** 2
    **Explanation:** 
    The five points are show in the figure below. The red triangle is the largest.
    

![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/04/04/1027.png)

**Notes:**

  * `3 <= points.length <= 50`.
  * No points will be duplicated.
  *  `-50 <= points[i][j] <= 50`.
  * Answers within `10^-6` of the true value will be accepted as correct.

### Solution 1
 **Explanaiton**  
Burete force loop on all combinations of three points and calculate the area
of these three points.  
If you google "three pointes triangle area formula", you can find the answer
with the first result in second.

 **Time complexity**  
`O(N^3)` solution, but `N <= 50`, so it's fast enough.  
You may find convex hull first as @weidairpi replies. It help improve to O(M^3
+ NlogN) in the best case where M is the number of points on the hull.  
But it make this easy problem complex and it stays same complexity in the
worst case.

 **Prove 1**  
Well, someone complains the situation without any formula.  
In fact the formula is not that difficult to find out.

For this case:  
![image](https://s3-lc-
upload.s3.amazonaws.com/users/lee215/image_1523209147.png)

We can calculate the area as follow:  
![image](https://s3-lc-
upload.s3.amazonaws.com/users/lee215/image_1523379988.png)

In the result A,B,C are symmetrical, so it won't matter what order we name it.  
In this case, we calculate the total area by addition three triangle.  
In the other cases, you may need to use substraction and it's quite the same
process.

 **Prove 2**  
If you are familar with vector product. The result is quite obvious.  
![image](https://s3-lc-
upload.s3.amazonaws.com/users/lee215/image_1523380000.png)

 **C++:**

    
    
         double largestTriangleArea(vector<vector<int>>& p) {
            double res = 0;
            for (auto &i : p)
                for (auto &j : p)
                    for (auto &k : p)
                res = max(res, 0.5 * abs(i[0] * j[1] + j[0] * k[1] + k[0] * i[1]- j[0] * i[1] - k[0] * j[1] - i[0] * k[1]));
            return res;
        }
    

**Java:**

    
    
         public double largestTriangleArea(int[][] p) {
            double res = 0;
            for (int[] i: p)
                for (int[] j: p)
                    for (int[] k: p)
                res = Math.max(res, 0.5 * Math.abs(i[0] * j[1] + j[0] * k[1] + k[0] * i[1]- j[0] * i[1] - k[0] * j[1] - i[0] * k[1]));
            return res;
        }
    

**1-line Python**

    
    
    def largestTriangleArea(self, p):
             return max(0.5 * abs(i[0] * j[1] + j[0] * k[1] + k[0] * i[1]- j[0] * i[1] - k[0] * j[1] - i[0] * k[1])
                for i, j, k in itertools.combinations(p, 3))
    


### Solution 2
This is a bad problem. I don't think any major company will ask this. It's
labeled easy, because the solution if brute force. When you are going to brute
force then any problem is easy and unrepresentative of one's algorithmic
thinking and programming skills.

I know there are more optimal solutions, but as I understand they are quite
complex for 'easy' problem and are more like 'tricks' instead of a pure
algorithms. Don't waste your time on finding a better solution like I did.


### Solution 3
How to pick all combinaitons from an array that consist of 3 points -  
<https://www.geeksforgeeks.org/print-all-possible-combinations-of-r-elements-
in-a-given-array-of-size-n/>

How to calculate the triangle area knowing the coordinates of the 3 points?  
<https://www.mathopenref.com/coordtrianglearea.html>

    
    
    class Solution {
        public double largestTriangleArea(int[][] points) {
            double max = 0.0; 
            for (int i = 0; i < points.length - 2; i++) 
                for (int j = i + 1; j < points.length - 1; j++) 
                    for (int k = j + 1; k < points.length; k++) 
                        max = Math.max(max, areaCal(points[i], points[j], points[k])); 
            return max; 
        }
        
        public double areaCal(int[] pt1, int[] pt2, int[] pt3) {
            return Math.abs(pt1[0] * (pt2[1] - pt3[1]) + pt2[0] * (pt3[1] - pt1[1]) + pt3[0] * (pt1[1] - pt2[1])) / 2.0; 
        }
    }



