---
layout: post
title: 1034. Subarrays with K Different Integers
---
### Question
Given an array `A` of positive integers, call a (contiguous, not necessarily
distinct) subarray of `A` _good_ if the number of different integers in that
subarray is exactly `K`.

(For example, `[1,2,3,1,2]` has `3` different integers: `1`, `2`, and `3`.)

Return the number of good subarrays of `A`.



 **Example 1:**

    
    
     **Input:** A = [1,2,1,2,3], K = 2
    **Output:** 7
    **Explanation:** Subarrays formed with exactly 2 different integers: [1,2], [2,1], [1,2], [2,3], [1,2,1], [2,1,2], [1,2,1,2].
    

**Example 2:**

    
    
    **Input:** A = [1,2,1,3,4], K = 3
    **Output:** 3
    **Explanation:** Subarrays formed with exactly 3 different integers: [1,2,1,3], [2,1,3], [1,3,4].
    



 **Note:**

  1. `1 <= A.length <= 20000`
  2. `1 <= A[i] <= A.length`
  3. `1 <= K <= A.length`

### Solution 1
[Contest 123 in C++](https://www.youtube.com/watch?v=eBKv6-m4NDQ)

 **Intuition** :  
Write a helper using sliding window,  
to get the number of subarrays with at most K distinct elements.  
Then f(exactly K) = f(atMost K) - f(atMost K-1).

Of course, you can merge 2 for loop into ones, if you like.

 **Time Complexity** :  
O(N)

 **Java:**

    
    
         public int subarraysWithKDistinct(int[] A, int K) {
            return atMostK(A, K) - atMostK(A, K - 1);
        }
        int atMostK(int[] A, int K) {
            int i = 0, res = 0;
            Map<Integer, Integer> count = new HashMap<>();
            for (int j = 0; j < A.length; ++j) {
                if (count.getOrDefault(A[j], 0) == 0) K--;
                count.put(A[j], count.getOrDefault(A[j], 0) + 1);
                while (K < 0) {
                    count.put(A[i], count.get(A[i]) - 1);
                    if (count.get(A[i]) == 0) K++;
                    i++;
                }
                res += j - i + 1;
            }
            return res;
        }
    

**C++:**

    
    
         int subarraysWithKDistinct(vector<int>& A, int K) {
            return atMostK(A, K) - atMostK(A, K - 1);
        }
        int atMostK(vector<int>& A, int K) {
            int i = 0, res = 0;
            unordered_map<int, int> count;
            for (int j = 0; j < A.size(); ++j) {
                if (!count[A[j]]++) K--;
                while (K < 0) {
                    if (!--count[A[i]]) K++;
                    i++;
                }
                res += j - i + 1;
            }
            return res;
        }
    

**Python:**

    
    
        def subarraysWithKDistinct(self, A, K):
             return self.atMostK(A, K) - self.atMostK(A, K - 1)
    
        def atMostK(self, A, K):
            count = collections.Counter()
            res = i = 0
            for j in range(len(A)):
                if count[A[j]] == 0: K -= 1
                count[A[j]] += 1
                while K < 0:
                    count[A[i]] -= 1
                    if count[A[i]] == 0: K += 1
                    i += 1
                res += j - i + 1
            return res
    


### Solution 2
If the problem talks about continuous subarrays or substrings, the sliding
window technique may help solve it in a linear time. Such problems are tricky,
though the solution is simple once you get it. No wonder I am seeing such
problems in almost every interview!

Here, we will take a look at [Subarrays with K Different
Integers](https://leetcode.com/problems/subarrays-with-k-different-integers/)
(LeetCode Hard), which appeared on LeetCode [weekly contest
#123](https://leetcode.com/contest/weekly-contest-123). You can also master
the sliding windows technique with these additional problems:

  * [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
  * [Longest Substring with At Most Two Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-two-distinct-characters/)
  * [Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/)

# Intuition

If the subarray `[j, i]` contains `K` unique numbers, and first `prefix`
numbers also appear in `[j + prefix, i]` subarray, we have total `1 + prefix`
good subarrays. For example, there are 3 unique numers in `[1, 2, 1, 2, 3]`.
First two numbers also appear in the remaining subarray `[1, 2, 3]`, so we
have 1 + 2 good subarrays: `[1, 2, 1, 2, 3]`, `[2, 1, 2, 3]` and `[1, 2, 3]`.

# Linear Solution

We can iterate through the array and use two pointers for our sliding window
(`[j, i]`). The back of the window is always the current position in the array
(`i`). The front of the window (`j`) is moved so that A[j] appear only once in
the sliding window. In other words, we are trying to shrink our sliding window
while maintaining the same number of unique elements.

To do that, we keep tabs on how many times each number appears in our window
(`m`). After we add next number to the back of our window, we try to remove as
many as possible numbers from the front, until the number in the front appears
only once. While removing numbers, we are increasing `prefix`.

If we collected `K` unique numbers, then we found 1 + `prefix` sequences, as
each removed number would also form a sequence.

If our window reached `K + 1` unique numbers, we remove one number from the
head (again, that number appears only in the front), and reset `prefix` as now
we are starting a new sequence. This process is demonstrated step-by-step for
the test case below; `prefix` are shown as `+1` in the green background.

    
    
    [5,7,5,2,3,3,4,1,5,2,7,4,6,2,3,8,4,5,7]
    7
    

![image](https://assets.leetcode.com/users/votrubac/image_1549876616.png)

In the code below, we use `cnt` to track unique numbers. Since `1 <= A[i] <=
A.size()`, we can use an array instead of hash map to improve the performance.

    
    
    int subarraysWithKDistinct(vector<int>& A, int K, int res = 0) {
      vector<int> m(A.size() + 1);
      for(auto i = 0, j = 0, prefix = 0, cnt = 0; i < A.size(); ++i) {
        if (m[A[i]]++ == 0) ++cnt;
        if (cnt > K) --m[A[j++]], --cnt, prefix = 0;
        while (m[A[j]] > 1) ++prefix, --m[A[j++]];
        if (cnt == K) res += prefix + 1;
      }
      return res;
    }
    

Java version:

    
    
    public int subarraysWithKDistinct(int[] A, int K) {
      int res = 0, prefix = 0;
      int[] m = new int[A.length + 1];
      for (int i = 0, j = 0, cnt = 0; i < A.length; ++i) {
        if (m[A[i]]++ == 0) ++cnt;
        if (cnt > K) {
          --m[A[j++]]; --cnt; prefix = 0; 
        }
        while (m[A[j]] > 1) {
          ++prefix; --m[A[j++]]; 
        }
        if (cnt == K) res += prefix + 1;
      }
      return res;
    } 
    

### Complexity Analysis

  * Time Complexity: _O(n)_ , where _n_ is the length of `A`.
  * Space Complexity: _O(n)_.


### Solution 3
For [992\. Subarrays with K Different
Integers](https://leetcode.com/problems/subarrays-with-k-different-integers/):

    
    
    class Solution:
        def subarraysWithKDistinct(self, A: 'List[int]', K: 'int') -> 'int':
            return self.subarraysWithAtMostKDistinct(A, K) - self.subarraysWithAtMostKDistinct(A, K-1)
        
        def subarraysWithAtMostKDistinct(self, s, k):
            lookup = collections.defaultdict(int)
            l, r, counter, res = 0, 0, 0, 0
            while r < len(s):
                lookup[s[r]] += 1
                if lookup[s[r]] == 1:
                    counter += 1
                r += 1   
                while l < r and counter > k:
                    lookup[s[l]] -= 1
                    if lookup[s[l]] == 0:
                        counter -= 1
                    l += 1
                res += r - l 
            return res
    

For [3\. Longest Substring Without Repeating
Characters](https://leetcode.com/problems/longest-substring-without-repeating-
characters/):

    
    
    class Solution:
        def lengthOfLongestSubstring(self, s):
            """
            :type s: str
            :rtype: int
            """
            lookup = collections.defaultdict(int)
            l, r, counter, res = 0, 0, 0, 0
            while r < len(s):
                lookup[s[r]] += 1
                if lookup[s[r]] == 1:
                    counter += 1
                r += 1
                while l < r and counter < r - l:
                    lookup[s[l]] -= 1
                    if lookup[s[l]] == 0:
                        counter -= 1
                    l += 1
                res = max(res, r - l)
            return res
    

For [159\. Longest Substring with At Most Two Distinct
Characters](https://leetcode.com/problems/longest-substring-with-at-most-two-
distinct-characters/):

    
    
    class Solution(object):
        def lengthOfLongestSubstringTwoDistinct(self, s):
            """
            :type s: str
            :rtype: int
            """
            lookup = collections.defaultdict(int)
            l, r, counter, res = 0, 0, 0, 0
            while r < len(s):
                lookup[s[r]] += 1
                if lookup[s[r]] == 1:
                    counter += 1
                r += 1   
                while l < r and counter > 2:
                    lookup[s[l]] -= 1
                    if lookup[s[l]] == 0:
                        counter -= 1
                    l += 1
                res = max(res, r - l) 
            return res
    

For [340\. Longest Substring with At Most K Distinct
Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-
distinct-characters/):

    
    
    class Solution(object):
        def lengthOfLongestSubstringKDistinct(self, s, k):
            """
            :type s: str
            :type k: int
            :rtype: int
            """
            lookup = collections.defaultdict(int)
            l, r, counter, res = 0, 0, 0, 0
            while r < len(s):
                lookup[s[r]] += 1
                if lookup[s[r]] == 1:
                    counter += 1
                r += 1   
                while l < r and counter > k:
                    lookup[s[l]] -= 1
                    if lookup[s[l]] == 0:
                        counter -= 1
                    l += 1
                res = max(res, r - l) 
            return res
    



