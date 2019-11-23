---
layout: post
title: 34. Find First and Last Position of Element in Sorted Array
---
### Question
Given an array of integers `nums` sorted in ascending order, find the starting
and ending position of a given `target` value.

Your algorithm's runtime complexity must be in the order of _O_ (log _n_ ).

If the target is not found in the array, return `[-1, -1]`.

 **Example 1:**

    
    
     **Input:** nums = [5,7,7,8,8,10], target = 8
    **Output:** [3,4]

**Example 2:**

    
    
    **Input:** nums = [5,7,7,8,8,10], target = 6
    **Output:** [-1,-1]

### Solution 1
The problem can be simply broken down as two binary searches for the begining
and end of the range, respectively:

First let's find the left boundary of the range. We initialize the range to
[i=0, j=n-1]. In each step, calculate the middle element [mid = (i+j)/2]. Now
according to the relative value of A[mid] to target, there are three
possibilities:

  1. If A[mid] < target, then the range must begins on the _**right**_ of mid (hence i = mid+1 for the next iteration)
  2. If A[mid] > target, it means the range must begins on the _**left**_ of mid (j = mid-1)
  3. If A[mid] = target, then the range must begins _**on the left of or at**_ mid (j= mid)

Since we would move the search range to the same side for case 2 and 3, we
might as well merge them as one single case so that less code is needed:

2*. If A[mid] >= target, j = mid;

Surprisingly, 1 and 2* are the only logic you need to put in loop while (i <
j). When the while loop terminates, the value of i/j is where the start of the
range is. Why?

No matter what the sequence originally is, as we narrow down the search range,
eventually we will be at a situation where there are only two elements in the
search range. Suppose our target is 5, then we have only 7 possible cases:

    
    
    case 1: [5 7] (A[i] = target < A[j])
    case 2: [5 3] (A[i] = target > A[j])
    case 3: [5 5] (A[i] = target = A[j])
    case 4: [3 5] (A[j] = target > A[i])
    case 5: [3 7] (A[i] < target < A[j])
    case 6: [3 4] (A[i] < A[j] < target)
    case 7: [6 7] (target < A[i] < A[j])
    

For case 1, 2 and 3, if we follow the above rule, since mid = i => A[mid] =
target in these cases, then we would set j = mid. Now the loop terminates and
i and j both point to the first 5.

For case 4, since A[mid] < target, then set i = mid+1. The loop terminates and
both i and j point to 5.

For all other cases, by the time the loop terminates, A[i] is not equal to 5.
So we can easily know 5 is not in the sequence if the comparison fails.

In conclusion, when the loop terminates, if A[i]==target, then i is the left
boundary of the range; otherwise, just return -1;

For the right of the range, we can use a similar idea. Again we can come up
with several rules:

  1. If A[mid] > target, then the range must begins on the _**left**_ of mid (j = mid-1)
  2. If A[mid] < target, then the range must begins on the _**right**_ of mid (hence i = mid+1 for the next iteration)
  3. If A[mid] = target, then the range must begins _**on the right of or at**_ mid (i= mid)

Again, we can merge condition 2 and 3 into:

    
    
    2* If A[mid] <= target, then i = mid;
    

However, the terminate condition on longer works this time. Consider the
following case:

    
    
    [5 7], target = 5
    

Now A[mid] = 5, then according to rule 2, we set i = mid. This practically
does nothing because i is already equal to mid. As a result, the search range
is not moved at all!

The solution is by using a small trick: instead of calculating mid as mid =
(i+j)/2, we now do:

    
    
    mid = (i+j)/2+1
    

Why does this trick work? When we use mid = (i+j)/2, the mid is rounded to the
lowest integer. In other words, mid is always _biased_ towards the left. This
means we could have i == mid when j - i == mid, but we NEVER have j == mid. So
in order to keep the search range moving, you must make sure the new i is set
to something different than mid, otherwise we are at the risk that i gets
stuck. But for the new j, it is okay if we set it to mid, since it was not
equal to mid anyways. Our two rules in search of the left boundary happen to
satisfy these requirements, so it works perfectly in that situation.
Similarly, when we search for the right boundary, we must make sure i won't
get stuck when we set the new i to i = mid. The easiest way to achieve this is
by making mid _biased_ to the right, i.e. mid = (i+j)/2+1.

All this reasoning boils down to the following simple code:

    
    
    vector<int> searchRange(int A[], int n, int target) {
        int i = 0, j = n - 1;
        vector<int> ret(2, -1);
        // Search for the left one
        while (i < j)
        {
            int mid = (i + j) /2;
            if (A[mid] < target) i = mid + 1;
            else j = mid;
        }
        if (A[i]!=target) return ret;
        else ret[0] = i;
        
        // Search for the right one
        j = n-1;  // We don't have to set i to 0 the second time.
        while (i < j)
        {
            int mid = (i + j) /2 + 1;	// Make mid biased to the right
            if (A[mid] > target) j = mid - 1;  
            else i = mid;				// So that this won't make the search range stuck.
        }
        ret[1] = j;
        return ret; 
    }


### Solution 2
    
    
    public class Solution {
    	public int[] searchRange(int[] A, int target) {
    		int start = Solution.firstGreaterEqual(A, target);
    		if (start == A.length || A[start] != target) {
    			return new int[]{-1, -1};
    		}
    		return new int[]{start, Solution.firstGreaterEqual(A, target + 1) - 1};
    	}
    
    	//find the first number that is greater than or equal to target.
    	//could return A.length if target is greater than A[A.length-1].
    	//actually this is the same as lower_bound in C++ STL.
    	private static int firstGreaterEqual(int[] A, int target) {
    		int low = 0, high = A.length;
    		while (low < high) {
    			int mid = low + ((high - low) >> 1);
    			//low <= mid < high
    			if (A[mid] < target) {
    				low = mid + 1;
    			} else {
    				//should not be mid-1 when A[mid]==target.
    				//could be mid even if A[mid]>target because mid<high.
    				high = mid;
    			}
    		}
    		return low;
    	}
    }


### Solution 3
Solution 1 : **Divide and Conquer with early breaks** : 56 ms

The O(log n) time isn't quite obvious, so I'll explain it below. Or you can
take the challenge and prove it yourself :-)

    
    
    def searchRange(self, nums, target):
        def search(lo, hi):
            if nums[lo] == target == nums[hi]:
                return [lo, hi]
            if nums[lo] <= target <= nums[hi]:
                mid = (lo + hi) / 2
                l, r = search(lo, mid), search(mid+1, hi)
                return max(l, r) if -1 in l+r else [l[0], r[1]]
            return [-1, -1]
        return search(0, len(nums)-1)
    

The `search` helper function returns an index range just like the requested
`searchRange` function, but only searches in `nums[lo..hi]`. It first compares
the end points and immediately returns `[lo, hi]` if that whole part of `nums`
is full of `target`, and immediately returns `[-1, -1]` if `target` is outside
the range. The interesting case is when `target` can be in the range but
doesn't fill it completely.

In that case, we split the range in left and right half, solve them
recursively, and combine their results appropriately. Why doesn't this explode
exponentially? Well, let's call the numbers in the left half `A, ..., B` and
the numbers in the right half `C, ..., D`. Now if one of them immediately
return their `[lo, hi]` or `[-1, -1]`, then this doesn't explode. And if
neither immediately returns, that means we have `A <= target <= B` and `C <=
target <= D`. And since `nums` is sorted, we actually have `target <= B <= C
<= target`, so `B = C = target`. **The left half thus ends with`target` and
the right half starts with it.** I highlight that because it's important. Now
consider what happens further. The left half gets halved again. Call the
middle elements `a` and `b`, so the left half is `A, ..., a, b, ..., B`. Then
`a <= target` and:

  * If `a < target`, then the call analyzing `A, ..., a` immediately returns `[-1, -1]` and we only look further into `b, ..., B` **which is again a part that ends with`target`**.
  * If `a == target`, then `a = b = ... = B = target` and thus the call analyzing `b, ..., B` immediately returns its `[lo, hi]` and we only look further into `A, ..., a` **which is again a part that ends with`target`**.

Same for the right half `C, ..., D`. So in the beginning of the search, as
long as `target` is only in at most one of the two halves (so the other
immediately stops), we have a single path. And if we ever come across the case
where `target` is in both halves, then we split into _two_ paths, but then
each of those remains a single path. And both paths are only O(log n) long, so
we have overall runtime O(log n).

This is btw based on [us917's solution](https://leetcode.com/discuss/4238/can-
solution-be-obtained-in-one-pass?show=4425#a4425).

* * *

Solution 2 : **Two binary searches** : 56 ms

    
    
    def searchRange(self, nums, target):
        def  search(n):
            lo, hi = 0, len(nums)
            while lo < hi:
                mid = (lo + hi) / 2
                if nums[mid] >= n:
                    hi = mid
                else:
                    lo = mid + 1
            return lo
        lo = search(target)
        return [lo, search(target+1)-1] if target in nums[lo:lo+1] else [-1, -1]
    

Here, my helper function is a simple binary search, telling me the first index
where I could insert a number `n` into `nums` to keep it sorted. Thus, if
`nums` contains `target`, I can find the first occurrence with
`search(target)`. I do that, and if `target` isn't actually there, then I
return `[-1, -1]`. Otherwise, I ask `search(target+1)`, which tells me the
first index where I could insert `target+1`, which of course is one index
behind the last index containing `target`, so all I have left to do is
subtract 1.

* * *

Solution 3 : **Two binary searches, using the library**

Binary search is so good and common that many languages have it in their
standard library and you just need to figure out how to apply it to the
problem at hand.

**Python:**

    
    
    def search Range(self, nums, target):
        lo = bisect.bisect_left(nums, target)
        return [lo, bisect.bisect(nums, target)-1] if target in nums[lo:lo+1] else [-1, -1]
    

**C++:**

    
    
    vector< int> searchRange(vector<int>& nums, int target) {
        auto bounds = equal_range(nums.begin(), nums.end(), target);
        if (bounds.first == bounds.second)
            return {-1, -1};
        return {bounds.first - nums.begin(), bounds.second - nums.begin() - 1};
    }
    

Or:

    
    
    vector<int> searchRange(vector<int>& nums, int target) {
        int lo = lower_bound(nums.begin(), nums.end(), target) - nums.begin();
        if (lo == nums.size() || nums[lo] != target)
            return {-1, -1};
        int hi = upper_bound(nums.begin(), nums.end(), target) - nums.begin() - 1;
        return {lo, hi};
    }
    

**Java:**

Well, Java decided to be annoying and offer `Arrays.binSearch` but with _"If
the array contains multiple elements with the specified value, there is no
guarantee which one will be found"_. So it's useless for us. I'm not good at
Java, though, so maybe I'm overlooking a way to still make it work. If you
manage to do so, please let me know.



