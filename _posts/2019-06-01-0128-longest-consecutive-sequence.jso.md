---
layout: post
title: 128. Longest Consecutive Sequence
---
### Question
Given an unsorted array of integers, find the length of the longest
consecutive elements sequence.

Your algorithm should run in O( _n_ ) complexity.

 **Example:**

    
    
     **Input:**  [100, 4, 200, 1, 3, 2]
    **Output:** 4
    **Explanation:** The longest consecutive elements sequence is [1, 2, 3, 4]. Therefore its length is 4.
    

### Solution 1
We will use HashMap. The key thing is to keep track of the sequence length and
store that in the boundary points of the sequence. For example, as a result,
for sequence {1, 2, 3, 4, 5}, map.get(1) and map.get(5) should both return 5.

Whenever a new element **n** is inserted into the map, do two things:

  1. See if **n - 1** and **n + 1** exist in the map, and if so, it means there is an existing sequence next to **n**. Variables **left** and **right** will be the length of those two sequences, while **0** means there is no sequence and **n** will be the boundary point later. Store **(left + right + 1)** as the associated value to key **n** into the map.
  2. Use **left** and **right** to locate the other end of the sequences to the left and right of **n** respectively, and replace the value with the new length.

Everything inside the **for** loop is O(1) so the total time is O(n). Please
comment if you see something wrong. Thanks.

    
    
     public int longestConsecutive(int[] num) {
        int res = 0;
        HashMap<Integer, Integer> map = new HashMap<Integer, Integer>();
        for (int n : num) {
            if (!map.containsKey(n)) {
                int left = (map.containsKey(n - 1)) ? map.get(n - 1) : 0;
                int right = (map.containsKey(n + 1)) ? map.get(n + 1) : 0;
                // sum: length of the sequence n is in
                int sum = left + right + 1;
                map.put(n, sum);
                
                // keep track of the max length 
                res = Math.max(res, sum);
                
                // extend the length to the boundary(s)
                // of the sequence
                // will do nothing if n has no neighbors
                map.put(n - left, sum);
                map.put(n + right, sum);
            }
            else {
                // duplicates
                continue;
            }
        }
        return res;
    }


### Solution 2
First turn the input into a _set_ of numbers. That takes O(n) and then we can
ask in O(1) whether we have a certain number.

Then go through the numbers. If the number x is the start of a streak (i.e.,
x-1 is not in the set), then test y = x+1, x+2, x+3, ... and stop at the first
number y _not_ in the set. The length of the streak is then simply y-x and we
update our global best with that. Since we check each streak only once, this
is overall O(n). This ran in 44 ms on the OJ, one of the fastest Python
submissions.

    
    
    def longestConsecutive( self, nums):
        nums = set(nums)
        best = 0
        for x in nums:
            if x - 1 not in nums:
                y = x + 1
                while y in nums:
                    y += 1
                best = max(best, y - x)
        return best


### Solution 3
use a hash map to store boundary information of consecutive sequence for each
element; there are 4 cases when a new element `i` reached:

  1. neither `i+1` nor `i-1` has been seen: `m[i]=1`;

  2. both `i+1` and `i-1` have been seen: extend `m[i+m[i+1]]` and `m[i-m[i-1]]` to each other;

  3. only `i+1` has been seen: extend `m[i+m[i+1]]` and `m[i]` to each other;

  4. only `i-1` has been seen: extend `m[i-m[i-1]]` and `m[i]` to each other.

    
    
    int longestConsecutive(vector<int> &num) {
        unordered_map<int, int> m;
        int r = 0;
        for (int i : num) {
            if (m[i]) continue;
            r = max(r, m[i] = m[i + m[i + 1]] = m[i - m[i - 1]] = m[i + 1] + m[i - 1] + 1);
        }
        return r;
    }
    



