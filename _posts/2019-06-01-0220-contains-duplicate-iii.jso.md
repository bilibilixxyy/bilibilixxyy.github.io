---
layout: post
title: 220. Contains Duplicate III
---
### Question
Given an array of integers, find out whether there are two distinct indices
_i_ and _j_ in the array such that the **absolute** difference between
**nums[i]** and **nums[j]** is at most _t_ and the **absolute** difference
between _i_ and _j_ is at most _k_.

 **Example 1:**

    
    
     **Input:** nums = [1,2,3,1], k = 3, t = 0
    **Output:** true
    

**Example 2:**

    
    
    **Input:** nums = [1,0,1,1], k = 1, t = 2
    **Output:** true
    

**Example 3:**

    
    
    **Input:** nums = [1,5,9,1,5,9], k = 2, t = 3
    **Output:** false
    

### Solution 1
As a followup question, it naturally also requires maintaining a window of
size k. When t == 0, it reduces to the previous question so we just reuse the
solution.

Since there is now a constraint on the range of the values of the elements to
be considered duplicates, it reminds us of doing a range check which is
implemented in tree data structure and would take O(LogN) if a balanced tree
structure is used, or doing a bucket check which is constant time. We shall
just discuss the idea using bucket here.

Bucketing means we map a range of values to the a bucket. For example, if the
bucket size is 3, we consider 0, 1, 2 all map to the same bucket. However, if
t == 3, (0, 3) is a considered duplicates but does not map to the same bucket.
This is fine since we are checking the buckets immediately before and after as
well. So, as a rule of thumb, just make sure the size of the bucket is
reasonable such that elements having the same bucket is immediately considered
duplicates or duplicates must lie within adjacent buckets. So this actually
gives us a range of possible bucket size, i.e. t and t + 1. We just choose it
to be t and a bucket mapping to be _num / t_.

Another complication is that negative ints are allowed. A simple _num / t_
just shrinks everything towards 0. Therefore, we can just reposition every
element to start from Integer.MIN_VALUE.

    
    
      public class Solution {
        public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
            if (k < 1 || t < 0) return false;
            Map<Long, Long> map = new HashMap<>();
            for (int i = 0; i < nums.length; i++) {
                long remappedNum = (long) nums[i] - Integer.MIN_VALUE;
                long bucket = remappedNum / ((long) t + 1);
                if (map.containsKey(bucket)
                        || (map.containsKey(bucket - 1) && remappedNum - map.get(bucket - 1) <= t)
                            || (map.containsKey(bucket + 1) && map.get(bucket + 1) - remappedNum <= t))
                                return true;
                if (map.entrySet().size() >= k) {
                    long lastBucket = ((long) nums[i - k] - Integer.MIN_VALUE) / ((long) t + 1);
                    map.remove(lastBucket);
                }
                map.put(bucket, remappedNum);
            }
            return false;
        }
    }
    

Edits:

Actually, we can use t + 1 as the bucket size to get rid of the case when t ==
0. It simplifies the code. The above code is therefore the updated version.


### Solution 2
This problem requires to maintain a window of size k of the previous values
that can be queried for value ranges. The best data structure to do that is
Binary Search Tree. As a result maintaining the tree of size k will result in
time complexity O(N lg K). In order to check if there exists any value of
range abs(nums[i] - nums[j]) to simple queries can be executed both of time
complexity O(lg K)

Here is the whole solution using TreeMap.

* * *
    
    
    public class Solution {
        public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
            if (nums == null || nums.length == 0 || k <= 0) {
                return false;
            }
    
            final TreeSet<Integer> values = new TreeSet<>();
            for (int ind = 0; ind < nums.length; ind++) {
    
                final Integer floor = values.floor(nums[ind] + t);
                final Integer ceil = values.ceiling(nums[ind] - t);
                if ((floor != null && floor >= nums[ind])
                        || (ceil != null && ceil <= nums[ind])) {
                    return true;
                }
    
                values.add(nums[ind]);
                if (ind >= k) {
                    values.remove(nums[ind - k]);
                }
            }
    
            return false;
        }
    }


### Solution 3
The idea is like the bucket sort algorithm. Suppose we have consecutive
buckets covering the range of nums with each bucket a width of (t+1). If there
are two item with difference <= t, one of the two will happen:

    
    
    (1) the two in the same bucket
    (2) the two in neighbor buckets
    

For detailed explanation see my blog [here](http://algobox.org/contains-
duplicate-iii/)

**Python**

    
    
     def containsNearbyAlmostDuplicate(self, nums, k, t):
        if t < 0: return False
        n = len(nums)
        d = {}
        w = t + 1
        for i in xrange(n):
            m = nums[i] / w
            if m in d:
                return True
            if m - 1 in d and abs(nums[i] - d[m - 1]) < w:
                return True
            if m + 1 in d and abs(nums[i] - d[m + 1]) < w:
                return True
            d[m] = nums[i]
            if i >= k: del d[nums[i - k] / w]
        return False
    
    
    # 30 / 30 test cases passed.
    # Status: Accepted
    # Runtime: 56 ms
    # 93.81%
    

**Java**

    
    
     private long getID(long i, long w) {
        return i < 0 ? (i + 1) / w - 1 : i / w;
    }
    
    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        if (t < 0) return false;
        Map<Long, Long> d = new HashMap<>();
        long w = (long)t + 1;
        for (int i = 0; i < nums.length; ++i) {
            long m = getID(nums[i], w);
            if (d.containsKey(m))
                return true;
            if (d.containsKey(m - 1) && Math.abs(nums[i] - d.get(m - 1)) < w)
                return true;
            if (d.containsKey(m + 1) && Math.abs(nums[i] - d.get(m + 1)) < w)
                return true;
            d.put(m, (long)nums[i]);
            if (i >= k) d.remove(getID(nums[i - k], w));
        }
        return false;
    }



