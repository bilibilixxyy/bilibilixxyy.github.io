---
layout: post
title: 966. Binary Subarrays With Sum
---
### Question
In an array `A` of `0`s and `1`s, how many **non-empty** subarrays have sum
`S`?



 **Example 1:**

    
    
     **Input:** A = [1,0,1,0,1], S = 2
    **Output:** 4
    **Explanation:**
    The 4 subarrays are bolded below:
    [ **1,0,1** ,0,1]
    [ **1,0,1,0** ,1]
    [1, **0,1,0,1** ]
    [1,0, **1,0,1** ]
    



 **Note:**

  1. `A.length <= 30000`
  2. `0 <= S <= A.length`
  3. `A[i]` is either `0` or `1`.

### Solution 1
Count the occurrence of all prefix sum.

I didn't notice that the array contains only 0 and 1.  
As @davidluoyes suggest, we can use an array with length = N + 1,  
to improve the run time.

 **C++:**

    
    
         int numSubarraysWithSum(vector<int>& A, int S) {
            unordered_map<int, int> c({{0, 1}});
            int psum = 0, res = 0;
            for (int i : A) {
                psum += i;
                res += c[psum - S];
                c[psum]++;
            }
            return res;
        }
    

**Java:**

    
    
         public int numSubarraysWithSum(int[] A, int S) {
            int psum = 0, res = 0, count[] = new int[A.length + 1];
            count[0] = 1;
            for (int i : A) {
                psum += i;
                if (psum >= S)
                    res += count[psum - S];
                count[psum]++;
            }
            return res;
        }
    

**Python:**

    
    
        def numSubarraysWithSum(self, A, S):
            c = collections.Counter({ 0: 1})
            psum = res = 0
            for i in A:
                psum += i
                res += c[psum - S]
                c[psum] += 1
            return res
    


### Solution 2
TLDR; `Two Sum + Prefix Sum Caching`

`Logic:` In this problem we are required to find some interval `[i:j] ,i < j`
where `sum[i:j] = target`. We know that `sum[i:j] = A[i] + A[i+1] +... +
A[j]`.  
Then we also know that  
Let's define `prefixSum[j] = A[0] + A[1] + ... + A[j] 0 <= j <= n-1 (n =
A.length)`  
It is easy to see that,  
`sum[i:j] = A[i] + A[i+1] ... + A[j] =`  
`(A[0] + A[1] + ... A[i] ... + A[j]) - (A[0] + A[1] + ... A[i-1])` =  
`prefix[j] - prefix[i-1]`.

Now we the problem reduces to finding # of pairs (i, j) (i < j) such that  
`prefix[j] - prefix[i-1]` = **target**  
`This becomes prefix[i-1] = prefix[j] - target with some algebra.`  
So we use the hashmap to find all pairs that satisfy the above equations.

We only need to track the prefix sum up to this point however, since we
already saved all the previous results in the map.

`if (sum == target) total++` Here I am checking for the case where the current
element is equal to the sum (it needs no interval to produce the sum).

    
    
    class Solution {
        public int numSubarraysWithSum(int[] A, int target) {
            Map<Integer, Integer> presum = new HashMap<>();
    	//Prefix sum
            int sum = 0;
    	//Answer
            int total = 0;
            for (int i = 0; i < A.length; i++){
                sum += A[i];
                if (presum.get(sum - target) != null){
                    total += presum.get(sum - target);
                }
                if (sum == target) total++;
                //Also put this sum into the map as well
                presum.put(sum, presum.getOrDefault(sum, 0) + 1);
            }
            
            return total;
            
        }
    }
    

Using a hashmap is overkill in this problem since the only sums that are
possible are [0, 1, ..., n].  
Therefore, we can just an array as our map instread. Credits to @davidluoyes
for pointing this out.

    
    
    class Solution {
        public int numSubarraysWithSum(int[] A, int target) {
            //The largest sum we can have is len(A) = n Why? What if array A[] has all 1's.
            int n = A.length;
            //Everything is initialized to zero
            int[] presum = new int[n+1];
            int sum = 0;
            //Case where it's just it's own 
            int total = 0;
            
            for (int i = 0; i < A.length; i++){
                sum += A[i];
                int compliment = sum - target;
                
                if (compliment >= 0)
                total += presum[compliment];
                
                if (sum == target) total++;
                //Also put this sum into the map as well
                presum[sum]+=1;
            }
            
            return total;
            
        }
    }
    


### Solution 3
This problem is a simplified problem of 560. This is the
[link](https://leetcode.com/problems/subarray-sum-equals-k/description/).

  1. brute force: $Time: O(N^2), Space: O(1)$  
loop all [i, j] combination.

  2. presum $Time: O(N), Space: O(N)$  
store previous sum and the times of this sum, because sum[i, j] = sum[0, j] -
sum[0, i - 1], this is a **very very important idea**

  3. two pointer: (positive value)  
if there no 0, we can use 2 pointers easily to solve this problem, because the
right pointer moving forward makes the sum larger, and left pointer moving
forward makes the sum smaller.  
BUT, if there exists 0, we need to pay attention to pattern like this :  
[x,x,x,0,0,0,y,y,y,0,0,0,z,z,z], assuming that y+y+y in the mediumm is what we
want, so when _lo_ is the idx of the first y, and _hi_ is the idx of the last
y, now _prev_ is the idx of the last x, which is the closest non-0 to the
first y(A[lo]), then we can know that up to _hi_ , we have 1 + (lo - prev - 1)
subarrays satisfying the sum equals K. This in java likes this:

    
    
    if ( sum + A[hi] == K ) {
        sum += A[hi];
        hi++;
        cnt += 1 + (lo - prev - 1);
    }
    

There are two more branches to consider, one is that sum + A[hi] is smaller
than K, which we need to move hi forward to make sum bigger. This in java
likes this:

    
    
    if ( sum + A[hi] < K ) {
        sum += A[hi];
        hi++;
    }
    

the other one is that sum + A[hi] is bigger than K, which we need to move lo
forward to make sum smaller, BUT, there are one more condition we need to pay
attention to, that is when we move forward, we need to update current _prev_
to the preivous lo. And to make sure A[lo] is non-0, we need to move lo over
the 0 in [y,y,y]. This in java likes this:

    
    
     if ( sum + A[hi] > K ) {
        sum -= A[lo];
        prev = lo;
        lo++;
        while ( A[lo] == 0 ) {
            lo++;
        }
    }
    

So now we have the code like this:

    
    
    if ( sum + A[hi] == K ) {
        sum += A[hi];
        hi++;
        cnt += 1 + (lo - prev - 1);
    }
    else if ( sum + A[hi] < K ) {
        sum += A[hi];
        hi++;
    }
    else {
        sum -= A[hi];
        prev = lo;
        lo++;
        while ( A[lo] == 0 ) {
            lo++;
        }
    }
    

And also we need to pay attention to S = 0. And case like [0,0,0,0,0] S > 0\.
So the whole code is as follows:

    
    
    public int numSubarraysWithSum(int[] A, int S) {
        if ( S == 0 ) return helper(A); //deal with S == 0 
        int prev = -1;
        int lo = 0, hi = 0;
        int sum = 0;
        int cnt = 0;
        // deal with S > 0 but cases like[0,0,0,0,0]
        while ( lo < A.length && A[lo] == 0 ) {
            lo++;
            hi++;
        }
        while ( hi < A.length ) {
            if ( sum + A[hi] == S ) {
                sum += A[hi];
                cnt += 1 + (lo - prev - 1);
                hi++;
            }
            else if ( sum + A[hi] < S ) {
                sum += A[hi];
                hi++;
            }
            else {
                sum -= A[lo];
                prev = lo;
                lo++;
                while ( A[lo] == 0 ) 
                    lo++;
            }
        }
        return cnt;
    }
    public int helper(int[] A) {
        int cnt = 0;
        int res = 0;
        for ( int i = 0; i < A.length; i++ ) {
            if ( A[i] == 0 ) {
                cnt++;
                res += cnt;
            }
            else {
                cnt = 0;
            }
        }
        return res;
    }
    



