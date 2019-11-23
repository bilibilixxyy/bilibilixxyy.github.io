---
layout: post
title: 913. Random Flip Matrix
---
### Question
You are given the number of rows `n_rows` and number of columns `n_cols` of a
2D binary matrix where all values are initially 0. Write a function `flip`
which chooses a 0 value [uniformly at
random](https://en.wikipedia.org/wiki/Discrete_uniform_distribution), changes
it to 1, and then returns the position `[row.id, col.id]` of that value. Also,
write a function `reset` which sets all values back to 0.  **Try to minimize
the number of calls to system's Math.random()** and optimize the time and
space complexity.

Note:

  1. `1 <= n_rows, n_cols <= 10000`
  2. `0 <= row.id < n_rows` and `0 <= col.id < n_cols`
  3. `flip` will not be called when the matrix has no 0 values left.
  4. the total number of calls to `flip` and `reset` will not exceed 1000.

 **Example 1:**

    
    
     **Input:** ["Solution","flip","flip","flip","flip"]
    [[2,3],[],[],[],[]]
    **Output:** [null,[0,1],[1,2],[1,0],[1,1]]
    

**Example 2:**

    
    
    **Input:** ["Solution","flip","flip","reset","flip"]
    [[1,2],[],[],[],[]]
    **Output:** [null,[0,0],[0,1],null,[0,0]]

 **Explanation of Input Syntax:**

The input is two lists: the subroutines called and their arguments.
`Solution`'s constructor has two arguments, `n_rows` and `n_cols`. `flip` and
`reset` have no arguments. Arguments are always wrapped with a list, even if
there aren't any.

### Solution 1
Only call once every time

    
    
    class Solution {
    
        Map<Integer, Integer> map;
        int rows, cols, total;
        Random rand;
        
        public Solution(int n_rows, int n_cols) {
            map = new HashMap<>();
            rand = new Random();
            rows = n_rows; 
            cols = n_cols; 
            total = rows * cols;
        }
        
        public int[] flip() {
            int r = rand.nextInt(total--);
            int x = map.getOrDefault(r, r);
            map.put(r, map.getOrDefault(total, total));
            return new int[]{x / cols, x % cols};
        }
        
        public void reset() {
            map.clear();
            total = rows * cols;
        }
    }
    


### Solution 2
We will be using modern method of the Fisher–Yates shuffle.

Please read how the original algorithm works first:
[Examples](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle#Examples)

So, the steps are:

  * generate random from 0 to n : m
  * swap m and n
  * decrease n

    
    
    class Solution {
        Map<Integer, Integer> map;
        int rows, cols, total;
        Random rand;
        
        public Solution(int n_rows, int n_cols) {
            map = new HashMap<>();
            rand = new Random();
            rows = n_rows; 
            cols = n_cols; 
            total = rows * cols;
        }
        
        public int[] flip() {
            // generate index, decrease total number of values
            int r = rand.nextInt(total--); 
            // check if we have already put something at this index
            int x = map.getOrDefault(r, r); 
            // swap - put total at index that we generated
            map.put(r, map.getOrDefault(total, total)); 
            return new int[]{x / cols, x % cols}; 
        }
        
        public void reset() {
            map.clear();
            total = rows * cols; 
        }
    }
    


### Solution 3
This is a sampling n elements without replacement problem. It is the same as
the operation that random shuffe an array and then return the first n
elements.

Here come the trick. When we random pick an element in the array we can store
its new position in a hash table instead of the array because n is extremely
less than the total num. So we can accomplish this within O(1) time and O(k)
space where k is the maxium call of flip.

    
    
    class Solution:
    
        def __init__(self, n_rows, n_cols):
            """
            :type n_rows: int
            :type n_cols: int
            """
            self.c = n_cols
            self.end = n_rows * n_cols - 1
            self.d = {}
            self.start = 0
            
        def flip(self):
            """
            :rtype: List[int]
            """
            rand = random.randint(self.start, self.end)
            res = self.d.get(rand, rand)
            self.d[rand] = self.d.get(self.start, self.start)
            self.start += 1
            return divmod(res, self.c)
        
        def reset(self):
            """
            :rtype: void
            """
            self.d = {}
            self.start = 0
    



