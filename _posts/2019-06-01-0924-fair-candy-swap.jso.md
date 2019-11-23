---
layout: post
title: 924. Fair Candy Swap
---
### Question
Alice and Bob have candy bars of different sizes: `A[i]` is the size of the
`i`-th bar of candy that Alice has, and `B[j]` is the size of the `j`-th bar
of candy that Bob has.

Since they are friends, they would like to exchange one candy bar each so that
after the exchange, they both have the same total amount of candy.  ( _The
total amount of candy  a person has is the sum of the sizes of candy bars they
have._)

Return an integer array `ans` where `ans[0]` is the size of the candy bar that
Alice must exchange, and `ans[1]` is the size of the candy bar that Bob must
exchange.

If there are multiple answers, you may return any one of them.  It is
guaranteed an answer exists.



 **Example 1:**

    
    
     **Input:** A = [1,1], B = [2,2]
    **Output:** [1,2]
    

**Example 2:**

    
    
    **Input:** A = [1,2], B = [2,3]
    **Output:** [1,2]
    

**Example 3:**

    
    
    **Input:** A = [2], B = [1,3]
    **Output:** [2,3]
    

**Example 4:**

    
    
    **Input:** A = [1,2,5], B = [2,4]
    **Output:** [5,4]
    



 **Note:**

  * `1 <= A.length <= 10000`
  * `1 <= B.length <= 10000`
  * ` 1 <= A[i] <= 100000`
  * `1 <= B[i] <= 100000`
  * It is guaranteed that Alice and Bob have different total amounts of candy.
  * It is guaranteed there exists an answer.

### Solution 1
Calculate `dif = (sum(A) - sum(B)) / 2`  
We want find a pair `(a, b)` with `a = b + dif`

 **Time Complexity** :  
O(N)

 **C++:**

    
    
        vector< int> fairCandySwap(vector<int> A, vector<int> B) {
            int dif = (accumulate(A.begin(), A.end(), 0) - accumulate(B.begin(), B.end(), 0)) / 2;
            unordered_set<int> S(A.begin(), A.end());
            for (int b: B)
                if (S.count(b + dif))
                    return {b + dif, b};
        }
    

**Java:**

    
    
         public int[] fairCandySwap(int[] A, int[] B) {
            int dif = (IntStream.of(A).sum() - IntStream.of(B).sum()) / 2;
            HashSet<Integer> S = new HashSet<>();
            for (int a : A) S.add(a);
            for (int b : B) if (S.contains(b + dif)) return new int[] {b + dif, b};
            return new int[0];
        }
    

**Python:**

    
    
        def fairCandySwap( self, A, B):
            dif = (sum(A) - sum(B)) / 2
            A = set(A)
            for b in set(B):
                if dif + b in A:
                    return [dif + b, b]
    


### Solution 2
![image](https://s3-lc-
upload.s3.amazonaws.com/users/marswork/image_1534651707.png)  
Therefore, our target is to find a candy pair whose difference is exactly
`x/2`  
if B > A, logic is exactly the same

#### Sol1 Brute Force

time complexity:`O(A + B + A * B)`  
space complexity:`O(1)`

    
    
    class Solution {
        public int[] fairCandySwap(int[] A, int[] B) {
            int sumA = 0, sumB = 0;
            for (int i = 0; i < A.length; i++)
                sumA += A[i];
            for (int i = 0; i < B.length; i++)
                sumB += B[i];
            int dif = (sumA - sumB) / 2;
            for (int i = 0; i < A.length; i++) {
                for (int j = 0; j < B.length; j++) {
                    if (A[i] - B[j] == dif)
                        return new int[]{A[i], B[j]};
                }
            }
            return null;
        }
    }
    

#### Sol2 Use Hash Set

time complexity: `O(A + B)`  
space complexity: `O(A)`

    
    
    class Solution {
        public int[] fairCandySwap(int[] A, int[] B) {
            int sumA = 0, sumB = 0;
            Set<Integer> setA = new HashSet<>();
            for (int i = 0; i < A.length; i++) {
                sumA += A[i];
                setA.add(A[i]);
            }
            for (int i = 0; i < B.length; i++)
                sumB += B[i];
            int dif = (sumA - sumB) / 2;
            for (int i = 0; i < B.length; i++) {
                int targetA = B[i] + dif;
                if (setA.contains(targetA))
                    return new int[]{targetA, B[i]};
            }
            return null;
        }
    }
    


### Solution 3
    
    
    class Solution:
        def fairCandySwap(self, A, B):
            """
            :type A: List[int]
            :type B: List[int]
            :rtype: List[int]
            """
            n = (sum(A) - sum(B)) // 2
            A = list(map(lambda a: a - n, A))
            b = (list(set(A) & set(B)))[0]
            return [b + n, b]
    



