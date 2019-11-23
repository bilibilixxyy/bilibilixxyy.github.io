---
layout: post
title: 1139. Previous Permutation With One Swap
---
### Question
Given an array `A` of positive integers (not necessarily distinct), return the
lexicographically largest permutation that is smaller than `A`, that can be
**made with one swap** (A _swap_ exchanges the positions of two numbers `A[i]`
and `A[j]`).  If it cannot be done, then return the same array.



 **Example 1:**

    
    
     **Input:** [3,2,1]
    **Output:** [3,1,2]
    **Explanation:** Swapping 2 and 1.
    

**Example 2:**

    
    
    **Input:** [1,1,5]
    **Output:** [1,1,5]
    **Explanation:** This is already the smallest permutation.
    

**Example 3:**

    
    
    **Input:** [1,9,4,6,7]
    **Output:** [1,7,4,6,9]
    **Explanation:** Swapping 9 and 7.
    

**Example 4:**

    
    
    **Input:** [3,1,1,3]
    **Output:** [1,3,1,3]
    **Explanation:** Swapping 1 and 3.
    



 **Note:**

  1. `1 <= A.length <= 10000`
  2. `1 <= A[i] <= 10000`

### Solution 1
“1 1 3 3” < “1 3 1 3” < “3 1 1 3”


### Solution 2
## Wondring why 1000+ accpected?

Sceenshot when I first did this problem.  
The description and test cases were both wrong, I didn't even understand the
the problem.  
But somehow I got feeling of what it wants. This record how I did that.  
[Youtube](https://www.youtube.com/watch?v=9gFC4LNyppM) and
[Bilibili](https://www.bilibili.com/video/av53612015)  
  

## Test cases was wrong

Leetcode's solution is wrong at first when this problem was published.  
 **Example 4:**  
Input: `[3,1,1,3]`  
Leetcode Output: `[1,1,3,3]`  
Lee's Code Output: `[1,3,1,3]`  
Leetcode < Lee Code < Input  
LeetCode didn't match Lee's Code.  
  

##  **Intuition**

When we swap two digits and try to get a largest permutation,  
they must have a common prefix, which we don't care.

So what loop from end to the start,  
and find the first digit that we can swap.  
  

##  **Explanation**

Loop from right to left, and find the first digit that we can swap.  
If the number keep decreasing,  
for example `[..,3,4,4,5,6]` in the end of `[9,5,3,4,4,5,6]`,  
no matter how we swap,  
the permutation will become lager,  
so we keep moving left.

When we find the first bigger number,  
for example `A[1] = 5` in the `[9,5,3,4,4,5,6]`,  
we can swap `A[1] = 5` with a smaller number, to get a smaller permutation.

`A[1]` must be the left number to swap,  
because any other swaps on it's left,  
will get smaller permutation,  
and we want the largest permutation that is smaller than `A`

Then we want to swap `A[left]` with the first largest possible number.

Why largest possible number?  
We'll swap `A[left]` with `A[right] < A[left]`,  
we don't want `A[right]` to be very small.

Why first?  
`A[left]` is relative bigger,  
when don't want put it too far.  
  

##  **Complexity**

Time O(N), Space O(1)  
  

 **Java:**

    
    
         public int[] prevPermOpt1(int[] A) {
            int n = A.length, left = n - 2, right = n - 1, tmp;
            while (left >= 0  && A[left] <= A[left + 1]) left--;
            if (left < 0) return A;
            while (A[left] <= A[right]) right--;
            while (A[right - 1] == A[right]) right--;
            tmp = A[left]; A[left] = A[right]; A[right] = tmp;
            return A;
        }
    

**C++:**

    
    
        vector<int> prevPermOpt1(vector<int>&  A) {
            int n = A.size(), left = n - 2, right = n - 1;
            while (left >= 0  && A[left] <= A[left + 1]) left--;
            if (left < 0) return A;
            while (A[left] <= A[right]) right--;
            while (A[right - 1] == A[right]) right--;
            swap(A[left], A[right]);
            return A;
        }
    

**Python:**

    
    
        def prevPermOpt1( self, A):
            n = len(A)
            for left in range(n - 2, -1, -1):
                if A[left] > A[left + 1]:
                    break
            else:
                return A
            right = A.index(max(a for a in A[left + 1:] if a < A[left]), left)
            A[left], A[right] = A[right], A[left]
            return A
    


### Solution 3
rt.  
To admin: will this problem be rejudged and the ranking be updated?



