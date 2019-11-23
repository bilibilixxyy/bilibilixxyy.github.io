---
layout: post
title: 495. Teemo Attacking
---
### Question
In LOL world, there is a hero called Teemo and his attacking can make his
enemy Ashe be in poisoned condition. Now, given the Teemo's attacking
**ascending** time series towards Ashe and the poisoning time duration per
Teemo's attacking, you need to output the total time that Ashe is in poisoned
condition.

You may assume that Teemo attacks at the very beginning of a specific time
point, and makes Ashe be in poisoned condition immediately.

 **Example 1:**

    
    
     **Input:** [1,4], 2
    **Output:** 4
    **Explanation:** At time point 1, Teemo starts attacking Ashe and makes Ashe be poisoned immediately. 
    This poisoned status will last 2 seconds until the end of time point 2. 
    And at time point 4, Teemo attacks Ashe again, and causes Ashe to be in poisoned status for another 2 seconds. 
    So you finally need to output 4.
    



**Example 2:**

    
    
    **Input:** [1,2], 2
    **Output:** 3
    **Explanation:** At time point 1, Teemo starts attacking Ashe and makes Ashe be poisoned. 
    This poisoned status will last 2 seconds until the end of time point 2. 
    However, at the beginning of time point 2, Teemo attacks Ashe again who is already in poisoned status. 
    Since the poisoned status won't add up together, though the second poisoning attack will still work at time point 2, it will stop at the end of time point 3. 
    So you finally need to output 3.
    



 **Note:**

  1. You may assume the length of given time series array won't exceed 10000.
  2. You may assume the numbers in the Teemo's attacking time series and his poisoning time duration per attacking are non-negative integers, which won't exceed 10,000,000.

### Solution 1
![alt text](https://encrypted-tbn2.gstatic.com/images?q=tbn:ANd9GcSa-
yd3K7uW2ocdpJ-7mA3kMyF-XLUjkVproRQxIzhrRzh_osOW)

    
    
    class Solution(object):
        def findPoisonedDuration(self, timeSeries, duration):
            ans = duration * len(timeSeries)
            for i in range(1,len(timeSeries)):
                ans -= max(0, duration - (timeSeries[i] - timeSeries[i-1]))
            return ans
    

![alt text](https://www.baronsteal.net/images/champion/loading/Ashe_4.jpg)


### Solution 2
The same idea as <https://leetcode.com/problems/merge-intervals/>  
Algorithm:

  1. Use two variable to record current start and end point.
  2. If the start of new interval is greater than current end, meaning NO overlapping, we can sum the current interval length to result and then update start and end.
  3. Otherwise just update the current end;

    
    
    public class Solution {
        public int findPosisonedDuration(int[] timeSeries, int duration) {
            if (timeSeries == null || timeSeries.length == 0 || duration == 0) return 0;
            
            int result = 0, start = timeSeries[0], end = timeSeries[0] + duration;
            for (int i = 1; i < timeSeries.length; i++) {
                if (timeSeries[i] > end) {
                    result += end - start;
                    start = timeSeries[i];
                }
                end = timeSeries[i] + duration;
            }
            result += end - start;
            
            return result;
        }
    }
    


### Solution 3
One of the reasons I prefer Leetcode to other coding sites is that the
questions are presented in a straightforward way, if it's about trees or
arrays then that is what is asked in the question. Unfortunately that is not
the case here and the real logic is dressed up with a story about Teemo and
Ashe (which I have never heard of). I would prefer to get straight to the
point and for the title of the question to convey some meaning about what it
is about.

However, the underlying problem is simple, I would not say this was "medium"

    
    
        def findPosisonedDuration(self, timeSeries, duration):
            if not timeSeries:
                return 0
            poisoned = duration
            for i in range(1, len(timeSeries)):
                poisoned += duration - max(0, timeSeries[i-1]+duration - timeSeries[i])
            return poisoned



