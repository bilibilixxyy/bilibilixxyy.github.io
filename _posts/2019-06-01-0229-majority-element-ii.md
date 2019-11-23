---
layout: post
title: 229. Majority Element II
---
### Question
Given an integer array of size _n_ , find all elements that appear more than
`⌊ n/3 ⌋` times.

 **Note:** The algorithm should run in linear time and in O(1) space.

 **Example 1:**

    
    
     **Input:** [3,2,3]
    **Output:** [3]

**Example 2:**

    
    
    **Input:** [1,1,1,3,3,2,2,2]
    **Output:** [1,2]

### Solution 1
For those who aren't familiar with Boyer-Moore Majority Vote algorithm,  
I found a great article (<http://goo.gl/64Nams>) that helps me to understand
this fantastic algorithm!!  
Please check it out!

The essential concepts is you keep a counter for the majority number **X**. If
you find a number **Y** that is not **X** , the current counter should deduce
1. The reason is that if there is 5 **X** and 4 **Y** , there would be one
(5-4) more **X** than **Y**. This could be explained as "4 **X** being paired
out by 4 **Y** ".

And since the requirement is finding the majority for more than ceiling of
[n/3], the answer would be less than or equal to two numbers.  
So we can modify the algorithm to maintain two counters for two majorities.

Followings are my sample Python code:

    
    
    class Solution:
    # @param {integer[]} nums
    # @return {integer[]}
    def majorityElement(self, nums):
        if not nums:
            return []
        count1, count2, candidate1, candidate2 = 0, 0, 0, 1
        for n in nums:
            if n == candidate1:
                count1 += 1
            elif n == candidate2:
                count2 += 1
            elif count1 == 0:
                candidate1, count1 = n, 1
            elif count2 == 0:
                candidate2, count2 = n, 1
            else:
                count1, count2 = count1 - 1, count2 - 1
        return [n for n in (candidate1, candidate2)
                        if nums.count(n) > len(nums) // 3]


### Solution 2
    
    
    	public List<Integer> majorityElement(int[] nums) {
    	if (nums == null || nums.length == 0)
    		return new ArrayList<Integer>();
    	List<Integer> result = new ArrayList<Integer>();
    	int number1 = nums[0], number2 = nums[0], count1 = 0, count2 = 0, len = nums.length;
    	for (int i = 0; i < len; i++) {
    		if (nums[i] == number1)
    			count1++;
    		else if (nums[i] == number2)
    			count2++;
    		else if (count1 == 0) {
    			number1 = nums[i];
    			count1 = 1;
    		} else if (count2 == 0) {
    			number2 = nums[i];
    			count2 = 1;
    		} else {
    			count1--;
    			count2--;
    		}
    	}
    	count1 = 0;
    	count2 = 0;
    	for (int i = 0; i < len; i++) {
    		if (nums[i] == number1)
    			count1++;
    		else if (nums[i] == number2)
    			count2++;
    	}
    	if (count1 > len / 3)
    		result.add(number1);
    	if (count2 > len / 3)
    		result.add(number2);
    	return result;
    }


### Solution 3
 **Solution**

I keep up to two candidates in my counter, so this fulfills the O(N) time and
O(1) space requirements.

    
    
    def majorityElement(self, nums):
        ctr = collections.Counter()
        for n in nums:
            ctr[n] += 1
            if len(ctr) == 3:
                ctr -= collections.Counter(set(ctr))
        return [n for n in ctr if nums.count(n) > len(nums)/3]
    

* * *

**Explanation**

Think of it this way: Find three different votes and hide them. Repeat until
there aren't three different votes left. A number that originally had more
than one third of the votes now still has at least one vote, because to hide
_all_ of its votes you would've had to hide more than three times one third of
the votes - more votes than there were. You can easily have false positives,
though, so in the end check whether the remaining up to two candidates
actually had more than one third of the votes.

My code does just that: Collect (count) the votes for every number, but remove
triples of three different votes on the fly, as soon as we have such a triple.

* * *

**Generalization to ⌊N/k⌋, still O(N) time but O(k) space**

For the general problem, looking for elements appearing more than ⌊N/k⌋ times
for some positive integer k, I just have to change my `3` to `k`. Then it
already works and takes takes O(k) space and O(kN) time.

The O(kN) time does **not** come from the main loop, though. Yes, each `ctr -=
...` does cost k, but I only have to do it at most N/k times. To put it in
terms of the above explanation, I can't hide a vote more than once.

No, the culprit is my last line, counting each remaining candidate separately.
If I count them at the same time, I get O(N) again. Here's the full
generalized code:

    
    
    def majorityElement(self, nums, k):
        ctr = collections.Counter()
        for n in nums:
            ctr[n] += 1
            if len(ctr) == k:
                ctr -= collections.Counter(set(ctr))
        ctr = collections.Counter(n for n in nums if n in ctr)
        return [n for n in ctr if ctr[n] > len(nums)/k]



