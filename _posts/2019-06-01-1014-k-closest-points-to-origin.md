---
layout: post
title: 1014. K Closest Points to Origin
---
### Question
We have a list of `points` on the plane.  Find the `K` closest points to the
origin `(0, 0)`.

(Here, the distance between two points on a plane is the Euclidean distance.)

You may return the answer in any order.  The answer is guaranteed to be unique
(except for the order that it is in.)



 **Example 1:**

    
    
     **Input:** points = [[1,3],[-2,2]], K = 1
    **Output:** [[-2,2]]
    **Explanation:**
    The distance between (1, 3) and the origin is sqrt(10).
    The distance between (-2, 2) and the origin is sqrt(8).
    Since sqrt(8)  < sqrt(10), (-2, 2) is closer to the origin.
    We only want the closest K = 1 points from the origin, so the answer is just [[-2,2]].
    

**Example 2:**

    
    
    **Input:** points = [[3,3],[5,-1],[-2,4]], K = 2
    **Output:** [[3,3],[-2,4]]
    (The answer [[-2,4],[3,3]] would also be accepted.)
    



 **Note:**

  1. `1 <= K <= points.length <= 10000`
  2. `-10000 < points[i][0] < 10000`
  3. `-10000 < points[i][1] < 10000`

### Solution 1
This is a very classical problem, so-called K-th problem.  
Here I will share some summaries and some classical solutions to this kind of
problem.

 **I.** The very naive and simple solution is sorting the all points by their
distance to the origin point directly, then get the top k closest points. We
can use the sort function and the code is very short.

 **Theoretically** , the time complexity is **O(NlogN)** , **pratically** ,
the real time it takes on leetcode is **104ms**.

The **advantages** of this solution are **short** , intuitive and easy to
implement.  
The **disadvatages** of this solution are not very efficient and have to know
all of the points previously, and it is unable to deal with real-time(online)
case, it is an **off-line** solution.

The short code shows as follows:

    
    
    public int[][] kClosest(int[][] points, int K) {
        Arrays.sort(points, (p1, p2) -> p1[0] * p1[0] + p1[1] * p1[1] - p2[0] * p2[0] - p2[1] * p2[1]);
        return Arrays.copyOfRange(points, 0, K);
    }
    

**II.** The second solution is based on the first one. We don't have to sort
all points.  
Instead, we can maintain a **max-heap** with size K. Then for each point, we
add it to the heap. Once the size of the heap is greater than K, we are
supposed to extract one from the max heap to ensure the size of the heap is
always K. Thus, the max heap is always maintain top K smallest elements from
the first one to crruent one. Once the size of the heap is over its maximum
capacity, it will exclude the maximum element in it, since it can not be the
proper candidate anymore.

**Theoretically** , the time complexity is **O(NlogK)** , but **pratically** ,
the real time it takes on leetcode is **134ms**.

The **advantage** of this solution is it can deal with **real-time(online)
stream data**. It does not have to know the size of the data previously.  
The **disadvatage** of this solution is it is not the most efficient solution.

The short code shows as follows:

    
    
    public int[][] kClosest(int[][] points, int K) {
        PriorityQueue<int[]> pq = new PriorityQueue<int[]>((p1, p2) -> p2[0] * p2[0] + p2[1] * p2[1] - p1[0] * p1[0] - p1[1] * p1[1]);
        for (int[] p : points) {
            pq.offer(p);
            if (pq.size() > K) {
                pq.poll();
            }
        }
        int[][] res = new int[K][2];
        while (K > 0) {
            res[--K] = pq.poll();
        }
        return res;
    }
    

**III.** The last solution is based on quick sort, we can also call it **quick
select**. In the quick sort, we will always choose a pivot to compare with
other elements. After one iteration, we will get an array that all elements
smaller than the pivot are on the left side of the pivot and all elements
greater than the pivot are on the right side of the pviot (assuming we sort
the array in ascending order). So, inspired from this, each iteration, we
choose a pivot and then find the position **p** the pivot should be. Then we
compare **p** with the **K** , if the **p** is smaller than the **K** ,
meaning the all element on the left of the pivot are all proper candidates but
it is not adequate, we have to do the same thing on right side, and vice
versa. If the **p** is exactly equal to the **K** , meaning that we've found
the K-th position. Therefore, we just return the first K elements, since they
are not greater than the pivot.

**Theoretically** , the average time complexity is **O(N)** , but just like
quick sort, in the worst case, this solution would be degenerated to
**O(N^2)** , and **pratically** , the real time it takes on leetcode is
**15ms**.

The **advantage** of this solution is it is very efficient.  
The **disadvatage** of this solution are it is neither an online solution nor
a stable one. And the K elements closest are **not sorted** in ascending
order.

The short code shows as follows:

    
    
    public int[][] kClosest(int[][] points, int K) {
        int len =  points.length, l = 0, r = len - 1;
        while (l <= r) {
            int mid = helper(points, l, r);
            if (mid == K) break;
            if (mid < K) {
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return Arrays.copyOfRange(points, 0, K);
    }
    
    private int helper(int[][] A, int l, int r) {
        int[] pivot = A[l];
        while (l < r) {
            while (l < r && compare(A[r], pivot) >= 0) r--;
            A[l] = A[r];
            while (l < r && compare(A[l], pivot) <= 0) l++;
            A[r] = A[l];
        }
        A[l] = pivot;
        return l;
    }
    
    private int compare(int[] p1, int[] p2) {
        return p1[0] * p1[0] + p1[1] * p1[1] - p2[0] * p2[0] - p2[1] * p2[1];
    }
    


### Solution 2
The simplest solution is to use `partial_sort` or `nth_element` to order the
`K` closest points at the beginning of `points`. Here we need a custom
comparator to compare the _closeness_ of points. This solution is of
`O(nlogK)` time. If we ignore the space of the output since that is
inevitable, this solution is of `O(1)` space.

    
    
     class Solution {
    public:
        vector<vector<int>> kClosest(vector<vector<int>>& points, int K) {
            partial_sort(points.begin(), points.begin() + K, points.end(), [](vector<int>& p, vector<int>& q) {
                return p[0] * p[0] + p[1] * p[1] < q[0] * q[0] + q[1] * q[1];
            });
            return vector<vector<int>>(points.begin(), points.begin() + K);
        }
    };
    
    
    
    class Solution {
    public:
        vector<vector<int>> kClosest(vector<vector<int>>& points, int K) {
            nth_element(points.begin(), points.begin() + K - 1, points.end(), [](vector<int>& p, vector<int>& q) {
                return p[0] * p[0] + p[1] * p[1] < q[0] * q[0] + q[1] * q[1];
            });
            return vector<vector<int>>(points.begin(), points.begin() + K);
        }
    };
    

You may also implement the underlying quickselect algorithm yourself.

    
    
    class Solution {
    public:
        vector<vector<int>> kClosest(vector<vector<int>>& points, int K) {
            int l = 0, r = points.size() - 1;
            while (true) {
                int p = partition(points, l, r);
                if (p == K - 1) {
                    break;
                }
                if (p < K - 1) {
                    l = p + 1;
                } else {
                    r = p - 1;
                }
            }
            return vector<vector<int>>(points.begin(), points.begin() + K);
        }
    private:
        bool farther(vector<int>& p, vector<int>& q) {
            return p[0] * p[0] + p[1] * p[1] > q[0] * q[0] + q[1] * q[1];
        }
        
        bool closer(vector<int>& p, vector<int>& q) {
            return p[0] * p[0] + p[1] * p[1] < q[0] * q[0] + q[1] * q[1];
        }
        
        int partition(vector<vector<int>>& points, int left, int right) {
            int pivot = left, l = left + 1, r = right;
            while (l <= r) {
                if (farther(points[l], points[pivot]) && closer(points[r], points[pivot])) {
                    swap(points[l++], points[r--]);
                }
                if (!farther(points[l], points[pivot])) {
                    l++;
                }
                if (!closer(points[r], points[pivot])) {
                    r--;
                }
            }
            swap(points[pivot], points[r]);
            return r;
        }
    };
    

If you would not like to modify `points`, you may maintain the `K` closest
points so far in a separate data structure. We can use a **max** heap to
maintain the `K` closest points. A max heap has its largest element in the
root. Each time we add a point to the heap, if its size exceeds `K`, we pop
the root, which means we get rid of the farthest point and keep the closest
ones. This solution is also of `O(nlogK)` time.

    
    
     class Solution {
    public:
        vector<vector<int>> kClosest(vector<vector<int>>& points, int K) {
            priority_queue<vector<int>, vector<vector<int>>, compare> pq;
            for (vector<int>& point : points) {
                pq.push(point);
                if (pq.size() > K) {
                    pq.pop();
                }
            }
            vector<vector<int>> ans;
            while (!pq.empty()) {
                ans.push_back(pq.top());
                pq.pop();
            }
            return ans;
        }
    private:
        struct compare {
            bool operator()(vector<int>& p, vector<int>& q) {
                return p[0] * p[0] + p[1] * p[1] < q[0] * q[0] + q[1] * q[1];
            }
        };
    };
    

We can also use a min heap. A min heap has the smallest element in the root.
We add all the points to the heap, and then pop the first `K` ones, we are
just the closest ones. This makes the code shorter. Now this one is of `O(n +
Klogn)` time. The `n` part is on adding all points to the heap (building a min
heap for all the points) and the `Klogn` part is on fetching the top `K`
points from the heap.

    
    
    class Solution {
    public:
        vector<vector<int>> kClosest(vector<vector<int>>& points, int K) {
            priority_queue<vector<int>, vector<vector<int>>, compare> pq(points.begin(), points.end());
            vector<vector<int>> ans;
            for (int i = 0; i < K; i++) {
                ans.push_back(pq.top());
                pq.pop();
            }
            return ans;
        }
    private:
        struct compare {
            bool operator()(vector<int>& p, vector<int>& q) {
                return p[0] * p[0] + p[1] * p[1] > q[0] * q[0] + q[1] * q[1];
            }
        };
    };
    

Note that for `priority_queue`, if you would like to use it as a max heap, the
comparator should be `<` and if as a min heap, the comparator is `>`.

Max/min heaps can also be implemented using `multiset`. For `multiset`, max
heap has `>` and min heap has `<` in the comparator. The following two
solutions are respectively max/min heap using `multiset`.

    
    
    class Solution {
    public:
        vector<vector<int>> kClosest(vector<vector<int>>& points, int K) {
            multiset<vector<int>, compare> mset;
            for (vector<int>& point : points) {
                mset.insert(point);
                if (mset.size() > K) {
                    mset.erase(mset.begin());
                }
            }
            vector<vector<int>> ans;
            copy_n(mset.begin(), K, back_inserter(ans));
            return ans;
        }
    private:
        struct compare {
            bool operator()(const vector<int>& p, const vector<int>& q) const {
                return p[0] * p[0] + p[1] * p[1] > q[0] * q[0] + q[1] * q[1];
            }
        };
    };
    
    
    
    class Solution {
    public:
        vector<vector<int>> kClosest(vector<vector<int>>& points, int K) {
            multiset<vector<int>, compare> mset(points.begin(), points.end());
            vector<vector<int>> ans;
            copy_n(mset.begin(), K, back_inserter(ans));
            return ans;
        }
    private:
        struct compare {
            bool operator()(const vector<int>& p, const vector<int>& q) const {
                return p[0] * p[0] + p[1] * p[1] < q[0] * q[0] + q[1] * q[1];
            }
        };
    };
    


### Solution 3
Sort all points and return `K` first, `O(NlogN)`  
 **Java:**

    
    
        public  int[][] kClosest(int[][] points, int K) {
            Arrays.sort(points, Comparator.comparing(p -> p[0] * p[0] + p[1] * p[1]));
            return Arrays.copyOfRange(points, 0, K);
        }
    

**C++:**

    
    
         vector<vector<int>> kClosest(vector<vector<int>>& points, int K) {
            sort(points.begin(), points.end(), [](vector<int>& a, vector<int>& b) {
                return a[0] * a[0] + a[1] * a[1] < b[0] * b[0] + b[1] * b[1];
            });
            return vector<vector<int>>(points.begin(), points.begin() + K);
        }
    

Sort using heap of size `K`, `O(NlogK)`  
**Python:**

    
    
        def kClosest( self, points, K):
            return heapq.nsmallest(K, points, lambda (x, y): x * x + y * y)
    



