---
layout: post
title: 1130. Last Stone Weight II
---
### Question
We have a collection of rocks, each rock has a positive integer weight.

Each turn, we choose **any two rocks**  and smash them together.  Suppose the
stones have weights `x` and `y` with `x <= y`.  The result of this smash is:

  * If `x == y`, both stones are totally destroyed;
  * If `x != y`, the stone of weight `x` is totally destroyed, and the stone of weight `y` has new weight `y-x`.

At the end, there is at most 1 stone left.  Return the **smallest possible**
weight of this stone (the weight is  0 if there are no stones left.)



 **Example 1:**

    
    
     **Input:** [2,7,4,1,8,1]
    **Output:** 1
    **Explanation:**
    We can combine 2 and 4 to get 2 so the array converts to [2,7,1,8,1] then,
    we can combine 7 and 8 to get 1 so the array converts to [2,1,1,1] then,
    we can combine 2 and 1 to get 1 so the array converts to [1,1,1] then,
    we can combine 1 and 1 to get 0 so the array converts to [1] then that's the optimal value.
    



 **Note:**

  1. `1 <= stones.length <= 30`
  2. `1 <= stones[i] <= 100`

### Solution 1
Chinese explanation for contest this week on
[bilibili](https://www.bilibili.com/video/av52875326).

#  **Intuition**

Same problem as:  
Divide all numbers into two groups,  
what is the minimum difference between the sum of two groups.  
Now it's a easy classic knapsack problem.  
  

#  **Brief Prove**

All cases of "cancellation of rocks" can be expressed by two knapsacks.  
And the last stone value equals to the difference of these two knapsacks  
It needs to be noticed that the opposite proposition is wrong.

#  **Solution 1**

##  **Explanation** :

Very classic knapsack problem solved by DP.  
In this solution, I use `dp` to record the achievable sum of the smaller
group.  
`dp[x] = 1` means the sum `x` is possible.  
  

##  **Time Complexity** :

`O(NS)` time,  
`O(S)` space, where `S = sum(A)`.  
  

 **Java, use array:**

    
    
         public int lastStoneWeightII(int[] A) {
            boolean[] dp = new boolean[1501];
            dp[0] = true;
            int sumA = 0, res = 100;
            for (int a : A) {
                sumA += a;
                for (int i = sumA; i >= a; --i)
                    dp[i] |= dp[i - a];
            }
            for (int i = sumA / 2; i > 0; --i)
                if (dp[i]) return sumA - i - i;
            return 0;
        }
    

**C++, use bitset:**

    
    
         int lastStoneWeightII(vector<int> A) {
            bitset<1501> dp = {1};
            int sumA = 0, res = 100;
            for (int a : A) {
                sumA += a;
                for (int i = sumA; i >= a; --i)
                    dp[i] = dp[i] + dp[i - a];
            }
            for (int i = sumA / 2; i > 0; --i)
                if (dp[i]) return sumA - i - i;
            return 0;
        }
    

**Python, use set:**

    
    
         def lastStoneWeightII(self, A):
            dp = {0}
            sumA = sum(A)
            for a in A:
                dp |= {a + i for i in dp}
            return min(abs(sumA - i - i) for i in dp)
    

  

# **Solution 2**

## **Explanation** :

Adapted dp to this problem.  
In this solution, I use `dp` to record the achievable diff of one group.  
If `x` in the set `dp`, it means the difference `x` is achievable currently.  
  

## **Time Complexity** :

`O(NS)` time,  
`O(S)` space, where `S = sum(A)`.  
  

**Python, use set:**

    
    
        def lastStoneWeightII( self, A):
            dp, sumA = {0}, sum(A)
            for a in A:
                dp = {a + x for x in dp} | {a - x for x in dp}
            return min(abs(x) for x in dp)
    

  

# Follow-up:

As this problem will be quite boring as Q4 (if you read my post),  
I'll leave you a slightly harder problem as follow-up just for more fun.  
Question: Return the **biggest** possible weight of this stone?  
  

# FAQ (Some high voted questions)

 **Question:** How is it a knapsack problem?  
My understanding of Knapsack problem is this-  
You are given a set of items , for each of which we have a weight `w[i]` and
value `v[i]`.  
Now we have a bag for capacity `W` and we maximize our profit.  
 **Answer:**  
`w[i] = 1`  
`v[i] = stones[i]`  
`W = sum(stones) / 2`  
  

 **Question:** Why the minimum result of cancellation is equal to minimum
knapsack partition?  
 **Answer:**

  1. One cancellation can be represented as one grouping.  
One grouping can be represented as one knapsack partition.

  2. If the grouping difference < max(A), it can be realized by a cancellation.  
With the 2 conclusions above,  
we can know that the minimum result of cancellation is equal to minimum
grouping difference,  
which we solved by knapsack problem.  
  

 **Question:** In some version of solution, what does the magic number 1500
mean?  
 **Answer:**  
The `dp[i]` present if the sum of one group can be `i`.  
So we can only record the smaller one for less space cost,  
which less than half of the upper bound of total sum 3000.


### Solution 2
    
    
    /**
    This question eaquals to partition an array into 2 subsets whose difference is minimal
    (1) S1 + S2  = S
    (2) S1 - S2 = diff  
    
    ==> -> diff = S - 2 * S2  ==> minimize diff equals to  maximize S2 
    
    Now we should find the maximum of S2 , range from 0 to S / 2, using dp can solve this
    
    dp[i][j]   = {true if some subset from 1st to j'th has a sum equal to sum i, false otherwise}
        i ranges from (sum of all elements) {1..n}
        j ranges from  {1..n}
    
    same as 494. Target Sum
    
    */
    
    
    class Solution {
        public int lastStoneWeightII(int[] stones) {
            int S = 0, S2 = 0;
            for (int s : stones) S += s;
            int n = stones.length;
            boolean[][] dp = new boolean[S + 1][n + 1];
            for (int i = 0; i <= n; i++) {
                dp[0][i] = true;
            }
            for (int s = 1; s <= S / 2; s++) {
                for (int i = 1; i <= n; i++) {
                    if (dp[s][i - 1] || (s >= stones[i - 1] && dp[s - stones[i - 1]][i - 1])) {
                        dp[s][i] = true;
                        S2 = Math.max(S2, s);
                    }
                }
            }
            return S - 2 * S2;
        }
    }
    
    


### Solution 3
same as [494 target sum](https://leetcode.com/problems/target-sum/), it is
recommended to look at the description(only description) of the 494 before
looking at the following content.

for three number `a1, a2, a3`, according to the order of our choice,  
there are `8-2=6` different cases:

  1. a1 - (a2 - a3) = a1 - a2 + a3
  2. (a2 - a3) - a1 = -a1 + a2 - a3
  3. a1 - (a3 - a2) = a1 + a2 - a3
  4. (a3 - a2) - a1 = -a1 - a2 + a3
  5. a2 - (a1 - a3) = -a1 + a2 + a3
  6. (a1 - a3) - a2 = a1 - a2 - a3
  7. a2 - (a3 - a1) = a1 + a2 - a3 `same as case 3`
  8. (a3 - a1) - a2 = -a1 - a2 + a3 `same as case 4`
  9. a3 - (a1 - a2) = -a1 + a2 + a3 `same as case 5`
  10. (a1 - a2) - a3 = a1 - a2 - a3 `same as case 6`
  11. a3 - (a2 - a1) = a1 - a2 + a3 `same as case 1`
  12. (a2 - a1) - a3 = -a1 + a2 - a3 `same as case 2`

for each number, we can add '+' or '-' before it. there are totally 2^3 = 8
cases  
but it cannot be all positive or all negtive, so it will decrease 2 cases.

the answer is choose some numbers to be positive, others negtive.  
assume `psum` is the sum of positive number.  
Our goal is to minimize `psum - (sum(stones)-psum) = 2*psum - sum(stones)`

    
    
    # 60ms
    class Solution:
        def lastStoneWeightII(self, stones):
            s = sum(stones)
            dp = [0] * (s + 1)
            dp[0] = 1
            for i in range(len(stones)):
                for j in range(len(dp)-1, -1, -1):
                    if j - stones[i] < 0: break
                    if dp[j-stones[i]]:
                        dp[j] = 1
            
            res = s + 1
            for psum in range(1, s+1):
                if dp[psum] and 2*psum-s >= 0:
                    res = min(res, 2*psum-s)
            return res
    



