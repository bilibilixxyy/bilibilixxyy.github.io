---
layout: post
title: 149. Max Points on a Line
---
### Question
Given _n_ points on a 2D plane, find the maximum number of points that lie on
the same straight line.

 **Example 1:**

    
    
     **Input:** [[1,1],[2,2],[3,3]]
    **Output:** 3
    **Explanation:**
    ^
    |
    |         o
    |     o
    |  o  
    +------------->
    0  1  2  3  4
    

**Example 2:**

    
    
    **Input:** [[1,1],[3,2],[5,3],[4,1],[2,3],[1,4]]
    **Output:** 4
    **Explanation:**
    ^
    |
    |  o
    |      o        o
    |        o
    |  o        o
    +------------------->
    0  1  2  3  4  5  6
    

**NOTE:**  input types have been changed on April 15, 2019. Please reset to
default code definition to get new method signature.

### Solution 1
    
    
       /*
         *  A line is determined by two factors,say y=ax+b
         *  
         *  If two points(x1,y1) (x2,y2) are on the same line(Of course). 
    
         *  Consider the gap between two points.
    
         *  We have (y2-y1)=a(x2-x1),a=(y2-y1)/(x2-x1) a is a rational, b is canceled since b is a constant
    
         *  If a third point (x3,y3) are on the same line. So we must have y3=ax3+b
    
         *  Thus,(y3-y1)/(x3-x1)=(y2-y1)/(x2-x1)=a
    
         *  Since a is a rational, there exists y0 and x0, y0/x0=(y3-y1)/(x3-x1)=(y2-y1)/(x2-x1)=a
    
         *  So we can use y0&x0 to track a line;
         */
        
        public class Solution{
            public int maxPoints(Point[] points) {
            	if (points==null) return 0;
            	if (points.length<=2) return points.length;
            	
            	Map<Integer,Map<Integer,Integer>> map = new HashMap<Integer,Map<Integer,Integer>>();
            	int result=0;
            	for (int i=0;i<points.length;i++){ 
            		map.clear();
            		int overlap=0,max=0;
            		for (int j=i+1;j<points.length;j++){
            			int x=points[j].x-points[i].x;
            			int y=points[j].y-points[i].y;
            			if (x==0&&y==0){
            				overlap++;
            				continue;
            			}
            			int gcd=generateGCD(x,y);
            			if (gcd!=0){
            				x/=gcd;
            				y/=gcd;
            			}
            			
            			if (map.containsKey(x)){
            				if (map.get(x).containsKey(y)){
            					map.get(x).put(y, map.get(x).get(y)+1);
            				}else{
            					map.get(x).put(y, 1);
            				}   					
            			}else{
            				Map<Integer,Integer> m = new HashMap<Integer,Integer>();
            				m.put(y, 1);
            				map.put(x, m);
            			}
            			max=Math.max(max, map.get(x).get(y));
            		}
            		result=Math.max(result, max+overlap+1);
            	}
            	return result;
            	
            	
            }
            private int generateGCD(int a,int b){
        
            	if (b==0) return a;
            	else return generateGCD(b,a%b);
            	
            }
        }


### Solution 2
    
    
    int maxPoints(vector<Point> &points) {
        int result = 0;
        for(int i = 0; i < points.size(); i++){
            int samePoint = 1;
            unordered_map<double, int> map;
            for(int j = i + 1; j < points.size(); j++){
                if(points[i].x == points[j].x && points[i].y == points[j].y){
                    samePoint++;
                }
                else if(points[i].x == points[j].x){
                    map[INT_MAX]++;
                }
                else{
                    double slope = double(points[i].y - points[j].y) / double(points[i].x - points[j].x);
                    map[slope]++;
                }
            }
            int localMax = 0;
            for(auto it = map.begin(); it != map.end(); it++){
                localMax = max(localMax, it->second);
            }
            localMax += samePoint;
            result = max(result, localMax);
        }
        return result;
    }
    

First, let's talk about mathematics.

How to determine if three points are on the same line?

The answer is to see if slopes of arbitrary two pairs are the same.

Second, let's see what the minimum time complexity can be.

Definitely, O(n^2). It's because you have to calculate all slopes between any
two points.

Then let's go back to the solution of this problem.

In order to make this discussion simpler, let's pick a random point A as an
example.

Given point A, we need to calculate all slopes between A and other points.
There will be three cases:

  1. Some other point is the same as point A.

  2. Some other point has the same x coordinate as point A, which will result to a positive infinite slope.

  3. General case. We can calculate slope.

We can store all slopes in a hash table. And we find which slope shows up
mostly. Then add the number of same points to it. Then we know the maximum
number of points on the same line for point A.

We can do the same thing to point B, point C...

Finally, just return the maximum result among point A, point B, point C...


### Solution 3
    
    
        /**
     * Definition for a point.
     * class Point {
     *     int x;
     *     int y;
     *     Point() { x = 0; y = 0; }
     *     Point(int a, int b) { x = a; y = b; }
     * }
     */
    public class Solution {
        public int maxPoints(Point[] points) {
            if(points.length <= 0) return 0;
            if(points.length <= 2) return points.length;
            int result = 0;
            for(int i = 0; i < points.length; i++){
                HashMap<Double, Integer> hm = new HashMap<Double, Integer>();
                int samex = 1;
                int samep = 0;
                for(int j = 0; j < points.length; j++){
                    if(j != i){
                        if((points[j].x == points[i].x) && (points[j].y == points[i].y)){
                            samep++;
                        }
                        if(points[j].x == points[i].x){
                            samex++;
                            continue;
                        }
                        double k = (double)(points[j].y - points[i].y) / (double)(points[j].x - points[i].x);
                        if(hm.containsKey(k)){
                            hm.put(k,hm.get(k) + 1);
                        }else{
                            hm.put(k, 2);
                        }
                        result = Math.max(result, hm.get(k) + samep);
                    }
                }
                result = Math.max(result, samex);
            }
            return result;
        }
    }



