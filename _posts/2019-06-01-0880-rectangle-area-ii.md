---
layout: post
title: 880. Rectangle Area II
---
### Question
We are given a list of (axis-aligned) `rectangles`.  Each `rectangle[i] = [x1,
y1, x2, y2] `, where (x1, y1) are the coordinates of the bottom-left corner,
and (x2, y2) are the coordinates of the top-right corner of the `i`th
rectangle.

Find the total area covered by all `rectangles` in the plane.  Since the
answer may be too large, **return it modulo 10^9 + 7**.

![](https://s3-lc-
upload.s3.amazonaws.com/uploads/2018/06/06/rectangle_area_ii_pic.png)

 **Example 1:**

    
    
     **Input:** [[0,0,2,2],[1,0,2,3],[1,0,3,1]]
    **Output:** 6
    **Explanation:** As illustrated in the picture.
    

**Example 2:**

    
    
    **Input:** [[0,0,1000000000,1000000000]]
    **Output:** 49
    **Explanation:** The answer is 10^18 modulo (10^9 + 7), which is (10^9)^2 = (-7)^2 = 49.
    

**Note:**

  * `1 <= rectangles.length <= 200`
  * ` rectanges[i].length = 4`
  * `0 <= rectangles[i][j] <= 10^9`
  * The total area covered by all rectangles will never exceed `2^63 - 1` and thus will fit in a 64-bit signed integer.

### Solution 1
 **Explanation** :  
Scan from `y = 0`, count the coverage of rectangles on `x`.  
For example, at `y = 0`, the intervale [0, 3] is covered by rectangles.  
The current sum of coverage is 3.

Then we move our scan line upper to next `y`.  
At `y = 1`, we add (1 - 0) * 3 = 3 to area. Now area = 3.  
And we update the sum of coverage to 2 because it's coverd on [0, 2].

Then we move our scan line upper to next `y`.  
At `y = 2`, we add (2 - 1) * 2 = 2 to area. Now area = 5.  
And we update the sum of coverage to 1 because it's coverd on [1, 2].

Then we move our scan line upper to next `y`.  
At `y = 3`, we add (3 - 2) * 1 = 1 to area. Now area = 6.  
And we update the sum of coverage to 0.

The final result is 6.

![](https://s3-lc-upload.s3.amazonaws.com/users/lee215/image_1528600475.png)

 **Time Complexity** :  
We checked every `y` value, and for every `y`, we count the coverage of `x`.  
So this a O(N^2) solution.  
In fact I expected the `N` to be big like 10^4. So only solution at least
O(N^2) can get accepted.

 **C++:**

    
    
         int rectangleArea(vector<vector<int>>& rectangles) {
            int mod = 1000000000 + 7;
            vector<int> x = {0};
            for (auto r : rectangles) {
                x.push_back(r[0]);
                x.push_back(r[2]);
            }
            sort(x.begin(), x.end());
            vector<int>::iterator end_unique = unique(x.begin(), x.end());
            x.erase(end_unique, x.end());
            unordered_map<int, int> x_i;
            for (int i = 0; i < x.size(); ++i) {
                x_i[x[i]] = i;
            }
            vector<int> count(x.size(), 0);
            vector<vector<int>> L;
            for (auto r : rectangles) {
                int x1 = r[0], y1 = r[1], x2 = r[2], y2 = r[3];
                L.push_back({y1, x1, x2, 1});
                L.push_back({y2, x1, x2, -1});
            }
            sort(L.begin(), L.end());
            long long cur_y = 0, cur_x_sum = 0, area = 0;
            for (auto l : L) {
                long long  y = l[0], x1 = l[1], x2 = l[2], sig = l[3];
                area = (area + (y - cur_y) * cur_x_sum) % mod;
                cur_y = y;
                for (int i = x_i[x1]; i < x_i[x2]; i++)
                    count[i] += sig;
                cur_x_sum = 0;
                for (int i = 0; i < x.size(); ++i) {
                    if (count[i] > 0)
                        cur_x_sum += x[i + 1] - x[i];
                }
            }
            return area;
        }
    

**Python:**

    
    
        def rectangleArea(self, rectangles):
            xs = sorted( set([x for x1, y1, x2, y2 in rectangles for x in [x1, x2]] + [0]))
            x_i = {v: i for i, v in enumerate(xs)}
            count = [0] * len(x_i)
            L = []
            for x1, y1, x2, y2 in rectangles:
                L.append([y1, x1, x2, 1])
                L.append([y2, x1, x2, -1])
            L.sort()
            cur_y = cur_x_sum = area = 0
            for y, x1, x2, sig in L:
                area += (y - cur_y) * cur_x_sum
                cur_y = y
                for i in range(x_i[x1], x_i[x2]):
                    count[i] += sig
                cur_x_sum = sum(x2 - x1 if c else 0 for x1, x2, c in zip(xs, xs[1:], count))
            return area % (10 ** 9 + 7)
    

**Follow Up Time**  
Can you do it better in O(NlogN)?

When I update the coverage on x, I updated one by one.  
But in fact I update for a range.  
If we use a segment tree to update the coverage, it will be only O(logN) for
add and delete action.

**Python:**

    
    
        def rectangleArea(self, rectangles):
            xs = sorted( set([x for x1, y1, x2, y2 in rectangles for x in [x1, x2]] + [0]))
            st = NumArray(xs)
            L = []
            for x1, y1, x2, y2 in rectangles:
                L.append([y1, 1, x1, x2])
                L.append([y2, -1, x1, x2])
            L.sort()
            cur_y = cur_x_sum = area = 0
            for y, sig, x1, x2 in L:
                area += (y - cur_y) * cur_x_sum
                cur_y = y
                cur_x_sum = st.update(x1, x2, sig)
            return area % (10 ** 9 + 7)
    
    


### Solution 2
<https://www.youtube.com/watch?v=RAx1dNmKxXY>  
I didn't solve Problem 3 with the expected time complexity. It's unfortunate
to let this code pass, please be aware.

Hope it's helpful.


### Solution 3
  1. Sort the points in `x` order
  2. For the points in the same `x`, calculate the current `y` (like the meeting room problem).
  3. In the next `x`, calculate the area by `preY * (curX - preX)`

The complexity in the worst case is `O(N ^ 2)` (all the rectangles have the
same `x`)

    
    
    class Solution {
        class Point {
            int x, y, val;
            Point(int x, int y, int val) {
                this.x = x;
                this.y = y;
                this.val = val;
            }
        }
        public int rectangleArea(int[][] rectangles) {
            int M = 1000000007;
            List<Point> data = new ArrayList<>();
            for (int[] r : rectangles) {
                data.add(new Point(r[0], r[1], 1));
                data.add(new Point(r[0], r[3], -1));
                data.add(new Point(r[2], r[1], -1));
                data.add(new Point(r[2], r[3], 1));
            }
            Collections.sort(data, (a, b) -> {
                if (a.x == b.x) {
                    return b.y - a.y;
                }
                return a.x - b.x;
            });
            TreeMap<Integer, Integer> map = new TreeMap<>();
            int preX = -1;
            int preY = -1;
            int result = 0;
            for (int i = 0; i < data.size(); i++) {
                Point p = data.get(i);
                map.put(p.y, map.getOrDefault(p.y, 0) + p.val);
                if (i == data.size() - 1 || data.get(i + 1).x > p.x) {
                    if (preX > -1) {
                        result += ((long)preY * (p.x - preX)) % M;
                        result %= M;
                    }
                    preY = calcY(map);
                    preX = p.x;
                }
            }
            return result;
        }
        private int calcY(TreeMap<Integer, Integer> map) {
            int result = 0, pre = -1, count = 0;
            for (Map.Entry<Integer, Integer> e : map.entrySet()) {
                if (pre >= 0 && count > 0) {
                    result += e.getKey() - pre;
                }
                count += e.getValue();
                pre = e.getKey();
            }
            return result;
        }
    }
    



