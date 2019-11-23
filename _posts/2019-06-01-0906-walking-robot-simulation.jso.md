---
layout: post
title: 906. Walking Robot Simulation
---
### Question
A robot on an infinite grid starts at point (0, 0) and faces north.  The robot
can receive one of three possible types of commands:

  * `-2`: turn left 90 degrees
  * `-1`: turn right 90 degrees
  * `1 <= x <= 9`: move forward `x` units

Some of the grid squares are obstacles.

The `i`-th obstacle is at grid point `(obstacles[i][0], obstacles[i][1])`

If the robot would try to move onto them, the robot stays on the previous grid
square instead (but still continues following the rest of the route.)

Return the **square** of the maximum Euclidean distance that the robot will be
from the origin.



 **Example 1:**

    
    
     **Input:** commands = [4,-1,3], obstacles = []
    **Output:** 25
    Explanation: robot will go to (3, 4)
    

**Example 2:**

    
    
    **Input:** commands = [4,-1,4,-2,4], obstacles = [[2,4]]
    **Output:** 65
    **Explanation** : robot will be stuck at (1, 4) before turning left and going to (1, 8)
    



 **Note:**

  1. `0 <= commands.length <= 10000`
  2. `0 <= obstacles.length <= 10000`
  3. `-30000 <= obstacle[i][0] <= 30000`
  4. `-30000 <= obstacle[i][1] <= 30000`
  5. The answer is guaranteed to be less than `2 ^ 31`.

### Solution 1
I spent nearly one hour to find this word! looooool

    
    
    class Solution {
        public int robotSim(int[] commands, int[][] obstacles) {
            Set<String> set = new HashSet<>();
            for (int[] obs : obstacles) {
                set.add(obs[0] + " " + obs[1]);
            }
            int[][] dirs = new int[][]{{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
            int d = 0, x = 0, y = 0, result = 0;
            for (int c : commands) {
                if (c == -1) {
                    d++;
                    if (d == 4) {
                        d = 0;
                    }
                } else if (c == -2) {
                    d--;
                    if (d == -1) {
                        d = 3;
                    }
                } else {
                    while (c-- > 0 && !set.contains((x + dirs[d][0]) + " " + (y + dirs[d][1]))) {
                        x += dirs[d][0];
                        y += dirs[d][1];
                    }
                }
                result = Math.max(result, x * x + y * y);
            }
            return result;
        }
    }
    


### Solution 2
**Logical Thinking**  
The robot starts at point (0, 0) and faces north. Which edge of grid is to the
north?  
Since it will go to point (3, 4) with commands = [4,-1,3], obstacles = [], we
know that the right edge is to the `North`.

    
    
         W
      S -|- N
         E
    

How do we represent absolute orientations given only relative turning
directions(i.e., left or right)? We define `direction` indicates the absolute
orientation as below:

    
    
    North, direction = 0, directions[direction] = {0, 1}
    East,  direction = 1, directions[direction] = {1, 0}
    South, direction = 2, directions[direction] = {0, -1}
    West,  direction = 3, directions[direction] = {-1, 0}
    
    direction will increase by one when we turn right, 
    and will decrease by one when we turn left.
    

In this way, if the robot faces South, i.e., its direction is 2, when it moves
forward by one step, x += 0, y += -1 for directions[2] = {0, -1}.

**Clear Java Code**

    
    
         private static int[][] directions = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
    
        public int robotSim(int[] commands, int[][] obstacles) {
    
            Set<String> obstaclesRowToCols = new HashSet<>();
            for (int[] obstacle : obstacles) {
                obstaclesRowToCols.add(obstacle[0] + " " + obstacle[1]);
            }
    
            int x = 0, y = 0, direction = 1, maxDistSquare = 0;
            for (int i = 0; i < commands.length; i++) {
                if (commands[i] == -2) {
                    direction--;
                    if (direction < 0) {
                        direction += 4;
                    }
                } else if (commands[i] == -1) {
                    direction++;
                    direction %= 4;
                } else {
                    int step = 0;
                    while (step < commands[i] && (!obstaclesRowToCols.contains((x + directions[direction][0]) + " " + (y + directions[direction][1])))) {
                        x += directions[direction][0];
                        y += directions[direction][1];
                        step++;
                    }
                }
                maxDistSquare = Math.max(maxDistSquare, x * x + y * y);
            }
    
            return maxDistSquare;
        }
    

**I would appreciate your VOTE UP ;)**


### Solution 3
  * Firstly, robot doesn't face **north** as stated in the explanation. When i consider north, i imagine robot faces upwards but its initial direction is **to right** actually.
  * Note: This is a similar question to **Robot Room cleaner (No: 489)**.
  *  **Code explanation** :
    * At first, we should make obstacles as **set** for **O(1)** check for obstacles and we simply move according to command if there is no obstacle.
    *  **Critical part** is the **move array** in my opinion. It is a simplified move to **next i and j** in array form for **"right", "up", "left", "down" respectively.**
    * We also change **d** variable, which is **direction** variable as move index, when we get -2 or -1 command.
    * If command is -2, we should turn **left** , which is to **increment direction index.**
    * If command is -1, we should turn **right** , which is to **decrement direction index.**
    * Else, we can walk through untill face an obstacle or not.
    * And we update **mx value** in each move also.

    
    
    class Solution:
        def robotSim(self, commands, obstacles):
            i =  j = mx = d = 0
            move, obstacles = [(0, 1), (-1, 0), (0, -1), (1, 0), ], set(map(tuple, obstacles))
            for command in commands:
                if command == -2: d = (d + 1) % 4
                elif command == -1: d = (d - 1) % 4
                else:
                    x, y = move[d]
                    while command and (i + x, j + y) not in obstacles:
                        i += x
                        j += y
                        command -= 1
                mx = max(mx, i ** 2 + j ** 2)
            return mx
    



