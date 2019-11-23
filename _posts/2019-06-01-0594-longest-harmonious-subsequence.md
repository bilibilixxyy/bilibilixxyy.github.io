---
layout: post
title: 594. Longest Harmonious Subsequence
---
### Question
We define a harmounious array as an array where the difference between its
maximum value and its minimum value is **exactly** 1.

Now, given an integer array, you need to find the length of its longest
harmonious subsequence among all its possible
[subsequences](https://en.wikipedia.org/wiki/Subsequence).

 **Example 1:**

    
    
     **Input:** [1,3,2,2,5,2,3,7]
    **Output:** 5
    **Explanation:** The longest harmonious subsequence is [3,2,2,2,3].
    



 **Note:** The length of the input array will not exceed 20,000.

### Solution 1
  * The idea is to keep a count of all the numbers, and eventually for each of the numbers, check if there's any adjacent number. If it's present, then add the count of both - since these two numbers form subsequence in the array.

 **Update : from @harkness comment, we don't need to check both +1 and -1;**

    
    
     public int findLHS(int[] nums) {
        Map<Long, Integer> map = new HashMap<>();
        for (long num : nums) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        int result = 0;
        for (long key : map.keySet()) {
            if (map.containsKey(key + 1)) {
                result = Math.max(result, map.get(key + 1) + map.get(key));
            }
        }
        return result;
    }


### Solution 2
Let `count[x]` be the number of `x`'s in our array.  
Suppose our longest subsequence `B` has `min(B) = x` and `max(B) = x+1`.  
Evidently, it should use all occurrences of `x` and `x+1` to maximize it's
length, so `len(B) = count[x] + count[x+1]`.  
Additionally, it must use `x` and `x+1` atleast once, so `count[x]` and
`count[x+1]` should both be positive.

    
    
    def findLHS(self, A):
        count = collections.Counter(A)
        ans = 0
        for x in count:
            if x+1 in count:
                ans = max(ans, count[x] + count[x+1])
        return ans
    

Alternatively, we can count values in a straightforward way using a
dictionary: replacing our first line of `count = collections.Counter(A)` with:

    
    
    count = {}
    for x in A:
        count[x] = count.get(x, 0) + 1
    


### Solution 3
  1. run time O(n) space O(n) use unordered_map Two pass solution  
First loop through all elements and count each number appearance. Then loop
through unordered_map, to find if the key - 1 is in the unordered map(To avoid
counting twice, we only find if key - 1 in the map instead of finding key + 1
and key -1). If key - 1 and key both in the map, update the result

    
    
        int findLHS(vector<int>& nums) {
            unordered_map<int,int>m;
            for(auto i: nums)
                m[i]++;
            int res = 0;
            for(auto it:m)
                if(m.count(it.first-1)>0)
                    res = max(res, it.second+m[it.first-1]);
            return res;
        }
    

  2. run time O(n) space O(n) use unordered_map One pass solution  
Loop through all elements and count each number appearance. And find if key -
1 or key + 1 in the unordered_map(because key+1 and key-1 may appear before
key in nums). If either in the unordered_map, update the result.

    
    
        int findLHS(vector<int>& nums) {
            unordered_map<int,int>m;
            int res = 0;
            for(auto i: nums){
                m[i]++;
                if(m.count(i+1))
                    res = max(res, m[i] + m[i+1]);
                if(m.count(i-1))
                    res = max(res, m[i] + m[i-1]);
            }
            return res;        
        }
    
    

  3. O(nlogn) running time ,space O(1) using sort  
The idea is to loop through each elements and update the result. The start
position is used for counting purpose and new start is used for whenever come
across different number

When the number is different from previous number, update the new start
position. When difference between current position and start position is
bigger than 1 then update start position.

    
    
           int findLHS(vector<int>& nums) {
            sort(nums.begin(),nums.end());
            int len = 0;
            for(int i = 1, start = 0, new_start = 0; i<nums.size(); i++)
            {
    
                if (nums[i] - nums[start] > 1)    
                    start = new_start;
                if (nums[i] != nums[i-1]) 
                    new_start = i;
                if(nums[i] - nums[start] == 1)
                    len = max(len, i-start+1);
            }
            return len;
    



