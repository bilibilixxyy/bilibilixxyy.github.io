---
layout: post
title: 461. Hamming Distance
---
### Question
The [Hamming distance](https://en.wikipedia.org/wiki/Hamming_distance) between
two integers is the number of positions at which the corresponding bits are
different.

Given two integers `x` and `y`, calculate the Hamming distance.

 **Note:**  
0 ≤ `x`, `y` < 231.

 **Example:**

    
    
     **Input:** x = 1, y = 4
    
    **Output:** 2
    
    **Explanation:**
    1   (0 0 0 1)
    4   (0 1 0 0)
           ↑   ↑
    
    The above arrows point to positions where the corresponding bits are different.
    

### Solution 1
What does come to your mind first when you see this sentence `"corresponding
bits are different"`? Yes, `XOR`! Also, do not forget there is a decent
function Java provided: `Integer.bitCount()` ~~~

    
    
    public class Solution {
        public int hammingDistance(int x, int y) {
            return Integer.bitCount(x ^ y);
        }
    }
    


### Solution 2
    
    
    class Solution {
    public:
        int hammingDistance(int x, int y) {
            int dist = 0, n = x ^ y;
            while (n) {
                ++dist;
                n &= n - 1;
            }
            return dist;
        }
    };
    
    


### Solution 3
    
    
        def hammingDistance(self, x, y):
            """
            :type x: int
            :type y: int
            :rtype: int
            """
            return bin(x^y).count('1')
    



