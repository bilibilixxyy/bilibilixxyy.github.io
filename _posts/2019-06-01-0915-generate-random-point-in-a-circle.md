---
layout: post
title: 915. Generate Random Point in a Circle
---
### Question
Given the radius and x-y positions of the center of a circle, write a function
`randPoint` which generates a uniform random point in the circle.

Note:

  1. input and output values are in [floating-point](https://www.webopedia.com/TERM/F/floating_point_number.html).
  2. radius and x-y position of the center of the circle is passed into the class constructor.
  3. a point on the circumference of the circle is considered to be in the circle.
  4. `randPoint` returns a size 2 array containing x-position and y-position of the random point, in that order.

 **Example 1:**

    
    
     **Input:** ["Solution","randPoint","randPoint","randPoint"]
    [[1,0,0],[],[],[]]
    **Output:** [null,[-0.72939,-0.65505],[-0.78502,-0.28626],[-0.83119,-0.19803]]
    

**Example 2:**

    
    
    **Input:** ["Solution","randPoint","randPoint","randPoint"]
    [[10,5,-7.5],[],[],[]]
    **Output:** [null,[11.52438,-8.33273],[2.46992,-16.21705],[11.13430,-12.42337]]

 **Explanation of Input Syntax:**

The input is two lists: the subroutines called and their arguments.
`Solution`'s constructor has three arguments, the radius, x-position of the
center, and y-position of the center of the circle. `randPoint` has no
arguments. Arguments are always wrapped with a list, even if there aren't any.

### Solution 1
The point is that we should not use `x=rand(len)*cos(rand(degree))`, we should
use `x=sqrt(rand(len))*cos(rand(degree))`.  
![image](https://s3-lc-
upload.s3.amazonaws.com/users/caraxin/image_1532665245.png)

Reference: <http://www.anderswallin.net/2009/05/uniform-random-points-in-a-
circle-using-polar-coordinates/>

    
    
    class Solution {
        double radius, x_center, y_center;
        public Solution(double radius, double x_center, double y_center) {
            this.radius=radius;
            this.x_center=x_center;
            this.y_center=y_center;
        }
        
        public double[] randPoint() {
            double len= Math.sqrt(Math.random())*radius;
            double deg= Math.random()*2*Math.PI;
            double x= x_center+len*Math.cos(deg);
            double y= y_center+len*Math.sin(deg);
            return new double[]{x,y};
        }
    }
    

**update** : thanks @jianhu for pointing this out, we should use`double deg =
Math.random() * Math.PI * 2` here.  
Happy Coding!


### Solution 2
Since we are given the `(x, y)` and `r`, so we can formulate this rectangle of
`[x-r, y-r, x+r, y+r]`. It is very easy for us to make samples from squares
uniformly, and we just reject the samples outside the circle.

    
    
    import random
    class Solution(object):
    
        def __init__(self, radius, x_center, y_center):
            """
            :type radius: float
            :type x_center: float
            :type y_center: float
            """
            self.x_min, self.x_max = x_center - radius, x_center + radius
            self.y_min, self.y_max = y_center - radius, y_center + radius
            self.radius = radius
            self.x_center = x_center
            self.y_center = y_center
    
        def randPoint(self):
            """
            :rtype: List[float]
            """
            while True:
                x, y = random.uniform(self.x_min, self.x_max), random.uniform(self.y_min, self.y_max)
                if (x - self.x_center)**2 + (y - self.y_center)**2 <= self.radius**2:
                    return [x, y]
    


### Solution 3
Out ouf curiosity, I was wondering how the solution is verified. Since we are
creating random numbers, how can the judge know that the solution is uniform?
It seems that it works since if you don't use the sqrt it fails, but how?

Since I doubt it has access to the internal mechanics of random generation in
every language, I guess that it calls a lot of random and checks if it follows
the accurate distribution, but as this comic tells us, you can never be sure
with randomness. What if the randomess just by chance created points that look
like another distribution?

![image](https://s3-lc-
upload.s3.amazonaws.com/users/ricola/image_1534934091.png)



