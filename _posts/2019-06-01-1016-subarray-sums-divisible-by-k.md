---
layout: post
title: 1016. Subarray Sums Divisible by K
---
### Question
Given an array `A` of integers, return the number of (contiguous, non-empty)
subarrays that have a sum divisible by `K`.



 **Example 1:**

    
    
     **Input:** A = [4,5,0,-2,-3,1], K = 5
    **Output:** 7
    **Explanation:** There are 7 subarrays with a sum divisible by K = 5:
    [4, 5, 0, -2, -3, 1], [5], [5, 0], [5, 0, -2, -3], [0], [0, -2, -3], [-2, -3]
    



 **Note:**

  1. `1 <= A.length <= 30000`
  2. `-10000 <= A[i] <= 10000`
  3. `2 <= K <= 10000`

### Solution 1
    
    
    class Solution {
        public int subarraysDivByK(int[] A, int K) {
            Map<Integer, Integer> map = new HashMap<>();
            map.put(0, 1);
            int count = 0, sum = 0;
            for(int a : A) {
                sum = (sum + a) % K;
                if(sum < 0) sum += K;  // Because -1 % 5 = -1, but we need the positive mod 4
                count += map.getOrDefault(sum, 0);
                map.put(sum, map.getOrDefault(sum, 0) + 1);
            }
            return count;
        }
    }
    

About the problems - sum of contiguous subarray , prefix sum is a common
technique.  
Another thing is if sum[0, i] % K == sum[0, j] % K, sum[i + 1, j] is divisible
by by K.  
So for current index j, we need to find out how many index i (i < j) exit that
has the same mod of K.  
Now it easy to come up with HashMap <mod, frequency>

Time Complexity: O(N)  
Space Complexity: O(K)

* * *

As @davidluoyes Comments, we can just use array, which is faster than HashMap.

    
    
    class Solution {
        public int subarraysDivByK(int[] A, int K) {
            int[] map = new int[K];
    		map[0] = 1;
            int count = 0, sum = 0;
            for(int a : A) {
                sum = (sum + a) % K;
                if(sum < 0) sum += K;  // Because -1 % 5 = -1, but we need the positive mod 4
                count += map[sum];
                map[sum]++;
            }
            return count;
        }
    }
    


### Solution 2
    
    
    class Solution:
        def subarraysDivByK(self, A, K):
            result,mod_map,running_sum = 0 ,{},0
            mod_map[0]=1
            for i in range(len(A)):
                running_sum+=A[i]
                if mod_map.get(running_sum%K) != None:
                    val = mod_map.get(running_sum%K)
                    result+=val
                    mod_map[running_sum%K]=val+1
                else:
                    mod_map[running_sum%K]=1
            return result
    

Running Sum[i]%K == Running Sum[j]%k that means we have sum(i,j) which is
divisible by K.  
Thus, we keep HashMap = {RunningSum%K : Frequency_Count}  
Time Complexity : O(n)  
Space Complexity : O(K) - As map keys are always Modulus of K with running
sum.

After Reading other solutions, revised to store mod values in an list of size
(K+1), as retreival from list is faster than Dictionary.

    
    
    class Solution:
        def subarraysDivByK(self, A, K):
            result,running_sum = 0,0
            mod_array = [1] + [0]*K
            for i in range(len(A)):
                running_sum+=A[i]
                result+=mod_array[running_sum%K]
                mod_array[running_sum%K]+=1
            return result
    

![image](https://assets.leetcode.com/users/ramanahuja/image_1547352267.png)  
![image](https://assets.leetcode.com/users/ramanahuja/image_1547352278.png)


### Solution 3
Calculate the prefix sum and count it.

 **Java, use HashMap**

    
    
        public int subarraysDivByK(int[] A, int K) {
            Map<Integer, Integer> count = new HashMap<>();
            count.put(0, 1);
            int prefix  = 0, res = 0;
            for (int a : A) {
                prefix = (prefix + a % K + K) % K;
                res += count.getOrDefault(prefix, 0);
                count.put(prefix, count.getOrDefault(prefix, 0) + 1);
            }
            return res;
        }
    

**Java, Use Array**

    
    
        public int subarraysDivByK(int[] A, int K) {
            int[] count = new int[K];
            count[0] = 1;
            int prefix  = 0, res = 0;
            for (int a : A) {
                prefix = (prefix + a % K + K) % K;
                res += count[prefix]++;
            }
            return res;
        }
    

**C++，Using vecotor:**

    
    
        int subarraysDivByK(vector<int>& A, int K) {
            vector<int> count(K);
            count[0] = 1;
            int prefix  = 0, res = 0;
            for (int a : A) {
                prefix = (prefix + a % K + K) % K;
                res += count[prefix]++;
            }
            return res;
        }
    

**Python**

    
    
        def subarraysDivByK(self, A, K):
            res = 0
            prefix  = 0
            count = [1] + [0] * K
            for a in A:
                prefix = (prefix + a) % K
                res += count[prefix]
                count[prefix] += 1
            return res
    



