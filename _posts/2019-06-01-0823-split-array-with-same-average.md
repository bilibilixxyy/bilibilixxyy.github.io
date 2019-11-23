---
layout: post
title: 823. Split Array With Same Average
---
### Question
In a given integer array A, we must move every element of A to either list B
or list C. (B and C initially start empty.)

Return true if and only if after such a move, it is possible that the average
value of B is equal to the average value of C, and B and C are both non-empty.

    
    
     **Example :**
    **Input:** 
    [1,2,3,4,5,6,7,8]
    **Output:** true
    **Explanation:** We can split the array into [1,4,5,8] and [2,3,6,7], and both of them have the average of 4.5.
    

**Note:**

  * The length of `A` will be in the range [1, 30].
  * `A[i]` will be in the range of `[0, 10000]`.

### Solution 1
First, this problem is NP, and the worst case runtime is exponential. But the
expected runtime for random input could be very fast.

If the array of size n can be splitted into group A and B with same mean,
assuming A is the smaller group, then

    
    
      totalSum/n = Asum/k = Bsum/(n-k), where k = A.size() and 1 <= k <= n/2;
      Asum = totalSum*k/n, which is an integer. So we have totalSum*k%n == 0;
    

In general, not many k are valid.

**Solution 2: early pruning + knapsack DP, O(n^3 * M) 33 ms**  
If there are still some k valid after early pruning by checking `totalSum*k%n
== 0`,  
we can generate all possible combination sum of k numbers from the array using
DP, like knapsack problem. (Note: 1  <= k <= n/2)  
Next, for each valid k, simply check whether the group sum, i.e. totalSum * k
/ n, exists in the kth combination sum hashset.

    
    
    vector<vector<unordered_set<int>>> sums(n, vector<unordered_set<int>>(n/2+1));
    sums[i][j] is all possible combination sum of j numbers from the subarray A[0, i];
    

**Goal:** sums[n-1][k], for all k in range [1, n/2]  
**Initial condition:** sums[i][0] = {0}, 0  <= i <= n-1; sums[0][1] = {all
numbers in the array};  
**Deduction:** sums[i+1][j] = sums[i][j] "join" (sums[i][j-1] + A[i+1])  
The following code uses less space but the same DP formula.  
**Runtime analysis:**  
All numbers in the array are in range [0, 10000]. Let M = 10000.  
So the size of kth combination sum hashset, i.e. sums[...][k], is  <= k * M;  
For each number in the array, the code need loop through all combination sum
hashsets, so  
the total runtime is n * (1 * M + 2 * M + ... + (n/2) * M) = **O(n^3 * M)**

    
    
     class Solution {
    public:
        bool splitArraySameAverage(vector<int>& A) {
            int n = A.size(), m = n/2, totalSum = accumulate(A.begin(), A.end(), 0);
            // early pruning
            bool isPossible = false;
            for (int i = 1; i <= m && !isPossible; ++i) 
                if (totalSum*i%n == 0) isPossible = true;
            if (!isPossible) return false;
            // DP like knapsack
            vector<unordered_set<int>> sums(m+1);
            sums[0].insert(0);
            for (int num: A) {
                for (int i = m; i >= 1; --i) 
                    for (const int t: sums[i-1]) 
                        sums[i].insert(t + num);
            }
            for (int i = 1; i <= m; ++i) 
                if (totalSum*i%n == 0 && sums[i].find(totalSum*i/n) != sums[i].end()) return true;
            return false;
        }
    };
    

**Solution 1: early termination + combination sum. 5 ms Now TLE (Update)**  
For such k, the problem transforms to "Find k sum = Asum, i.e. totalSum * k/n,
from an array of size n". This subproblem is similar to LC39 combination sum,
which can be solved by backtracking.

    
    
     class Solution {
    public:
        bool splitArraySameAverage(vector<int>& A) {
            int n = A.size(), m = n/2, totalSum = accumulate(A.begin(), A.end(), 0);
            sort(A.rbegin(), A.rend()); // Optimization
            for (int i = 1; i <= m; ++i) 
                if (totalSum*i%n == 0 && combinationSum(A, 0, i, totalSum*i/n)) return true;
            return false;
        }
        bool combinationSum(vector<int>& nums, int idx, int k, int tar) {
            if (tar > k * nums[idx]) return false; // Optimization, A is sorted from large to small
            if (k == 0) return tar == 0;
            for (int i = idx; i <= nums.size()-k; ++i) 
                if (nums[i] <= tar && combinationSum(nums, i+1, k-1, tar-nums[i])) return true;
            return false;
        }
    };
    


### Solution 2
**Python version**

My fastest Python solution so far, gets accepted in about 44 ms (tried five
times, took 44, 48, 44, 40 and 44 ms). After using [bitset over size
N](https://leetcode.com/problems/split-array-with-same-
average/discuss/120830/DP-with-bitset) I realized I could do bitset over sum S
instead, using Python's big integers. Then almost all action is in Python's C
code (for big integers). Which is fast and saves me code.

    
    
    def splitArraySameAverage(self, A):
        N, S, P = len(A), sum(A), [1]
        for a in A:
            P[1:] = [(p << a) | q for p, q in zip(P, P[1:] + [0])]
        return any(S * n % N == 0 and P[n] & (1 << (S * n // N))
                   for n in range(1, N))
    

A nice side effect of using big integers for the bitsets is that they don't
always store S bits but only as many bits as needed to include the largest
1-bit. We can even take further advantage of this by using `for a in
sorted(A)`, which keeps the bitsets short as long as it can. Though that
doesn't make it faster here, as 44 ms is already pretty much as fast as
possible. Even the below cheating solution takes about 40 ms:

    
    
    answers = iter('TFFTFTFFFFTTFFFFFFFFFFFFFTTFFFFFTFFFFFTFFFFTFFFFTFFFFFFFFTFFFTFTTFFFTFTFFFFFFFFFFFFTFFF')
    class Solution:
        def splitArraySameAverage(self, A):
            return next(answers) == 'T'
    

  

**Ruby version**

Ruby also has big integers that we can use as bitsets. Takes about 50 ms (five
attempts took 52, 52, 48, 52 and 48 ms):

    
    
    def split_array_same_average(a)
      n, s, p = a.size, a.sum, [1]
      a.each { |x| p[1..-1] = (p << 0).each_cons(2).map { |q, r| (q << x) | r } }
      (1..n/2).any? { |m| s * m % n == 0 && (p[m] & (1 << (s * m / n))) > 0 }
    end
    

  

**C++ version** using `bitset<300001>`

Sadly `bitset` doesn't support dynamic sizes. So I must make it as large as
the largest _possible_ sum, not just as large as the sum of the given input
and not just as large as the current prefix sum of the given input. It takes
about 190 ms, much slower than my [bitset over
size](https://leetcode.com/problems/split-array-with-same-
average/discuss/120830/Simple-DP) version (which takes about 35 ms). I could
make it about twice as fast by not going the full N-range for every `a` but
only going as far as how many `a` values I've used so far, but meh... what's
the point if it's gonna be slower anyway.

    
    
     bool splitArraySameAverage(vector<int>& A) {        
        int N = A.size(), S = 0;
        for (int a : A) S += a;
        bitset<300001> p[N] = {1};
        for (int a : A)
            for (int n = N - 2; n >= 0; n--)
                p[n+1] |= p[n] << a;
        for (int n = 1; n < N; n++)
            if (S*n%N == 0  &&  p[n][S*n/N])
                return true;
        return false;
    }
    


### Solution 3
We need to split A into B and C, the length of B can be `[1, A.length / 2]`,
we consider them one by one:  
B should have the same mean value as A, so `sumB / lenOfB = sumA / lenOfA`,
which is equavalent to `sumB = sumA * lenOfB / lenOfA`. All elements here are
integers, so `sumB` must be an integer, this gives our first criteria `(sumA *
lenOfB) % A.length == 0`.  
Then further in function `check(int[] A, int leftSum, int leftNum, int
startIndex)`, we recursicely check if we can find `lenOfB` elements in A who
sum up to `sumB`.

    
    
    class Solution {    
        public boolean check(int[] A, int leftSum, int leftNum, int startIndex) {       
            if (leftNum == 0) return leftSum == 0;
            if ((A[startIndex]) > leftSum / leftNum) return false;
            for (int i = startIndex; i < A.length - leftNum + 1; i ++) {
    	    if (i > startIndex && A[i] == A[i - 1]) continue;
                if (check(A, leftSum - A[i], leftNum - 1, i + 1)) return true;
            }
            return false;       
        }
        
        public boolean splitArraySameAverage(int[] A) {
            if (A.length == 1) return false;
            int sumA = 0;
            for (int a: A) sumA += a;
            Arrays.sort(A);
            for (int lenOfB = 1; lenOfB <= A.length / 2; lenOfB ++) {
                if ((sumA * lenOfB) % A.length == 0) {
                    if (check(A, (sumA * lenOfB) / A.length, lenOfB, 0)) return true;
                }
            }
            return false;
            
        }
    }
    



