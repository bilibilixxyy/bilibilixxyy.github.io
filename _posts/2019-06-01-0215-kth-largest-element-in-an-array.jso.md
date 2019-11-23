---
layout: post
title: 215. Kth Largest Element in an Array
---
### Question
Find the **k** th largest element in an unsorted array. Note that it is the
kth largest element in the sorted order, not the kth distinct element.

 **Example 1:**

    
    
     **Input:** [3,2,1,5,6,4] and k = 2
    **Output:** 5
    

**Example 2:**

    
    
    **Input:** [3,2,3,1,2,4,5,5,6] and k = 4
    **Output:** 4

 **Note:**  
You may assume k is always valid, 1 ≤ k ≤ array's length.

### Solution 1
This problem is well known and quite often can be found in various text books.

You can take a couple of approaches to actually solve it:

  * O(N lg N) running time + O(1) memory

The simplest approach is to sort the entire input array and then access the
element by it's index (which is O(1)) operation:

* * *
    
    
    public int findKthLargest(int[] nums, int k) {
            final int N = nums.length;
            Arrays.sort(nums);
            return nums[N - k];
    }
    

* * *

  * O(N lg K) running time + O(K) memory

Other possibility is to use a min oriented priority queue that will store the
K-th largest values. The algorithm iterates over the whole input and maintains
the size of priority queue.

* * *
    
    
    public int findKthLargest(int[] nums, int k) {
    
        final PriorityQueue<Integer> pq = new PriorityQueue<>();
        for(int val : nums) {
            pq.offer(val);
    
            if(pq.size() > k) {
                pq.poll();
            }
        }
        return pq.peek();
    }
    

* * *

  * O(N) best case / O(N^2) worst case running time + O(1) memory

The smart approach for this problem is to use the selection algorithm (based
on the partion method - the same one as used in quicksort).

* * *
    
    
    public int findKthLargest(int[] nums, int k) {
    
            k = nums.length - k;
            int lo = 0;
            int hi = nums.length - 1;
            while (lo < hi) {
                final int j = partition(nums, lo, hi);
                if(j < k) {
                    lo = j + 1;
                } else if (j > k) {
                    hi = j - 1;
                } else {
                    break;
                }
            }
            return nums[k];
        }
    
        private int partition(int[] a, int lo, int hi) {
    
            int i = lo;
            int j = hi + 1;
            while(true) {
                while(i < hi && less(a[++i], a[lo]));
                while(j > lo && less(a[lo], a[--j]));
                if(i >= j) {
                    break;
                }
                exch(a, i, j);
            }
            exch(a, lo, j);
            return j;
        }
    
        private void exch(int[] a, int i, int j) {
            final int tmp = a[i];
            a[i] = a[j];
            a[j] = tmp;
        }
    
        private boolean less(int v, int w) {
            return v < w;
        }
    

* * *

O(N) guaranteed running time + O(1) space

So how can we improve the above solution and make it O(N) guaranteed? The
answer is quite simple, we can randomize the input, so that even when the
worst case input would be provided the algorithm wouldn't be affected. So all
what it is needed to be done is to shuffle the input.

* * *
    
    
    public int findKthLargest(int[] nums, int k) {
    
            shuffle(nums);
            k = nums.length - k;
            int lo = 0;
            int hi = nums.length - 1;
            while (lo < hi) {
                final int j = partition(nums, lo, hi);
                if(j < k) {
                    lo = j + 1;
                } else if (j > k) {
                    hi = j - 1;
                } else {
                    break;
                }
            }
            return nums[k];
        }
    
    private void shuffle(int a[]) {
    
            final Random random = new Random();
            for(int ind = 1; ind < a.length; ind++) {
                final int r = random.nextInt(ind + 1);
                exch(a, ind, r);
            }
        }
    

* * *

There is also worth mentioning the Blum-Floyd-Pratt-Rivest-Tarjan algorithm
that has a guaranteed O(N) running time.


### Solution 2
#### STL

This problem needs to use partial sorting. In STL, there are two built-in
functions (`nth_element` and `partial_sort`) for this.

    
    
    class Solution {
    public:
        int findKthLargest(vector<int>& nums, int k) {
            nth_element(nums.begin(), nums.begin() + k - 1, nums.end(), greater<int>());
            return nums[k - 1];
        }
    };
    
    
    
    class Solution {
    public:
        int findKthLargest(vector<int>& nums, int k) {
            partial_sort(nums.begin(), nums.begin() + k, nums.end(), greater<int>());
            return nums[k - 1];
        }
    };
    

Note the off-by-1 difference in the second argument of the two built-in
functions.

We may also use a heap to solve this problem. We can maintain the largest `k`
elements in a heap with the smallest among them at the top. Or we can add all
the elements to a heap, with the largest at the top, and then pop the heap for
`k - 1` times, then the one on the top is our target. The first one is min-
heap and the second one is max-heap. In STL, both `priority_queue` and
`multiset` can be used as a min/max-heap.

**min-heap using`priority_queue`**

    
    
     class Solution {
    public:
        int findKthLargest(vector<int>& nums, int k) {
            priority_queue<int, vector<int>, greater<int>> pq;
            for (int num : nums) {
                pq.push(num);
                if (pq.size() > k) {
                    pq.pop();
                }
            }
            return pq.top();
        }
    };
    

**max-heap using`priority_queue`**

    
    
     class Solution {
    public:
        int findKthLargest(vector<int>& nums, int k) {
            priority_queue<int> pq(nums.begin(), nums.end());
            for (int i = 0; i < k - 1; i++) {
                pq.pop();
            }
            return pq.top();
        }
    };
    

**min-heap using`multiset`**

    
    
     class Solution {
    public:
        int findKthLargest(vector<int>& nums, int k) {
            multiset<int> mset;
            for (int num : nums) {
                mset.insert(num);
                if (mset.size() > k) {
                    mset.erase(mset.begin());
                }
            }
            return *mset.begin();
        }
    };
    

**max-heap using`multiset`**

    
    
     class Solution {
    public:
        int findKthLargest(vector<int>& nums, int k) {
            multiset<int, greater<int>> mset(nums.begin(), nums.end());
            for (int i = 0; i < k - 1; i++) {
                mset.erase(mset.begin());
            }
            return *mset.begin();
        }
    };
    

#### Partition

The partition subroutine of quicksort can also be used to solve this problem.
In `partition`, we divide the array into

    
    
    elements>=pivot pivot elements<=pivot
    

Then, according to the index of `pivot`, we will know whther the `k`th largest
element is to the left or right of `pivot` or just itself.

In average, this algorithm reduces the size of the problem by approximately
one half after each partition, giving the recurrence `T(n) = T(n/2) + O(n)`
with `O(n)` being the time for partition. The solution is `T(n) = O(n)`, which
means we have found an average linear-time solution. However, in the worst
case, the recurrence will become `T(n) = T(n - 1) + O(n)` and `T(n) = O(n^2)`.

    
    
    class Solution {
    public:
        int findKthLargest(vector<int>& nums, int k) {
            int left = 0, right = nums.size() - 1, kth;
            while (true) {
                int idx = partition(nums, left, right);
                if (idx == k - 1) {
                    kth = nums[idx];
                    break;
                }
                if (idx < k - 1) {
                    left = idx + 1;
                } else {
                    right = idx - 1;
                }
            }
            return kth;
        }
    private:
        int partition(vector<int>& nums, int left, int right) {
            int pivot = nums[left], l = left + 1, r = right;
            while (l <= r) {
                if (nums[l] < pivot && nums[r] > pivot) {
                    swap(nums[l++], nums[r--]);
                }
                if (nums[l] >= pivot) {
                    l++;
                }
                if (nums[r] <= pivot) {
                    r--;
                }
            }
            swap(nums[left], nums[r]);
            return r;
        }
    };
    

#### Heapsort

In the above we have presented heap solutions using STL. You may also
implement your own heap if you are interested. I suggest you to read the
Heapsort chapter of Introduction to Algorithms if you are not familiar with
it. The following code implements a max-heap.

    
    
    class Solution {
    public:
        int findKthLargest(vector<int>& nums, int k) {
            buildMaxHeap(nums);
            for (int i = 0; i < k - 1; i++) {
                swap(nums[0], nums[--heapSize]);
                maxHeapify(nums, 0);
            }
            return nums[0];
        }
    private:
        int heapSize;
        
        int left(int i) {
            return (i << 1) + 1;
        }
        
        int right(int i) {
            return (i << 1) + 2;
        }
        
        void maxHeapify(vector<int>& nums, int i) {
            int largest = i, l = left(i), r = right(i);
            if (l < heapSize && nums[l] > nums[largest]) {
                largest = l;
            }
            if (r < heapSize && nums[r] > nums[largest]) {
                largest = r;
            }
            if (largest != i) {
                swap(nums[i], nums[largest]);
                maxHeapify(nums, largest);
            }
        }
        
        void buildMaxHeap(vector<int>& nums) {
            heapSize = nums.size();
            for (int i = (heapSize >> 1) - 1; i >= 0; i--) {
                maxHeapify(nums, i);
            }
        }
    };
    


### Solution 3
<https://en.wikipedia.org/wiki/Quickselect>

    
    
    public class Solution {
      
      public int findKthLargest(int[] a, int k) {
        int n = a.length;
        int p = quickSelect(a, 0, n - 1, n - k + 1);
        return a[p];
      }
      
      // return the index of the kth smallest number
      int quickSelect(int[] a, int lo, int hi, int k) {
        // use quick sort's idea
        // put nums that are <= pivot to the left
        // put nums that are  > pivot to the right
        int i = lo, j = hi, pivot = a[hi];
        while (i < j) {
          if (a[i++] > pivot) swap(a, --i, --j);
        }
        swap(a, i, hi);
        
        // count the nums that are <= pivot from lo
        int m = i - lo + 1;
        
        // pivot is the one!
        if (m == k)     return i;
        // pivot is too big, so it must be on the left
        else if (m > k) return quickSelect(a, lo, i - 1, k);
        // pivot is too small, so it must be on the right
        else            return quickSelect(a, i + 1, hi, k - m);
      }
      
      void swap(int[] a, int i, int j) {
        int tmp = a[i];
        a[i] = a[j];
        a[j] = tmp;
      }
    
    }



