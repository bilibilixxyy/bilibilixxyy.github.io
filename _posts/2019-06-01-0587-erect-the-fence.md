---
layout: post
title: 587. Erect the Fence
---
### Question
There are some trees, where each tree is represented by (x,y) coordinate in a
two-dimensional garden. Your job is to fence the entire garden using the
**minimum length** of rope as it is expensive. The garden is well fenced only
if all the trees are enclosed. Your task is to help find the coordinates of
trees which are exactly located on the fence perimeter.



 **Example 1:**

    
    
     **Input:** [[1,1],[2,2],[2,0],[2,4],[3,3],[4,2]]
    **Output:** [[1,1],[2,0],[4,2],[3,3],[2,4]]
    **Explanation:**
    ![](https://assets.leetcode.com/uploads/2018/10/12/erect_the_fence_1.png)
    

**Example 2:**

    
    
    **Input:** [[1,2],[2,2],[4,2]]
    **Output:** [[1,2],[2,2],[4,2]]
    **Explanation:**
    ![](https://assets.leetcode.com/uploads/2018/10/12/erect_the_fence_2.png)
    Even you only have trees in a line, you need to use rope to enclose them. 
    



 **Note:**

  1. All trees should be enclosed together. You cannot cut the rope to enclose trees that will separate them in more than one group.
  2. All input integers will range from 0 to 100.
  3. The garden has at least one tree.
  4. All coordinates are distinct.
  5. Input points have **NO** order. No order required for output.
  6. input types have been changed on April 15, 2019. Please reset to default code definition to get new method signature.

### Solution 1
There are couple of ways to solve Convex Hull problem.
<https://en.wikipedia.org/wiki/Convex_hull_algorithms>  
The following code implements `Gift wrapping aka Jarvis march` algorithm
<https://en.wikipedia.org/wiki/Gift_wrapping_algorithm> and also added logic
to handle case of `multiple Points in a line` because original Jarvis march
algorithm assumes `no three points are collinear`.  
It also uses knowledge in this problem <https://leetcode.com/problems/convex-
polygon> . Disscussion: <https://discuss.leetcode.com/topic/70706/beyond-my-
knowledge-java-solution-with-in-line-explanation>

    
    
    public class Solution {
        public List<Point> outerTrees(Point[] points) {
            Set<Point> result = new HashSet<>();
            
            // Find the leftmost point
            Point first = points[0];
            int firstIndex = 0;
            for (int i = 1; i < points.length; i++) {
                if (points[i].x < first.x) {
                    first = points[i];
                    firstIndex = i;
                }
            }
            result.add(first);
            
            Point cur = first;
            int curIndex = firstIndex;
            do {
                Point next = points[0];
                int nextIndex = 0;
                for (int i = 1; i < points.length; i++) {
                    if (i == curIndex) continue;
                    int cross = crossProductLength(cur, points[i], next);
                    if (nextIndex == curIndex || cross > 0 ||
                            // Handle collinear points
                            (cross == 0 && distance(points[i], cur) > distance(next, cur))) {
                        next = points[i];
                        nextIndex = i;
                    }
                }
                // Handle collinear points
                for (int i = 0; i < points.length; i++) {
                    if (i == curIndex) continue;
                    int cross = crossProductLength(cur, points[i], next);
                    if (cross == 0) {
                        result.add(points[i]);
                    }
                }
    
                cur = next;
                curIndex = nextIndex;
                
            } while (curIndex != firstIndex);
            
            return new ArrayList<Point>(result);
        }
        
        private int crossProductLength(Point A, Point B, Point C) {
            // Get the vectors' coordinates.
            int BAx = A.x - B.x;
            int BAy = A.y - B.y;
            int BCx = C.x - B.x;
            int BCy = C.y - B.y;
        
            // Calculate the Z coordinate of the cross product.
            return (BAx * BCy - BAy * BCx);
        }
    
        private int distance(Point p1, Point p2) {
            return (p1.x - p2.x) * (p1.x - p2.x) + (p1.y - p2.y) * (p1.y - p2.y);
        }
    }
    


### Solution 2
The trick is that once all points are sorted by polar angle with respect to
the reference point:

  * For collinear points in the begin positions, make sure they are sorted by distance to reference point in **ascending** order.
  * For collinear points in the end positions, make sure they are sorted by distance to reference point in **descending** order.

For example:  
`(0, 0), (2, 0), (3, 0), (3, 1), (3, 2), (2, 2), (1, 2), (0, 2), (0, 1)`  
These points are sorted by polar angle  
The reference point (bottom left point) is `(0, 0)`

  * In the begin positions `(0, 0)` collinear with `(2, 0), (3, 0)` sorted by distance to reference point in **ascending** order.
  * In the end positions `(0, 0)` collinear with `(0, 2), (0, 1)` sorted by distance to reference point in **descending** order.

Now we can run the standard Graham scan to give us the desired result.

    
    
    public class Solution {
    
        public List<Point> outerTrees(Point[] points) {
            if (points.length <= 1)
                return Arrays.asList(points);
            sortByPolar(points, bottomLeft(points));
            Stack<Point> stack = new Stack<>(); 
            stack.push(points[0]);                      
            stack.push(points[1]);                              
            for (int i = 2; i < points.length; i++) {
                Point top = stack.pop();                                
                while (ccw(stack.peek(), top, points[i]) < 0)
                    top = stack.pop();
                stack.push(top);
                stack.push(points[i]);
            }       
            return new ArrayList<>(stack);
        }                               
    
        private static Point bottomLeft(Point[] points) {
            Point bottomLeft = points[0];
            for (Point p : points)          
                if (p.y < bottomLeft.y || p.y == bottomLeft.y && p.x < bottomLeft.x)
                    bottomLeft = p;                 
            return bottomLeft;                                                  
        }
    
        /**
         * @return positive if counter-clockwise, negative if clockwise, 0 if collinear
         */
        private static int ccw(Point a, Point b, Point c) {
            return a.x * b.y - a.y * b.x + b.x * c.y - b.y * c.x + c.x * a.y - c.y * a.x;       
        }
    
        /**
         * @return distance square of |p - q|
         */
        private static int dist(Point p, Point q) {
            return (p.x - q.x) * (p.x - q.x) + (p.y - q.y) * (p.y - q.y);
        }
                                  
        private static void sortByPolar(Point[] points, Point r) {
            Arrays.sort(points, (p, q) -> {
                int compPolar = ccw(p, r, q);
                int compDist = dist(p, r) - dist(q, r); 
                return compPolar == 0 ? compDist : compPolar;
            });     
            // find collinear points in the end positions
            Point p = points[0], q = points[points.length - 1];
            int i = points.length - 2;
            while (i >= 0 && ccw(p, q, points[i]) == 0)
                i--;    
            // reverse sort order of collinear points in the end positions
            for (int l = i + 1, h = points.length - 1; l < h; l++, h--) {
                Point tmp = points[l];
                points[l] = points[h];
                points[h] = tmp;
            }
        }
    }
    


### Solution 3
C++ version:

    
    
    // Ref: http://www.algorithmist.com/index.php/Monotone_Chain_Convex_Hull.cpp
    class Solution {
     public:
      typedef int coord_t;  // coordinate type
      typedef long long coord2_t;  // must be big enough to hold 2*max(|coordinate|)^2
      // 2D cross product of OA and OB vectors, i.e. z-component of their 3D cross
      // product. Returns a positive value, if OAB makes a counter-clockwise turn,
      // negative for clockwise turn, and zero if the points are collinear.
      coord2_t cross(const Point &O, const Point &A, const Point &B) {
        return (A.x - O.x) * (coord2_t)(B.y - O.y) -
               (A.y - O.y) * (coord2_t)(B.x - O.x);
      }
    
      static bool cmp(Point &p1, Point &p2) {
        return p1.x < p2.x || (p1.x == p2.x && p1.y < p2.y);
      }
    
      static bool equ(Point &p1, Point &p2) { return p1.x == p2.x && p1.y == p2.y; }
      // Returns a list of points on the convex hull in counter-clockwise order.
      // Note: the last point in the returned list is the same as the first one.
      vector<Point> outerTrees(vector<Point> &P) {
        int n = P.size(), k = 0;
        vector<Point> H(2 * n);
    
        // Sort points lexicographically
        sort(P.begin(), P.end(), cmp);
    
        // Build lower hull
        for (int i = 0; i < n; i++) {
          while (k >= 2 && cross(H[k - 2], H[k - 1], P[i]) < 0) k--;
          H[k++] = P[i];
        }
    
        // Build upper hull
        for (int i = n - 2, t = k + 1; i >= 0; i--) {
          while (k >= t && cross(H[k - 2], H[k - 1], P[i]) < 0) k--;
          H[k++] = P[i];
        }
    
        // Remove duplicates
        H.resize(k);
        sort(H.begin(), H.end(), cmp);
        H.erase(unique(H.begin(), H.end(), equ), H.end());
        return H;
      }
    };
    

Python version:

    
    
    # http://www.algorithmist.com/index.php/Monotone_Chain_Convex_Hull.py
    
    
    class Solution(object):
    
        def outerTrees(self, points):
            """Computes the convex hull of a set of 2D points.
    
            Input: an iterable sequence of (x, y) pairs representing the points.
            Output: a list of vertices of the convex hull in counter-clockwise order,
              starting from the vertex with the lexicographically smallest coordinates.
            Implements Andrew's monotone chain algorithm. O(n log n) complexity.
            """
    
            # Sort the points lexicographically (tuples are compared lexicographically).
            # Remove duplicates to detect the case we have just one unique point.
            # points = sorted(set(points))
            points = sorted(points, key=lambda p: (p.x, p.y))
    
            # Boring case: no points or a single point, possibly repeated multiple times.
            if len(points) <= 1:
                return points
    
            # 2D cross product of OA and OB vectors, i.e. z-component of their 3D cross product.
            # Returns a positive value, if OAB makes a counter-clockwise turn,
            # negative for clockwise turn, and zero if the points are collinear.
            def cross(o, a, b):
                # return (a[0] - o[0]) * (b[1] - o[1]) - (a[1] - o[1]) * (b[0] - o[0])
                return (a.x - o.x) * (b.y - o.y) - (a.y - o.y) * (b.x - o.x)
    
            # Build lower hull
            lower = []
            for p in points:
                while len(lower) >= 2 and cross(lower[-2], lower[-1], p) < 0:
                    lower.pop()
                lower.append(p)
    
            # Build upper hull
            upper = []
            for p in reversed(points):
                while len(upper) >= 2 and cross(upper[-2], upper[-1], p) < 0:
                    upper.pop()
                upper.append(p)
    
            # Concatenation of the lower and upper hulls gives the convex hull.
            # Last point of each list is omitted because it is repeated at the
            # beginning of the other list.
            # return lower[:-1] + upper[:-1]
            return list(set(lower[:-1] + upper[:-1]))
    



