---
layout: post
title: 879. Maximize Distance to Closest Person
---
### Question
In a row of `seats`, `1` represents a person sitting in that seat, and `0`
represents that the seat is empty.

There is at least one empty seat, and at least one person sitting.

Alex wants to sit in the seat such that the distance between him and the
closest person to him is maximized.

Return that maximum distance to closest person.

 **Example 1:**

    
    
     **Input:** [1,0,0,0,1,0,1]
    **Output:** 2
    **Explanation:**
    If Alex sits in the second open seat (seats[2]), then the closest person has distance 2.
    If Alex sits in any other open seat, the closest person has distance 1.
    Thus, the maximum distance to the closest person is 2.

**Example 2:**

    
    
    **Input:** [1,0,0,0]
    **Output:** 3
    **Explanation:**
    If Alex sits in the last seat, the closest person is 3 seats away.
    This is the maximum distance possible, so the answer is 3.
    

**Note:**

  1. `1 <= seats.length <= 20000`
  2. `seats` contains only 0s or 1s, at least one `0`, and at least one `1`.

### Solution 1
##  **Explanation**

`last` is the index of last seated seat.  
Loop on all seats, when we met a people, we count the distance from the
`last`.  
The final result = max(distance at the beginning, distance in the middle / 2,
distance at the end).

##  **Explanation**

Time O(N) Space O(1)

 **Java:**

    
    
         public int maxDistToClosest(int[] seats) {
            int res = 0, n = seats.length, last = -1;
            for (int i = 0; i < n; ++i) {
                if (seats[i] == 1) {
                    res = last < 0 ? i : Math.max(res, (i - last) / 2);
                    last = i;
                }
            }
            res = Math.max(res, n - last - 1);
            return res;
        }
    

**C++:**

    
    
         int maxDistToClosest(vector<int> seats) {
            int res = 0, n = seats.size(), last = -1;
            for (int i = 0; i < n; ++i) {
                if (seats[i] == 1) {
                    res = last < 0 ? i : max(res, (i - last) / 2);
                    last = i;
                }
            }
            res = max(res, n - last - 1);
            return res;
        }
    

**Python:**

    
    
         def maxDistToClosest(self, seats):
            res, last, n = 0, -1, len(seats)
            for i in range(n):
                if seats[i]:
                    res = max(res, i if last < 0 else (i - last) / 2)
                    last = i
            return max(res, n - last - 1)
    


### Solution 2
Idea is simple. Use two pointers.

  1. If the current value is "0", keep going forward.
  2. Left pointer points to the position of left "1" and right pointer points to the position of right "1". Keep updating two pointers and calculate the max distance.
  3. Be careful of two situations: seats[0] is 0 and seats[len - 1] is 0. Just check them and get the final answer. Ex: 00101000.

    
    
    class Solution {
        public int maxDistToClosest(int[] seats) {
            int left = -1, maxDis = 0;
            int len = seats.length;
            
            for (int i = 0; i < len; i++) {
                if (seats[i] == 0) continue;
    
                if (left == -1) {
                    maxDis = Math.max(maxDis, i);
                } else {
                    maxDis = Math.max(maxDis, (i - left) / 2);
                }
                left = i;
            }
            
            if (seats[len - 1] == 0) {
                maxDis = Math.max(maxDis, len - 1 - left);
            }
            
            return maxDis;
        }
    }
    


### Solution 3
    
    
    class Solution {// 0 0 0 1
        public int maxDistToClosest(int[] nums) {
            int n = nums.length;
            int max = 0;
            int i = 0;
            while(i < n){
                while(i < n && nums[i] == 1){
                    i++;
                }
                int j = i;//start
                while(i < n && nums[i] == 0){
                    i++;
                }
                if(j == 0 || i == n){
                    max = Math.max(max, i - j);
                }else{
                    max = Math.max(max, (i - j + 1) / 2) ;
                }
            }
            return max;
        }
    }
    



