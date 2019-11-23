---
layout: post
title: 699. Falling Squares
---
### Question
On an infinite number line (x-axis), we drop given squares in the order they
are given.

The `i`-th square dropped (`positions[i] = (left, side_length)`) is a square
with the left-most point being `positions[i][0]` and sidelength
`positions[i][1]`.

The square is dropped with the bottom edge parallel to the number line, and
from a higher height than all currently landed squares. We wait for each
square to stick before dropping the next.

The squares are infinitely sticky on their bottom edge, and will remain fixed
to any positive length surface they touch (either the number line or another
square). Squares dropped adjacent to each other will not stick together
prematurely.



Return a list `ans` of heights. Each height `ans[i]` represents the current
highest height of any square we have dropped, after dropping squares
represented by `positions[0], positions[1], ..., positions[i]`.

 **Example 1:**

    
    
     **Input:** [[1, 2], [2, 3], [6, 1]]
    **Output:** [2, 5, 5]
    **Explanation:**
    

After the first drop of `positions[0] = [1, 2]: _aa _aa ------- `The maximum
height of any square is 2.

After the second drop of `positions[1] = [2, 3]: __aaa __aaa __aaa _aa__ _aa__
-------------- `The maximum height of any square is 5. The larger square stays
on top of the smaller square despite where its center of gravity is, because
squares are infinitely sticky on their bottom edge.

After the third drop of `positions[1] = [6, 1]: __aaa __aaa __aaa _aa _aa___a
-------------- `The maximum height of any square is still 5. Thus, we return
an answer of `[2, 5, 5]`.





**Example 2:**

    
    
    **Input:** [[100, 100], [200, 100]]
    **Output:** [100, 100]
    **Explanation:** Adjacent squares don't get stuck prematurely - only their bottom edge can stick to surfaces.
    



 **Note:**

  * `1 <= positions.length <= 1000`.
  * `1 <= positions[i][0] <= 10^8`.
  * `1 <= positions[i][1] <= 10^6`.

### Solution 1
The idea is quite simple, we use intervals to represent the square. the
initial height we set to the square `cur` is `pos[1]`. That means we assume
that all the square will fall down to the land. we iterate the previous
squares, check is there any square `i` beneath my `cur` square. If we found
that we have squares `i` intersect with us, which means my current square will
go above to that square `i`. My target is to find the highest square and put
square `cur` onto square `i`, and set the height of the square `cur` as

    
    
    cur.height = cur.height + previousMaxHeight;
    

Actually, you do not need to use the interval class to be faster, I just use
it to make my code cleaner

    
    
    class Solution {
        private class Interval {
            int start, end, height;
            public Interval(int start, int end, int height) {
                this.start = start;
                this.end = end;
                this.height = height;
            }
        }
        public List<Integer> fallingSquares(int[][] positions) {
            List<Interval> intervals = new ArrayList<>();
            List<Integer> res = new ArrayList<>();
            int h = 0;
            for (int[] pos : positions) {
                Interval cur = new Interval(pos[0], pos[0] + pos[1] - 1, pos[1]);
                h = Math.max(h, getHeight(intervals, cur));
                res.add(h);
            }
            return res;
        }
        private int getHeight(List<Interval> intervals, Interval cur) {
            int preMaxHeight = 0;
            for (Interval i : intervals) {
                // Interval i does not intersect with cur
                if (i.end < cur.start) continue;
                if (i.start > cur.end) continue;
                // find the max height beneath cur
                preMaxHeight = Math.max(preMaxHeight, i.height);
            }
            cur.height += preMaxHeight;
            intervals.add(cur);
            return cur.height;
        }
    }
    


### Solution 2
Here is the link I refer to:  
<https://www.geeksforgeeks.org/lazy-propagation-in-segment-tree/>  
<https://leetcode.com/articles/falling-squares/>

I think the first link is pretty useful to help you understand the segment
tree's basic and lazy propagation. The second link I really appreciate its
systematic way to define the interface to solve this using different
algorithm. However, the segment tree implementation is not well explained in
the article (at least it's hard for me to understand).  
Thus, I decided to post my Treemap solution, segment tree solution and segment
tree solution with lazy propagation for future readers.

TreeMap Solution: The basic idea here is pretty simple, for each square i, I
will find all the maximum height from previously dropped squares range from
floorKey(i_start) (inclusive) to end (exclusive), then I will update the
height and delete all the old heights.

    
    
    import java.util.SortedMap;
    class Solution {
        public List<Integer> fallingSquares(int[][] positions) {
            List<Integer> res = new ArrayList<>();
            TreeMap<Integer, Integer> startHeight = new TreeMap<>();
            startHeight.put(0, 0); 
            int max = 0;
            for (int[] pos : positions) {
                int start = pos[0], end = start + pos[1];
                Integer from = startHeight.floorKey(start);
                int height = startHeight.subMap(from, end).values().stream().max(Integer::compare).get() + pos[1];
                max = Math.max(height, max);
                res.add(max);
                // remove interval within [start, end)
                int lastHeight = startHeight.floorEntry(end).getValue();
                startHeight.put(start, height);
                startHeight.put(end, lastHeight);
                startHeight.keySet().removeAll(new HashSet<>(startHeight.subMap(start, false, end, false).keySet()));
            }
            return res;
        }
    }
    

Segment tree with coordinates compression: It's easy to understand if go
through the links above.

    
    
    class Solution {
        public List<Integer> fallingSquares(int[][] positions) {
            int n = positions.length;
            Map<Integer, Integer> cc = coorCompression(positions);
            int best = 0;
            List<Integer> res = new ArrayList<>();
            SegmentTree tree = new SegmentTree(cc.size());
            for (int[] pos : positions) {
                int L = cc.get(pos[0]);
                int R = cc.get(pos[0] + pos[1] - 1);
                int h = tree.query(L, R) + pos[1];
                tree.update(L, R, h);
                best = Math.max(best, h);
                res.add(best);
            }
            return res;
        }
        
        private Map<Integer, Integer> coorCompression(int[][] positions) {
            Set<Integer> set = new HashSet<>();
            for (int[] pos : positions) {
                set.add(pos[0]);
                set.add(pos[0] + pos[1] - 1);
            }
            List<Integer> list = new ArrayList<>(set);
            Collections.sort(list);
            Map<Integer, Integer> map = new HashMap<>();
            int t = 0;
            for (int pos : list) map.put(pos, t++);
            return map;
        }
        
        class SegmentTree {
            int[] tree;
            int N;
            
            SegmentTree(int N) {
                this.N = N;
                int n = (1 << ((int) Math.ceil(Math.log(N) / Math.log(2)) + 1));
                tree = new int[n];
            }
            
            public int query(int L, int R) {
                return queryUtil(1, 0, N - 1, L, R);
            }
            
            private int queryUtil(int index, int s, int e, int L, int R) {
                // out of range
                if (s > e || s > R || e < L) {
                    return 0;
                }
                // [L, R] cover [s, e]
                if (s >= L && e <= R) {
                    return tree[index];
                }
                // Overlapped
                int mid = s + (e - s) / 2;
                return Math.max(queryUtil(2 * index, s, mid, L, R), queryUtil(2 * index + 1, mid + 1, e, L, R));
            }
            
            public void update(int L, int R, int h) {
                updateUtil(1, 0, N - 1, L, R, h);
            }
            
            private void updateUtil(int index, int s, int e, int L, int R, int h) {
                // out of range
                if (s > e || s > R || e < L) {
                    return;
                }
                tree[index] = Math.max(tree[index], h);
                if (s != e) {
                    int mid = s + (e - s) / 2;
                    updateUtil(2 * index, s, mid, L, R, h);
                    updateUtil(2 * index + 1, mid + 1, e, L, R, h);
                }
            }
        }
    }
    

Segment tree with lazy propagation and coordinates compression: This code is
easy to add if you already got the code above.

    
    
    class Solution {
        public List<Integer> fallingSquares(int[][] positions) {
            int n = positions.length;
            Map<Integer, Integer> cc = coorCompression(positions);
            int best = 0;
            List<Integer> res = new ArrayList<>();
            SegmentTree tree = new SegmentTree(cc.size());
            for (int[] pos : positions) {
                int L = cc.get(pos[0]);
                int R = cc.get(pos[0] + pos[1] - 1);
                int h = tree.query(L, R) + pos[1];
                tree.update(L, R, h);
                best = Math.max(best, h);
                res.add(best);
            }
            return res;
        }
        
        private Map<Integer, Integer> coorCompression(int[][] positions) {
            Set<Integer> set = new HashSet<>();
            for (int[] pos : positions) {
                set.add(pos[0]);
                set.add(pos[0] + pos[1] - 1);
            }
            List<Integer> list = new ArrayList<>(set);
            Collections.sort(list);
            Map<Integer, Integer> map = new HashMap<>();
            int t = 0;
            for (int pos : list) map.put(pos, t++);
            return map;
        }
        
        class SegmentTree {
            int[] tree;
            int[] lazy;
            int N;
            
            SegmentTree(int N) {
                this.N = N;
                int n = (1 << ((int) Math.ceil(Math.log(N) / Math.log(2)) + 1));
                tree = new int[n];
                lazy = new int[n];
            }
            
            public int query(int L, int R) {
                return queryUtil(1, 0, N - 1, L, R);
            }
            
            private int queryUtil(int index, int s, int e, int L, int R) {
                if (lazy[index] != 0) {
                    // apply lazy to node
                    int update = lazy[index];
                    lazy[index] = 0;
                    tree[index] = Math.max(tree[index], update);
                    // check if this is leaf
                    if (s != e) {
                        lazy[2 * index] = Math.max(lazy[2 * index], update);
                        lazy[2 * index + 1] = Math.max(lazy[2 * index + 1], update);
                    }
                }
                // out of range
                if (s > e || s > R || e < L) {
                    return 0;
                }
                // [L, R] cover [s, e]
                if (s >= L && e <= R) {
                    return tree[index];
                }
                // Overlapped
                int mid = s + (e - s) / 2;
                return Math.max(queryUtil(2 * index, s, mid, L, R), queryUtil(2 * index + 1, mid + 1, e, L, R));
            }
            
            public void update(int L, int R, int h) {
                updateUtil(1, 0, N - 1, L, R, h);
            }
            
            private void updateUtil(int index, int s, int e, int L, int R, int h) {
                if (lazy[index] != 0) {
                    // apply lazy to node
                    int update = lazy[index];
                    lazy[index] = 0;
                    tree[index] = Math.max(tree[index], update);
                    // check if this is leaf
                    if (s != e) {
                        lazy[2 * index] = Math.max(lazy[2 * index], update);
                        lazy[2 * index + 1] = Math.max(lazy[2 * index + 1], update);
                    }
                }
                // out of range
                if (s > e || s > R || e < L) {
                    return;
                }
                if (s >= L && e <= R) {
                    tree[index] = Math.max(tree[index], h);
                    if (s != e) {
                        lazy[2 * index] = Math.max(lazy[2 * index], h);
                        lazy[2 * index + 1] = Math.max(lazy[2 * index + 1], h);
                    }
                    return;
                }
                int mid = s + (e - s) / 2;
                updateUtil(2 * index, s, mid, L, R, h);
                updateUtil(2 * index + 1, mid + 1, e, L, R, h);
                tree[index] = Math.max(tree[2 * index], tree[2 * index + 1]);
            }
        }
    }
    


### Solution 3
I used two list to take note of the current state.  
If you read question **218\. The Skyline Problem** , you will easily
understand how I do this.

`pos` tracks the x-coordinate of start points.  
`height` tracks the y-coordinate of lines.

![0_1508239910449_skyline.png](https://discuss.leetcode.com/assets/uploads/files/1508239911437-skyline.png)

    
    
    def fallingSquares(self, positions):
            height = [0]
            pos = [0]
            res = []
            max_h = 0
            for left, side in positions:
                i = bisect.bisect_right(pos, left)
                j = bisect.bisect_left(pos, left + side)
                high = max(height[i - 1:j] or [0]) + side
                pos[i:j] = [left, left + side]
                height[i:j] = [high, height[j - 1]]
                max_h = max(max_h, high)
                res.append(max_h)
            return res



