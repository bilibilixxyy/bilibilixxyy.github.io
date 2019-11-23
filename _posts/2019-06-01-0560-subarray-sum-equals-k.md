---
layout: post
title: 560. Subarray Sum Equals K
---
### Question
Given an array of integers and an integer **k** , you need to find the total
number of continuous subarrays whose sum equals to **k**.

 **Example 1:**  

    
    
     **Input:** nums = [1,1,1], k = 2
    **Output:** 2
    

**Note:**  

  1. The length of the array is in range [1, 20,000].
  2. The range of numbers in the array is [-1000, 1000] and the range of the integer **k** is [-1e7, 1e7].

### Solution 1
Solution 1. Brute force. We just need two loops (i, j) and test if `SUM[i, j]`
= k. Time complexity O(n^2), Space complexity O(1). I bet this solution will
TLE.

Solution 2. From solution 1, we know the key to solve this problem is `SUM[i,
j]`. So if we know `SUM[0, i - 1]` and `SUM[0, j]`, then we can easily get
`SUM[i, j]`. To achieve this, we just need to go through the array, calculate
the current sum and save number of all seen `PreSum` to a HashMap. Time
complexity O(n), Space complexity O(n).

    
    
    public class Solution {
        public int subarraySum(int[] nums, int k) {
            int sum = 0, result = 0;
            Map<Integer, Integer> preSum = new HashMap<>();
            preSum.put(0, 1);
            
            for (int i = 0; i < nums.length; i++) {
                sum += nums[i];
                if (preSum.containsKey(sum - k)) {
                    result += preSum.get(sum - k);
                }
                preSum.put(sum, preSum.getOrDefault(sum, 0) + 1);
            }
            
            return result;
        }
    }
    


### Solution 2
Let's remember count[V], the number of previous prefix sums with value V. If
our newest prefix sum has value W, and W-V == K, then we add count[V] to our
answer.

This is because at time t, `A[0] + A[1] + ... + A[t-1] = W`, and there are
`count[V]` indices `j` with `j < t-1` and `A[0] + A[1] + ... + A[j] = V`.
Thus, there are `count[V]` subarrays `A[j+1] + A[j+2] + ... + A[t-1] = K`.

    
    
    def subarraySum(self, A, K):
        count = collections.Counter()
        count[0] = 1
        ans = su = 0
        for x in A:
            su += x
            ans += count[su-K]
            count[su] += 1
        return ans
    


### Solution 3
**Solution1 (TLE)**  
Basic brute-force approach - for all sum[i,j] count how many times we saw k.
O(n^3).

    
    
    public int subarraySum(int[] nums, int k) {
            int count = 0;
            for(int x=0; x < nums.length; x++){
                for(int y=x; y < nums.length; y++){
                    int sum = 0;
                    for(int z=x; z <= y; z++)
                        sum += nums[z];
                    if(sum == k)
                        ++count;
                }
            }
            return count;
        }
    

**Solution2** \- make use of prefix sum for the third for-loop above. O(n^2)

    
    
    public int subarraySum(int[] nums, int k) {
            int count =  0;
            for(int x=1; x < nums.length; x++)
                nums[x] += nums[x-1];
            for(int x=0; x < nums.length; x++){
                if(nums[x] == k)
                    ++count;
                for(int y=x+1; y < nums.length; y++)
                    if(nums[y]-nums[x] == k)
                        ++count;
            }
            return count;
        }
    

**Solution3** \- Remember the frequency of all prefix sums. O(n) time and O(n)
memory.  
`sum` to keep track of sum of all the elements so far. If we can find a prefix
sum in the map for `sum-k`, it means we can form `sum == k` using the elements
after the element corresponding to that prefix sum till the current element
(included). Count all such sums at each element.

There is a special case like in the Solution2 when `nums[x] == k` that is
current sum itself is equal to target without any subtractions. For this
solution, we can either increment count by 1 whenever `sum == k` below or make
an entry as a special case in our map  
`preSumFreq.put(0, 1)` to cover those cases.

    
    
    public int subarraySum(int[] nums, int k) {
            int count = 0, sum=0;
            Map<Integer, Integer> preSumFreq = new HashMap<>();
            preSumFreq.put(0, 1);
            for(int i=0; i < nums.length; i++){
                sum += nums[i];
                count += preSumFreq.getOrDefault(sum-k, 0);
                preSumFreq.put(sum, preSumFreq.getOrDefault(sum,0)+1);
            }
            return count;
        }
    



