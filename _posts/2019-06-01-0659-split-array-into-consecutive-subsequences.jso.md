---
layout: post
title: 659. Split Array into Consecutive Subsequences
---
### Question
You are given an integer array sorted in ascending order (may contain
duplicates), you need to split them into several subsequences, where each
subsequences consist of at least 3 consecutive integers. Return whether you
can make such a split.

 **Example 1:**  

    
    
     **Input:** [1,2,3,3,4,5]
    **Output:** True
    **Explanation:**
    You can split them into two consecutive subsequences : 
    1, 2, 3
    3, 4, 5
    

**Example 2:**  

    
    
    **Input:** [1,2,3,3,4,4,5,5]
    **Output:** True
    **Explanation:**
    You can split them into two consecutive subsequences : 
    1, 2, 3, 4, 5
    3, 4, 5
    

**Example 3:**  

    
    
    **Input:** [1,2,3,4,4,5]
    **Output:** False
    

**Note:**  

  1. The length of the input is in range of [1, 10000]

### Solution 1
  1. We iterate through the array once to get the frequency of all the elements in the array
  2. We iterate through the array once more and for each element we either see if it can be appended to a previously constructed consecutive sequence or if it can be the start of a new consecutive sequence. If neither are true, then we return false.

    
    
    public boolean isPossible(int[] nums) {
        Map<Integer, Integer> freq = new HashMap<>(), appendfreq = new HashMap<>();
        for (int i : nums) freq.put(i, freq.getOrDefault(i,0) + 1);
        for (int i : nums) {
            if (freq.get(i) == 0) continue;
            else if (appendfreq.getOrDefault(i,0) > 0) {
                appendfreq.put(i, appendfreq.get(i) - 1);
                appendfreq.put(i+1, appendfreq.getOrDefault(i+1,0) + 1);
            }   
            else if (freq.getOrDefault(i+1,0) > 0 && freq.getOrDefault(i+2,0) > 0) {
                freq.put(i+1, freq.get(i+1) - 1);
                freq.put(i+2, freq.get(i+2) - 1);
                appendfreq.put(i+3, appendfreq.getOrDefault(i+3,0) + 1);
            }
            else return false;
            freq.put(i, freq.get(i) - 1);
        }
        return true;
    }
    


### Solution 2
The basic idea is that, for each distinct element `ele` in the input array, we
only need to maintain three pieces of information: the number of consecutive
subsequences ending at `ele` with length of `1`, length of `2` and length `>=
3`.

The input array will be scanned linearly from left to right. Let `cur` be the
element currently being examined and `cnt` as its number of appearance. `pre`
is the element processed immediately before `cur`. The number of consecutive
subsequences ending at `pre` with length of `1`, length of `2` and length `>=
3` are denoted as `p1`, `p2` and `p3`, respectively. There are two cases in
general:

  1. `cur != pre + 1`: for this case, `cur` cannot be added to any consecutive subsequences ending at `pre`, therefore, we must have `p1 == 0 && p2 == 0`; otherwise the input array cannot be split into consecutive subsequences of length `>= 3`. Now let `c1, c2, c3` be the number of consecutive subsequences ending at `cur` with length of `1`, length of `2` and length `>= 3`, respectively, we will have `c1 = cnt, c2 = 0, c3 = 0`, which means we only have consecutive subsequence ending at `cur` with length of `1` and its number given by `cnt`.

  2. `cur == pre + 1`: for this case, `cur` can be added to consecutive subsequences ending at `pre` and thus extend those subsequences. But priorities should be given to those with length of `1` first, then length of `2` and lastly length `>= 3`. Also we must have `cnt >= p1 + p2`; otherwise the input array cannot be split into consecutive subsequences of length `>= 3`. Again let `c1, c2, c3` be the number of consecutive subsequences ending at `cur` with length of `1`, length of `2` and length `>= 3`, respectively, we will have: `c2 = p1, c3 = p2 + min(p3, cnt - (p1 + p2)), c1 = max(cnt - (p1 + p2 + p3), 0)`. The meaning is as follows: first adding `cur` to the end of subsequences of length `1` will make them subsequences of length `2`, and we have `p1` such subsequences, therefore `c2 = p1`. Then adding `cur` to the end of subsequences of length `2` will make them subsequences of length `3`, and we have `p2` such subsequences, therefore `c3` is at least `p2`. If `cnt > p1 + p2`, we can add the remaining `cur` to the end of subsequences of length `>= 3` to make them even longer subsequences. The number of such subsequences is the smaller one of `p3` and `cnt - (p1 + p2)`. In total, `c3 = p2 + min(p3, cnt - (p1 + p2))`. If `cnt > p1 + p2 + p3`, then we still have remaining `cur` that cannot be added to any subsequences. These residual `cur` will form subsequences of length `1`, hence `c1 = max(cnt - (p1 + p2 + p3), 0)`.

For either case, we need to update: `pre = cur, p1 = c1, p2 = c2, p3 = c3`
after processing the current element. When all the elements are done, we check
the values of `p1` and `p2`. The input array can be split into consecutive
subsequences of length `>= 3` if and only if `p1 == 0 && p2 == 0`.

Here is the `O(n)` time and `O(1)` space Java solution:

    
    
    public boolean isPossible(int[] nums) {
        int pre = Integer.MIN_VALUE, p1 = 0, p2 = 0, p3 = 0;
        int cur = 0, cnt = 0, c1 = 0, c2 = 0, c3 = 0;
            
        for (int i = 0; i < nums.length; pre = cur, p1 = c1, p2 = c2, p3 = c3) {
            for (cur = nums[i], cnt = 0; i < nums.length && cur == nums[i]; cnt++, i++);
                
            if (cur != pre + 1) {
                if (p1 != 0 || p2 != 0) return false;
                c1 = cnt; c2 = 0; c3 = 0;
                
            } else {
                if (cnt < p1 + p2) return false;
                c1 = Math.max(0, cnt - (p1 + p2 + p3));
                c2 = p1;
                c3 = p2 + Math.min(p3, cnt - (p1 + p2));
            }
        }
        
        return (p1 == 0 && p2 == 0);
    }
    

  

* * *

**PS:** in case you're curious, here is how I come up with this solution.

First note that if the array can be split into consecutive subsequences of
length `>= 3`, then every element in the array must belong to such a
subsequence of length `>= 3`. So let's take any integer `m` in the array as an
example. Apparently you can only add it to consecutive subsequences ending at
`m - 1`. This tells you that we need information about consecutive
subsequences ending at `m - 1`. But what kind of information about those
subsequences are relevant here?

Think about what distinguishes one subsequence from another. Since all the
subsequences are ending at `m - 1`, they can only differ by length, i.e., each
subsequence will be uniquely identified by its length. Now what if two
subsequences have the same length? This suggests that we also need to keep
track of the number of appearance of each length. Therefore in summary, we
need to maintain information of subsequences ending at `m - 1` with various
lengths and their corresponding number of appearance.

Of course I know we cannot keep track of all lengths, as this would require an
infinite amount of memory. So the next question is to ponder more carefully
the role that the length of each subsequence is playing here. From the point
view of `m - 1`, we care more about subsequences of length `1` and `2`. Since
if there are no other elements that can extend these subsequences, we know the
input array cannot be split into consecutive subsequences of length `>= 3`. On
the other hand, we don't really care about subsequences of length `>= 3`,
since they already meet the required condition. So I conclude that for
subsequences ending at `m - 1`, there are really only three types of length
information needed: those of length `1`, of length `2` and of length `>= 3`.

Once I figure this out, the next thing is to extend the conclusion to
subsequences ending at `m`, so we can have a working recurrence relation. The
key here is how we add `m` to those subsequences ending at `m - 1`. As I
mentioned above, priorities should be given to those of length `1` and `2`,
since if these subsequences cannot be extended, the input array cannot be
split into consecutive subsequences of length `>= 3`. After those subsequences
are extended, if we have more elements of `m`, they can be used to extend
subsequences ending at `m - 1` with length `>= 3`. At last, if we still have
elements of `m`, then they will form subsequences of length `1` since there
are no more subsequences ending at `m - 1` available for them. Therefore for
subsequences ending at `m`, we still just need subsequences of length `1`,
length `2` and length `>= 3`. We can continue in this fashion until all
elements are processed, at which point we need to check the number of
subsequences of length `1` and `2` that ends at the last element of the input
array to see if the input array can be split into consecutive subsequences of
length `>= 3` (since these subsequences cannot be extended any longer as there
are no more elements).

Hope this helps!


### Solution 3
I used a greedy algorithm.  
`left`is a hashmap, `left[i]` counts the number of `i` that I haven't placed
yet.  
`end`is a hashmap, `end[i]` counts the number of consecutive subsequences that
ends at number `i`  
Then I tried to split the nums one by one.  
If I could neither add a number to the end of a existing consecutive
subsequence nor find two following number in the left, I returned `False`

    
    
    def isPossible(self, nums):
            left = collections.Counter(nums)
            end = collections.Counter()
            for i in nums:
                if not left[i]: continue
                left[i] -= 1
                if end[i - 1] > 0:
                    end[i - 1] -= 1
                    end[i] += 1
                elif left[i + 1] and left[i + 2]:
                    left[i + 1] -= 1
                    left[i + 2] -= 1
                    end[i + 2] += 1
                else:
                    return False
            return True



