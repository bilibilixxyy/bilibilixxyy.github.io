---
layout: post
title: 657. Robot Return to Origin
---
### Question
There is a robot starting at position (0, 0), the origin, on a 2D plane. Given
a sequence of its moves, judge if this robot **ends up at (0, 0)** after it
completes its moves.

The move sequence is represented by a string, and the character moves[i]
represents its ith move. Valid moves are R (right), L (left), U (up), and D
(down). If the robot returns to the origin after it finishes all of its moves,
return true. Otherwise, return false.

 **Note** : The way that the robot is "facing" is irrelevant. "R" will always
make the robot move to the right once, "L" will always make it move left, etc.
Also, assume that the magnitude of the robot's movement is the same for each
move.

 **Example 1:**

    
    
     **Input:** "UD"
    **Output:** true 
    **Explanation** : The robot moves up once, and then down once. All moves have the same magnitude, so it ended up at the origin where it started. Therefore, we return true.
    



**Example 2:**

    
    
    **Input:** "LL"
    **Output:** false
    **Explanation** : The robot moves left twice. It ends up two "moves" to the left of the origin. We return false because it is not at the origin at the end of its moves.
    

### Solution 1
    
    
     def judgeCircle(self, moves):
        return moves.count('L') == moves.count('R') and moves.count('U') == moves.count('D')
    


### Solution 2
**C++**

    
    
     class Solution {
    public:
        bool judgeCircle(string moves) {
            int v = 0;
            int h = 0;
            for (char ch : moves) {
                switch (ch) {
                    case 'U' : v++; break;
                    case 'D' : v--; break;
                    case 'R' : h++; break;
                    case 'L' : h--; break;
                }
            }
            return v == 0 && h == 0;
        }
    };
    

**Java**

    
    
     public class Solution {
        public boolean judgeCircle(String moves) {
            int x = 0;
            int y = 0;
            for (char ch : moves.toCharArray()) {
                if (ch == 'U') y++;
                else if (ch == 'D') y--;
                else if (ch == 'R') x++;
                else if (ch == 'L') x--;
            }
            return x == 0 && y == 0;
        }
    }
    


### Solution 3
There are a few problems has vague descriptions which is quite confusing that
make the contestants to submit multiple of time to "guess" or "try out" what
is the real meaning of the problem.

It's remotely what we want in Leetcode. So please STOP making this kind of
problem again. It's no more than a waste of our time.

THANK YOU!



