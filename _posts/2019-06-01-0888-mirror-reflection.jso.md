---
layout: post
title: 888. Mirror Reflection
---
### Question
There is a special square room with mirrors on each of the four walls.  Except
for the southwest corner, there are receptors on each of the remaining
corners, numbered `0`, `1`, and `2`.

The square room has walls of length `p`, and a laser ray from the southwest
corner first meets the east wall at a distance `q` from the `0`th receptor.

Return the number of the receptor that the ray meets first.  (It is guaranteed
that the ray will meet a receptor eventually.)



 **Example 1:**

    
    
     **Input:** p = 2, q = 1
    **Output:** 2
    **Explanation:** The ray meets receptor 2 the first time it gets reflected back to the left wall.
    
    
    ![](https://ibb.co/mYSFJT)![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/06/18/reflection.png)
    
    
    

**Note:**

  1. `1 <= p <= 1000`
  2. `0 <= q <= p`

### Solution 1
![image](https://s3-lc-
upload.s3.amazonaws.com/users/wangzi6147/image_1529813576.png)

I draw this graph to help. In this example, `p / q = 5 / 2`. In this way, we
can find some patterns between pq and the result.

I use the extending blocks to simulate the reflection. As well as the
reflection of the corners `0, 1, 2`. Then the laser could go straightly to the
top right of the extending blocks until it reaches any of the corners. Then we
can use `p/q` to represents the extending blocks `x/y` ratio which is `5/2` in
the example above. Then the destination corner could be calculated using this
`x/y` ratio.  
The reason why we used modulo 2 is that if `x/y=10/4` for example, the laser
will firstly reach the corner `0` when `x/y=5/2` before `x/y=10/4`. You can
draw a similar graph to simulate it.  
In other words, when the laser first reaches a corner, the `x` and `y` cannot
both be even. So we keep using modulo 2 to let `x` and `y` go down.

    
    
    class Solution {
        public int mirrorReflection(int p, int q) {
            while (p % 2 == 0 && q % 2 == 0) {
                p /= 2;
                q /= 2;
            }
            if (p % 2 == 0) {
                return 2;
            } else if (q % 2 == 0) {
                return 0;
            } else {
                return 1;
            }
        }
    }
    


### Solution 2
Here is my first solution. When I did it, I just code without any thinking.

    
    
        def mirrorReflection(self, p, q):
            k = 1
            while (q * k % p): k += 1
            if q * k / p % 2 and k % 2: return 1
            if q * k / p % 2 == 0 and k % 2: return 0
            if q * k / p % 2 and k % 2 == 0: return 2
            return -1
    

When I reviewed the problems for my discuss article, I finaly realised only
odd or even matter.

Divide `p,q` by `2` until at least one odd.

If `p = odd, q = even`: return 0  
If `p = even, q = odd`: return 2  
If `p = odd, q = odd`: return 1  
I summary it as `return 1 - p % 2 + q % 2`

![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/06/18/reflection.png)

**C++:**

    
    
         int mirrorReflection(int p, int q) {
            while (p % 2 == 0 && q % 2 == 0) p >>= 1, q >>= 1;
            return 1 - p % 2 + q % 2;
        }
    

**Java:**

    
    
         public int mirrorReflection(int p, int q) {
            while (p % 2 == 0 && q % 2 == 0) {p >>= 1; q >>= 1;}
            return 1 - p % 2 + q % 2;
        }
    

**Python:**

    
    
        def mirrorReflection(self, p, q):
             while p % 2 == 0 and q % 2 == 0: p, q = p / 2, q / 2
            return 1 - p % 2 + q % 2
    

Based on this idea, I did this 1-line solutions without any package.  
**C++/Java**

    
    
             return (p & -p) > (q & -q) ? 2 : (p & -p) < (q & -q) ? 0:1;
    

**Python**

    
    
            return (( p & -p) >= (q & -q)) + ((p & -p) > (q & -q))
    

**Time Complexity**  
Time for 4 bit manipulations and 2 comparations.


### Solution 3
Just straight forward. Use a while loop to compute every reflection point in
bound until the light reaches the corner 0 or 1 or 2. Totally physical
imitation.

    
    
    class Solution {
        
        class Point{
            double x;
            double y;
            
            public Point(double x,double y){
                this.x = x;
                this.y = y;
            }
        }
        
        int p;
        
        Point P_LB; //left - bottom
        Point P_LT;
        Point P_RB;
        Point P_RT; //right - top
        
        public int mirrorReflection(int p, int q) {
            this.p = p;
            P_LB = new Point(0.0,0.0);
            P_LT = new Point(0.0,(double)p);
            P_RB = new Point((double)p,0.0);
            P_RT = new Point((double)p,(double)p);
          
                
            Point start = new Point(0.0,0.0);
            Point end = new Point((double)p,(double)q);
            
            while(!isCorner(end)){
                Point mirror = findMirPoint(start,end);
                Point next = insect(end,mirror);
                
                start = end;
                end = next;
            }
            
            if(isEquals(end,P_LT)){
                return 2;
            }
            
            if(isEquals(end,P_RT)){
                return 1;
            }
            
            if(isEquals(end,P_RB)){
                return 0;
            }
            
            return -1;
        }
        
        private boolean isCorner(Point end){
            if(Math.abs(end.x-0.0)<0.001){
                return Math.abs(end.y-p)<0.001; // corner 2
            }else if(Math.abs(end.x-p)<0.001){
                return Math.abs(end.y-0.0)<0.001 || Math.abs(end.y-p)<0.001; //corner 0 || corner 1
            }else{
                return false;
            }
            
        }
        
        private Point findMirPoint(Point start,Point end){
            if(Math.abs(end.x-0.0)<0.001 || Math.abs(end.x-p)<0.001){ //left or right mirror
                return new Point(start.x,end.y*2-start.y);
                /*
                if(start.y<end.y){
                    return new Point(start.x,end.y+(end.y-start.y));
                }else{
                    return new Point(start.x,end.y-(start.y-end.y));
                }
                */
                
            }else{ //Math.abs(end.y-0.0)<0.001 || Math.abs(end.y-p)<0.001 //bottom or top mirror
                return new Point(end.x*2-start.x,start.y);
            }
        }
        
        
        private boolean inBound(Point next){
            return next.x>-0.001 && next.x<p+0.001 && next.y>-0.001 && next.y<p+0.001;
        }
        
        private boolean isEquals(Point a,Point b){
            return Math.abs(a.x-b.x)<0.001 && Math.abs(a.y-b.y)<0.001;
        }
        
        private Point insect(Point end,Point next){
            
            Point temp1 = calculateIntersectionPoint(end,next,P_LB,P_LT);
            Point temp2 = calculateIntersectionPoint(end,next,P_LB,P_RB);
            Point temp3 = calculateIntersectionPoint(end,next,P_RT,P_LT);
            Point temp4 = calculateIntersectionPoint(end,next,P_RT,P_RB);
            
            Point[] pArr = new Point[]{temp1,temp2,temp3,temp4};
            
            for(Point temp:pArr){
                if(inBound(temp) && !isEquals(temp,end)) return temp;
            }
            
            return null;
        }
        
        private double calculateVectorProduct(Point P1, Point P2, Point P3, Point P4) { 
            return(P2.x-P1.x) * (P4.y-P3.y) - (P2.y-P1.y) * (P4.x-P3.x);
        }
        
        private Point calculateIntersectionPoint(Point A, Point B, Point C, Point D) {
            double t1 = calculateVectorProduct(C, D, A, B);
            double t2 = calculateVectorProduct(A, B, A, C);
            double x = C.x +(D.x-C.x) * t2 / t1;
            double y = C.y +(D.y-C.y) * t2 / t1;
            return new Point(x, y);
        }
    }
    



