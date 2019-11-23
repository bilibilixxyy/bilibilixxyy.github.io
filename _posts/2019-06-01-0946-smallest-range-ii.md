---
layout: post
title: 946. Smallest Range II
---
### Question
Given an array `A` of integers, for each integer `A[i]` we need to choose
**either  `x = -K` or `x = K`**, and add `x` to `A[i] **(only once)**`.

After this process, we have some array `B`.

Return the smallest possible difference between the maximum value of `B` and
the minimum value of `B`.



 **Example 1:**

    
    
     **Input:** A = [1], K = 0
    **Output:** 0
    **Explanation** : B = [1]
    

**Example 2:**

    
    
    **Input:** A = [0,10], K = 2
    **Output:** 6
    **Explanation** : B = [2,8]
    

**Example 3:**

    
    
    **Input:** A = [1,3,6], K = 3
    **Output:** 3
    **Explanation** : B = [4,6,3]
    



 **Note:**

  1. `1 <= A.length <= 10000`
  2. `0 <= A[i] <= 10000`
  3. `0 <= K <= 10000`

### Solution 1
##  **Intuition** :

For each integer `A[i]`,  
we may choose either `x = -K` or `x = K`.

If we add `K` to all `B[i]`, the result won't change.

It's the same as:  
For each integer `A[i]`, we may choose either `x = 0` or `x = 2 * K`.

##  **Explanation** :

We sort the `A` first, and we choose to add `x = 0` to all `A[i]`.  
Now we have `res = A[n - 1] - A[0]`.  
Starting from the smallest of `A`, we add `2 * K` to `A[i]`,  
hoping this process will reduce the difference.

Update the new `mx = max(mx, A[i] + 2 * K)`  
Update the new `mn = min(A[i + 1], A[0] + 2 * K)`  
Update the `res = min(res, mx - mn)`

##  **Time Complexity** :

`O(NlogN)`, in both of the worst and the best cases.  
In the Extending Reading part, I improve this to `O(N)` in half of cases.

 **C++:**

    
    
         int smallestRangeII(vector<int> A, int K) {
            sort(A.begin(), A.end());
            int n = A.size(), mx = A[n - 1], mn = A[0], res = mx - mn;
            for (int i = 0; i < n - 1; ++i) {
                mx = max(mx, A[i] + 2 * K);
                mn = min(A[i + 1], A[0] + 2 * K);
                res = min(res, mx - mn);
            }
            return res;
        }
    

**Java:**

    
    
         public int smallestRangeII(int[] A, int K) {
            Arrays.sort(A);
            int n = A.length, mx = A[n - 1], mn = A[0], res = mx - mn;
            for (int i = 0; i < n - 1; ++i) {
                mx = Math.max(mx, A[i] + 2 * K);
                mn = Math.min(A[i + 1], A[0] + 2 * K);
                res = Math.min(res, mx - mn);
            }
            return res;
        }
    

**Python:**

    
    
        def smallestRangeII(self, A, K):
            A. sort()
            res = A[-1] - A[0]
            for i in range(len(A) - 1):
                big = max(A[-1], A[i] + 2 * K)
                small = min(A[i + 1], A[0] + 2 * K)
                res = min(res, big - small)
            return res
    

## **Extending Reading:**

I notice that no other posts help me explain this part,  
So I decided to complete it myself.  
  

**Q:** Do we have to sort the list?  
**A:** In general cases, Yes. and this can be easily proved.

For example A = [0,1,4,5,6,7,8,9], K = 5  
result = 2 * K - max(gap between numbers) = 2 * 5 - (4 - 1) = 7

To get the gap of consecutive numbers (in sorted order),  
I don't have a better idea than sortting.

  

**Q:** If we add `2*K` to `A[i]`, why do we need to add `2*K` to `A[0]` ~ `A[i
- 1]`?  
**A:** Yes we need to keep the array **rotated sorted**.

If we add `2*K` to any `A[i]`, now the maximum will be at least `A[i] + 2*K`  
`A[0]` is the minimum and `A[0] + 2K` won't be bigger than `A[i] + 2*K`.

For example A = [0,1,4,5,6,7,8,9], K = 5  
If we add K * 2 = 10 to 5, then we have [0,1,4,15,6,7,8,9],  
we should also add 10 to 0,1 and 4.

In other words, if we add any A[i],  
we should do the same thing to the all smaller numbers,  
and keep the array **rotated sorted**.

  

**Q:** Except the general cases, do we any corner case?  
**A:** Yes, we have.  
if `max(A) - min(A) >= 4 * K`, return `max(A) - min(A) - 2 * K`  
if `max(A) - min(A) <= K`, return `max(A) - min(A)`  
Otherwise, we have to sort.

  

**Q:** Can we optimise this O(NlogN) solution?  
**A:** No.

To be easier understood, I sort the whole list here.  
But we don't have to.  
We can only take and sort he number between `[max(A) - 2 * K, min(A) + 2 * K]`

This will help improve the complexity a lot.  
In 30 of 68 total cases, we solve the problem in O(N).  
In ther rest cases, we solve in O(KlogK) where K <= N.

In python, it will improve from 120ms to 45ms.

    
    
        def smallestRangeII(self, A, K):
            if len(A) == 1: return 0
            ma, mi = max(A), min(A)
            if ma - mi >= 4 * K:
                return ma - mi - 2 * K
            if ma - mi <= 2 * K:
                return ma - mi
            inter = sorted([i for i in A if ma - 2 * K < i < mi + 2 * K] + [ma - 2 * K, mi + 2 * K])
            return min(a + 2 * K - b for a, b in zip(inter, inter[1:]))
    


### Solution 2
  * Sort the vector.
  * Assuming there is a `point`, on the left of the `point`, all elements add K, on the right of the `point`, all elements substract K, check each possible point. (a `point` is between two numbers).

    
    
    class Solution {
    public:
        int smallestRangeII(vector<int>& A, int K) {
            sort(A.begin(), A.end());
            int res = A[A.size() - 1] - A[0];
            int left = A[0] + K, right = A[A.size() - 1] - K;
            for (int i = 0; i < A.size() - 1; i++) {
                int maxi = max(A[i] + K, right), mini = min(left, A[i + 1] - K);
                res = min(res, maxi - mini);
            }
            return res;
        }
    };
    

  * More explanation:  
Cause there are two segments `(A[0]+K, A[1]+K, ..., A[i]+K, A[i+1]-K, ...,
A[n]-K)`, the first one is on the left of the current `point` (`A[i] + K` is
the last element of the first segment), the second one is on the right of the
current `point` (`A[i + 1] - K` is the first element of the second segment).
For the first segment, the smallest element is `left`, the largest is `A[i] +
K`; For the second segment, `A[i + 1] - K` is the smallest element, the
largest is `right`. Thus, for each point, the largest element should be either
`A[i] + K` or `right`, the smallest element should be either `left` or `A[i +
1] - K`.


### Solution 3
This question is a very deep question really, with lots to think about. Before
we get to it, some things must be considered.

## Simple solution

Imagine all the numbers where in asending order. At some point, as we move
from smallest to highest, we will want to change from adding _K_ to
subtracting _K_ , and once changed, not change back. Thus we can produce a
simple greedy solution that simply tries changing direction at each point and
comparing it against the current best (smallest) difference. Thus a simple
solution is;

    
    
      int smallestRangeII(vector<int>& A, int K) {
        std::sort(A.begin(), A.end());
        int ret = A.back() - A.front();
        for (auto first = A.begin(), first2 = first + 1; first2 != A.end(); ++first, ++first2) {
          ret = std::min(ret, std::max(A.back() - K, *first + K) - std::min(A.front() + K, *first2 - K));
        }
        return ret;
      }
    

We're doing a cheaky sort on the input vector to produce an O(nlogn) time,
O(1) space solution.

## The 'simple' O(n) cases

There are two simple cases, where we can return the answer O(n) easily. When
the range is small and when the range is large. First we get the _min_ and
_max_ values using a simple O(n) operation.

  1. If the range is less than or equal to _K_ , then the best we can hope for is moving all elements in the same direction (up or down). The result will be the range stays the same and is our answer.
  2. If the range is greater or equal to 4 times _K_ , then we can add _K_ to all elements in the lower half of the range and subtract _K_ from the upper range. We have removed _K_ from both ends of the range and thus the resulting range is [ _min_ \+ _K_ , _max_ \- _K_ ] and our answer is _max_ \- _K_ \- _min_ \- _K_.

To see _why_ 4 times _K_ allows for a quick answer might not be obvious at
first. Imagine the input numbers where in order and further where divided into
3 groups. [ _min_ , _min_ \+ _K_ ], ( _min_ \+ _K_ , _max_ \- _K_ ), [ _max_
\- _K_ , _max_ ]. It's obvious that we will want to add _K_ to all the
elements in [ _min_ , _min_ \+ _K_ ] and subtract _K_ from all the elements in
[ _max_ \- _K_ , _max_ ]. If we ignore the middle range for the moment, it
follows that the best we can hope for is [ _min_ \+ _K_ , _max_ \- _K_ ].

Now to show that it's actually the answer we need to show all the elements in
the middle range stay within that range once we add or subtract _K_ from each
one. We've taken 2 * _K_ off our original range that we know is _at least_ 4 *
_K_ , so the middle range is _at least_ 2 * _K_. Thus we can add _K_ to the
lower half knowing there is a gap of at least _K_ before we reach the upper
range. Similarly, we can subtract _K_ from the upper half knowing there is a
gap of at least _K_ before we reach the lower range. Therefore we can add or
subtract _K_ from every element while remaining in the range [ _min_ \+ _K_ ,
_max_ \- _K_ ]. Therefore that is our answer.

## The harder cases

### **Narrowing the search**

With the simple cases out of the way, we're trying to find a point between
_min_ and _max_ where we want to change direction. The trick is to decide
_where_ to do this exactly.

Well we can get an idea where to start. We know that we're going to add _K_ to
_min_ , so anything that is greater than 2 * _K_ above _min_ , we can subtract
_K_ from and know that it will not be less than _min_ \+ _K_ and hence not
adversely impact our diff, thus everything greater or equal than 2 * _K_ we
want to subtract _K_ from, and similarly anything less than or equal to _max_
\- 2 * _K_ we want to add _K_ to. We therefore only actually need to consider
elements in the range ( _max_ \- _2K_ , _min_ \+ _2K_ ). We actually need to
consider the element just before and just after as well as we will need these
to test those elements inside the range. Thus we can produce a faster, less
greedy, answer where we only consider these elements.

    
    
      int smallestRangeII(vector<int>& A, int K) {
        auto rng = std::minmax_element(A.begin(), A.end());
        int imin = *rng.first;
        int imax = *rng.second;
        int ret = imax - imin;
        
        // If the minimum and maximum are close together, then the minimum is to simply add K to all elements and hence not change the distance
        if (ret <= K) {
          return ret;
        // If the minimum and maximum are so far apart each edge of the list can just be moved in towards the middle. Moving in K from each side.
        } else if (ret >= 4 * K) {
          return ret - 2 * K;
        }
        
        // Only elements in the middle concern us, they are the only ones that need to move away from the middle and hence we have to decide
        // which direction they must move. We only care about elements in the range [max - 2K, min + 2K], plus the nearest element on either
        // side of the range.
        std::sort(A.begin(), A.end());
        auto first = std::lower_bound(A.begin(), A.end(), imax - 2 * K);
        auto last = std::upper_bound(first, A.end(), imin + 2 * K);
        // Grab one more element (if there is one) on either side of the range, we need these for comparison of our range
        if (first != A.begin()) { --first; }
        if (last != A.end()) { ++last; }
        // Use a greedy algorithm to try every possible element in our range
        for (auto first2 = first + 1; first2 != last; ++first, ++first2) {
          ret = std::min(ret, std::max(imax - K, *first + K) - std::min(imin + K, *first2 - K));
        }
        return ret;
      }
    

Of course we can take advantage of this and produce an answer where we only
sort the required numbers. Thus producing an answer which is log(mlogm) where
_m_ is the number of elements in our reduced range. This has the advantage of
not modifying our input vector, which could be frowned upon.

    
    
       int smallestRangeII(vector<int>& A, int K) {
        auto rng = std::minmax_element(A.begin(), A.end());
        int ret = *rng.second - *rng.first;
        // If the minimum and maximum are close together, then the minimum is to simply add K to all elements and hence not change the distance
        if (ret <= K) {
          return ret;
        // If the minimum and maximum are so far apart each edge of the list can just be moved in towards the middle. Moving in K from each side.
        } else if (ret >= 4 * K) {
          return ret - 2 * K;
        }
       
        // Only elements in the middle concern us, they are the only ones that need to move away from the middle and hence we have to decide
        // which direction they must move. We only care about elements in the range [max - 2K, min + 2K], plus the nearest element on either
        // side of the range.
        int below = *rng.first;
        int after = *rng.second;
        std::vector<int> tmp;
        for (auto num : A) {
          if (num < *rng.second - 2 * K) { below = std::max(below, num); }
          else if (num > *rng.first + 2 * K) { after = std::min(after, num); }
          else { tmp.push_back(num); }
        }
        tmp.push_back(below);
        tmp.push_back(after);
        std::sort(tmp.begin(), tmp.end());
        
        // We're looking for the greatest difference between consecutive instruments.
        for (auto it = tmp.begin(), it2 = it + 1; it2 != tmp.end(); ++it, ++it2) {
          ret = std::min(ret, std::max(*rng.second - K, *it + K) - std::min(*rng.first + K, *it2 - K));
        }
        return ret;
      }
    

### **What are we actually looking for?**

We know there is some element inside our reduced range of [ _max_ \- _2K_ ,
_min_ \+ _2K_ ] is where we change from adding _K_ to subtracting _K_ , but
_what_ exactly makes the target point so special?

Well, we're after the point where our change over will cause the least impact
to our diff. Lets walk through some examples to see if we can see a pattern.
If we have [1, 3, 4, 6, 7] and _K_ of 3 and using our reduced range, we know
it must change at either at 3, 4, or 6. A quick check by hand will confirm
that changing at between 4 and 6 is where the money is at. So our result is
[3, 6, 7, 3, 4] for a difference of 4. The trick should be clear now, but just
to hammer it home, we're looking for the point where there is the biggest jump
in value between consecutive values (if our numbsers where sorted). In case
you're wondering, if there are multiple jumps of the same size, it doesn't
matter which is chosen.

**Keep this in mind, all the above solutions simply find the point, inside our
reduced range, where the jump in value is greatest.**

## What about O(n) time already?

That's a lot to get though, but it's important to know what we're looking for
before we can solve it faster. Obviously we can't just sort the list and find
the jump as the sort is too expensive for an O(n) time solution. What we need
is a fast way to find the biggest jump in an unsorted list. This is where the
magic happens.

Consider _n_ numbers, that are equally spaced. Say 10 to 100, incrementing by
10 [10, 20, 30, 40, 50, 60, 70, 80, 90, 100]. Now if a single number is
increased or decreased, the spacing will change. One higher and one lower
(well dah!). Seems obvious and random, but stay with me.

If we know our _min_ , _max_ and the count of numbers, then we can calculate
what the space between each number will be if they are equally spaced. Thus
any jump _greater_ than that number is what we're interested in as a potential
changeover point.

`(imax - imin) / count = jump`

If we group our numbers into ranges based on _jump_ , we end up with [ _min_ ,
_min_ \+ _jump_ ), [ _min_ \+ _jump_ , _min_ \+ _2jump_ ), [ _min_ \+ _2jump_
, _min_ \+ _3jump_ ), ..., [ _min_ \+ _x_ * _jump_ , _max]. All numbers that
fall inside a range together are irrelivant, as they by definition must be
less than _jump_ distance apart. Thus we actually only care about the maximum
number inside each range compared to the minimum number in the next (non
empty) range. Each of these pairs is the only place that can have a jump
larger than _jump_.

Hence all we actually need to store is the minimum and maximum value that
falls inside each _bucket_... hint hint.

With everything in place, I present my O(n) time, O(m) space (where m is the
number of ranges required).

    
    
      int smallestRangeII(vector<int>& A, int K) {
        auto minmax_its = std::minmax_element(A.begin(), A.end());
        int imin = *minmax_its.first;
        int imax = *minmax_its.second;
        int diff = imax - imin;
        
        // If the range is smaller than K, then adding K to all elements will still have the same gap
        if (diff <= K) { 
          return diff;
        // If the range is larger than 4K, then adding K to all elements in the lower and taking K from all
        // elements in the upper range will shrink the range by K from each end
        } else if (diff >= 4 * K) {
          return diff - 2 * K;
        }
        
        // We're going to have to work for and answer, use buckets to group numbers. Storing the minimum and maximum values in each bucket
        struct Bucket {
          int imin = std::numeric_limits<int>::max();
          int imax = std::numeric_limits<int>::min();
    
          void add(int val) 
          { 
            imin = std::min(imin, val);
            imax = std::max(imax, val);
          }
          
          explicit operator bool () const { return imin != std::numeric_limits<int>::max(); }
        };
        
        // Calculate the number of buckets and the range of each bucket.
        int bucket_size = std::max(1, diff / (static_cast<int>(A.size()) - 1));
        std::vector<Bucket> buckets(diff / bucket_size + 1);
       
        // Fill the buckets
        for (auto num : A) {
          buckets[(num - imin) / bucket_size].add(num);
        }
        
        // Compare each bucket to its neighbouring bucket. The difference between the maximum of of one bucket and the minimum of it's neighbour
        // is what we're looking for. The maximum gap is the point we will want to change from adding K to subtracting K 
        auto prev_it = buckets.begin();
        for (auto it = prev_it + 1; it != buckets.end(); ++it) {
          if (*it) {
            diff = std::min(diff, std::max(imax - K, prev_it->imax + K) - std::min(imin + K, it->imin - K));
            prev_it = it;
          }
        }
        
        return diff;
      }
    

**Ta-Da!**

I left off reducing my range in the above solution to make things clearer, for
completeness, here is everything tied together;

    
    
      int smallestRangeII(vector<int>& A, int K) {
        auto minmax_its = std::minmax_element(A.begin(), A.end());
        int imin = *minmax_its.first;
        int imax = *minmax_its.second;
        int diff = imax - imin;
        
        // If the range is smaller than K, then adding K to all elements will still have the same gap
        if (diff <= K) { 
          return diff;
        // If the range is larger than 4K, then adding K to all elements in the lower and taking K from all
        // elements in the upper range will shrink the range by K from each end
        } else if (diff >= 4 * K) {
          return diff - 2 * K;
        }
        
        // Only elements in the middle concern us, they are the only ones that need to move away from the middle and hence we have to decide
        // which direction they must move. We only care about elements in the range [mmax - 2K, min + 2K], plus the nearest element on either
        // side of the range.
        int below = imin;
        int after = imax;
        int count = ((imin >= imax - 2 * K) ? 0 : 1) + ((imax <= imin + 2 * K) ? 0 : 1);
        std::vector<int> tmp;
        for (auto num : A) {
          if (num < imax - 2 * K) { below = std::max(below, num); }
          else if (num > imin + 2 * K) { after = std::min(after, num); }
          else { ++count; }
        }
    
        // We're going to have to work for and answer, use buckets to group numbers. Storing the minimum and maximum values in each bucket
        struct Bucket {
          int imin = std::numeric_limits<int>::max();
          int imax = std::numeric_limits<int>::min();
    
          void add(int val) 
          { 
            imin = std::min(imin, val);
            imax = std::max(imax, val);
          }
          
          explicit operator bool () const { return imin != std::numeric_limits<int>::max(); }
        };
        
        // Calculate the number of buckets and the range of each bucket.
        int inner_diff = after - below;
        int bucket_size = std::max(1, inner_diff / (count - 1));
    
        std::vector<Bucket> buckets(inner_diff / bucket_size + 1);
       
        // Fill the buckets
        for (auto num : A) {
          if (below <= num && num <= after) {
            buckets[(num - below) / bucket_size].add(num);
          }
        }
        
        // Compare each bucket to its neighbouring bucket. The difference between the maximum of of one bucket and the minimum of it's 
        // neighbour is what we're looking for. The maximum gap is the point we will wnat to change from adding K to subtracting K 
        auto prev_it = buckets.begin();
        for (auto it = prev_it + 1; it != buckets.end(); ++it) {
          if (*it) {
            diff = std::min(diff, std::max(imax -K, prev_it->imax + K) - std::min(imin + K, it->imin - K));
            prev_it = it;
          }
        }
        
        return diff;
      }
    

# Proof?

operation| time complexity| space complexity  
---|---|---  
Get _min_ and _max_|  O(n)| O(1)  
Count elements in reduced range| O(n)| O(1)  
Fill buckets| O(n)| O(m) where _m_ must be less than _n_ and is the number of
buckets  
Process buckets| O(m)| O(1)  
 **TOTAL**|  **O(n)**|  **O(m)**  
  
Technically, since we know the maximum range, a O(1) space solution could be
achieved by always allocating the maximum number of buckets that _could_ be
required. I feel like that's kinda of cheating.

Can O(n) time and O(1) space be achieved without doing such a hack? Well I
believe it _might_ be possible, but in this case I think O(m) space is fine...
hmmm, I might think about giving it a go for fun.

 **Don't forget the thumbs up**

![image](https://assets.leetcode.com/users/christrompf/image_1538041417.png)



