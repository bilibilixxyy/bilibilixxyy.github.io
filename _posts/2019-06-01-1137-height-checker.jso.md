---
layout: post
title: 1137. Height Checker
---
### Question
Students are asked to stand in non-decreasing order of heights for an annual
photo.

Return the minimum number of students not standing in the right positions.
(This is the number of students that must move in order for all students to be
standing in non-decreasing order of height.)



 **Example 1:**

    
    
     **Input:** [1,1,4,2,1,3]
    **Output:** 3
    **Explanation:**
    Students with heights 4, 3 and the last 1 are not standing in the right positions.
    



 **Note:**

  1. `1 <= heights.length <= 100`
  2. `1 <= heights[i] <= 100`

### Solution 1
If you consider the input  
[1,2,1,2,1,1,1,2,1]

LeetCode's solution (at least in today's contest) was 4 moves for the above
problem. And it's coming from the sort and compare solution strategy or
something similar. But the minimum number of student moves for them to be in
order of non-decreasing height is 3. You move each 2 to the end of the array
one by one and you can sort the array in 3 student moves. I think the
definition should just be how many students are out of order.


### Solution 2
Just count the frequency of each height (using HashMap or int[] as the height
is promised to be within range[1, 100]) and use 2 pointers to make comparison:

    
    
    class Solution {
        public int heightChecker(int[] heights) {
            int[] heightToFreq = new int[101];
            
            for (int height : heights) {
                heightToFreq[height]++;
            }
            
            int result = 0;
            int curHeight = 0;
            
            for (int i = 0; i < heights.length; i++) {
                while (heightToFreq[curHeight] == 0) {
                    curHeight++;
                }
                
                if (curHeight != heights[i]) {
                    result++;
                }
                heightToFreq[curHeight]--;
            }
            
            return result;
        }
    }
    


### Solution 3
The problem asks us to figure out how many students are out of place in terms
of ranking them from shortest to tallest. I create a clone of the input array
and sort it. By sorting the cloned array, we can see what our array should
look like if each student were to be correctly ordered by height. After this
point the problem becomes very simple because we just need to iterate from i =
0 to n-1 and compare the value at i in each array to see if they are the same.
If they are not the same, then that means that a student in the input array is
not in their correct spot, therefore count is incremented by 1. The time
complexity is O(nlogn) because it is bounded by the Java sort method, which
runs in O(nlogn) time.

    
    
    class Solution {
        public int heightChecker(int[] heights) {
            int[] copy = heights.clone();
            Arrays.sort(copy);
            int count = 0;
            for(int i = 0; i < copy.length; i++){
                if(heights[i]!=copy[i])count++;
            }
            return count;
        }
    }
    



