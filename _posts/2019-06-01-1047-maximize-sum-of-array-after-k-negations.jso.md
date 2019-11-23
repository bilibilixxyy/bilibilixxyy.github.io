---
layout: post
title: 1047. Maximize Sum Of Array After K Negations
---
### Question
Given an array `A` of integers, we **must**  modify the array in the following
way: we choose an `i` and replace `A[i]` with `-A[i]`, and we repeat this
process `K` times in total.  (We may choose the same index `i` multiple
times.)

Return the largest possible sum of the array after modifying it in this way.



 **Example 1:**

    
    
     **Input:** A = [4,2,3], K = 1
    **Output:** 5
    **Explanation:** Choose indices (1,) and A becomes [4,-2,3].
    

**Example 2:**

    
    
    **Input:** A = [3,-1,0,2], K = 3
    **Output:** 6
    **Explanation:** Choose indices (1, 2, 2) and A becomes [3,1,0,2].
    

**Example 3:**

    
    
    **Input:** A = [2,-3,-1,5,-4], K = 2
    **Output:** 13
    **Explanation:** Choose indices (1, 4) and A becomes [2,3,-1,5,4].
    



 **Note:**

  1. `1 <= A.length <= 10000`
  2. `1 <= K <= 10000`
  3. `-100 <= A[i] <= 100`

### Solution 1
    
    
         public int largestSumAfterKNegations(int[] A, int K) {
            PriorityQueue<Integer> pq = new PriorityQueue<Integer>();
            
            for(int x: A) pq.add(x);
            while( K--  > 0) pq.add(-pq.poll());
      
            int sum  = 0;
            for(int i = 0; i < A.length; i++){
                sum += pq.poll();
            }
            return sum;
        }
    


### Solution 2
**Java**

    
    
         public int largestSumAfterKNegations(int[] A, int K) {
            Arrays.sort(A);
            for (int i = 0; K > 0 && i < A.length && A[i] < 0; ++i, --K)
                A[i] = -A[i];
            int res = 0, min = Integer.MAX_VALUE;
            for (int a : A) {
                res += a;
                min = Math.min(min, a);
            }
            return res - (K % 2) * min * 2;
        }
    

**C++**

    
    
         int largestSumAfterKNegations(vector<int>& A, int K) {
            sort(A.begin(), A.end());
            for (int i = 0; K > 0 && i < A.size() && A[i] < 0; ++i, --K)
                A[i] = -A[i];
            return accumulate(A.begin(), A.end(), 0) - (K%2) * *min_element(A.begin(), A.end()) * 2;
        }
    

**Python:**

    
    
        def largestSumAfterKNegations(self, A, K):
            A. sort()
            i = 0
            while i < len(A) and i < K and A[i] < 0:
                A[i] = -A[i]
                i += 1
            return sum(A) - (K - i) % 2 * min(A) * 2
    

We can improve this to O(N) by quick selecting the Kth in the negative
numbers.


### Solution 3
Since the `A[i]` is limited to [-100, 100], we can use an fixed-size array
`cnt` to count occurrences. Bucket sort (or, more precise, countint sort), in
other words :)

Then, as we process numbers `[-100, 100]`, we flip the negative numbers by
moving count from `cnt[i + 100]` to `cnt[-i + 100]`. This guaranties that, if
`K > 0` after processing all negative numbers, the first positive number will
have the smallest absolute value.

Therefore, when we encounter the first positive number, and our `K % 2 == 1`,
we negate one occurrence of that number.

    
    
    int largestSumAfterKNegations(vector<int>& A, int K) {
      int cnt[201] = {}, j = -100;
      for (auto i : A) ++cnt[i + 100];
      for (auto i = -100; i <= 100 && K; ++i) {
        if (cnt[i + 100]) {
          auto k = i < 0 ? min(K, cnt[i + 100]) : K % 2;
          cnt[-i + 100] += k;
          cnt[i + 100] -= k;
          K = i < 0 ? K - k : 0;
        }
      }
      return accumulate(begin(cnt), end(cnt), 0, [&](int s, int cnt) { return s + cnt * j++; });
    }
    

Java version:

    
    
    public int largestSumAfterKNegations(int[] A, int K) {
      int[] cnt = new int[201];
      int res = 0;
      for (int i : A) ++cnt[i + 100];
      for (int i = -100; i <= 100 && K > 0; ++i) {
        if (cnt[i + 100] > 0) {
          int k = i < 0 ? Math.min(K, cnt[i + 100]) : K % 2;
          cnt[-i + 100] += k;
          cnt[i + 100] -= k;
          K = i < 0 ? K - k : 0;
        }
      }
      for (int i = -100; i <= 100; ++i) res += i * cnt[i + 100];
      return res;
    }
    



