---
layout: post
title: 994. Prison Cells After N Days
---
### Question
There are 8 prison cells in a row, and each cell is either occupied or vacant.

Each day, whether the cell is occupied or vacant changes according to the
following rules:

  * If a cell has two adjacent neighbors that are both occupied or both vacant, then the cell becomes occupied.
  * Otherwise, it becomes vacant.

(Note that because the prison is a row, the first and the last cells in the
row can't have two adjacent neighbors.)

We describe the current state of the prison in the following way: `cells[i] ==
1` if the `i`-th cell is occupied, else `cells[i] == 0`.

Given the initial state of the prison, return the state of the prison after
`N` days (and `N` such changes described above.)



 **Example 1:**

    
    
     **Input:** cells = [0,1,0,1,1,0,0,1], N = 7
    **Output:** [0,0,1,1,0,0,0,0]
    **Explanation:** The following table summarizes the state of the prison on each day:
    Day 0: [0, 1, 0, 1, 1, 0, 0, 1]
    Day 1: [0, 1, 1, 0, 0, 0, 0, 0]
    Day 2: [0, 0, 0, 0, 1, 1, 1, 0]
    Day 3: [0, 1, 1, 0, 0, 1, 0, 0]
    Day 4: [0, 0, 0, 0, 0, 1, 0, 0]
    Day 5: [0, 1, 1, 1, 0, 1, 0, 0]
    Day 6: [0, 0, 1, 0, 1, 1, 0, 0]
    Day 7: [0, 0, 1, 1, 0, 0, 0, 0]
    
    

**Example 2:**

    
    
    **Input:** cells = [1,0,0,1,0,0,1,0], N = 1000000000
    **Output:** [0,0,1,1,1,1,1,0]
    



 **Note:**

  1. `cells.length == 8`
  2. `cells[i]` is in `{0, 1}`
  3. `1 <= N <= 10^9`

### Solution 1
## **Intuition** :

    
    
        public int[] prisonAfterNDays(int[] cells, int N) {
            while (N > 0) {
                N--;
                int[] cells2 = new int[8];
                for (int i = 1; i < 7; ++i)
                    cells2[i] = cells[i - 1] == cells[i + 1] ? 1 : 0;
                cells = cells2;
            }
            return cells;
        }
    

This is right solution, but it will get TLE when `N` is big.  
Note that `cells.length = 8`, and `cells[0]` and `cells[7]` will become `0`.  
In fact, `cells` have only 2 ^ 6 = 64 different states.  
And there will be a loop.  
  

## Solution 1

We record all seen states.  
Be careful,  
we need transform array to string as the key,  
otherwise it use the reference.

**Java:**

    
    
        public  int[] prisonAfterNDays(int[] cells, int N) {
            Map<String, Integer> seen = new HashMap<>();
            while (N > 0) {
                int[] cells2 = new int[8];
                seen.put(Arrays.toString(cells), N--);
                for (int i = 1; i < 7; ++i)
                    cells2[i] = cells[i - 1] == cells[i + 1] ? 1 : 0;
                cells = cells2;
                if (seen.containsKey(Arrays.toString(cells))) {
                    N %= seen.get(Arrays.toString(cells)) - N;
                }
            }
            return cells;
        }
    

**Python:**

    
    
        def prisonAfterNDays(self, cells, N):
            seen = {str(cells): N}
             while N:
                seen.setdefault(str(cells), N)
                N -= 1
                cells = [0] + [cells[i - 1] ^ cells[i + 1] ^ 1 for i in range(1, 7)] + [0]
                if str(cells) in seen:
                    N %= seen[str(cells)] - N
            return cells
    

  

## Solution 2

I tried to find the pattern of the loop.  
Well, the length of loop can be 1, 7, or 14.

So once we enter the loop, every 14 steps must be the same state.

The length of cells is even,  
so for any state, we can find a previous state.  
So all states are in a loop.

It means that, after a single step from the initial state, we enter the loop.

**Java**

    
    
         public int[] prisonAfterNDays(int[] cells, int N) {
            for (N = (N - 1) % 14 + 1; N > 0; --N) {
                int[] cells2 = new int[8];
                for (int i = 1; i < 7; ++i)
                    cells2[i] = cells[i - 1] == cells[i + 1] ? 1 : 0;
                cells = cells2;
            }
            return cells;
        }
    

**C++**

    
    
        vector< int> prisonAfterNDays(vector<int>& cells, int N) {
            for (N = (N - 1) % 14 + 1; N > 0; --N) {
                vector<int> cells2(8, 0);
                for (int i = 1; i < 7; ++i)
                    cells2[i] = cells[i - 1] == cells[i + 1] ? 1 : 0;
                cells = cells2;
            }
            return cells;
        }
    

**Python:**

    
    
        def prisonAfterNDays(self, cells, N):
            N -= max(N -  1, 0) / 14 * 14
            for i in xrange(N):
                cells = [0] + [cells[i - 1] ^ cells[i + 1] ^ 1 for i in range(1, 7)] + [0]
            return cells
    


### Solution 2
While the most voted solutions are smart and brilliant, I can't wrap my head
around to write their solutions in an actual interview. So here goes my
solution that is easy to understand:

1.Have a sub function nextDay() that finds the next day's cell states  
2.Iterate and store the cell states that occurred previously  
3.If there's no cycle, return. If there's a cycle, break the loop and rerun
N%cycle times to find the target cell states

    
    
    class Solution {
        public int[] prisonAfterNDays(int[] cells, int N) {
    		if(cells==null || cells.length==0 || N<=0) return cells;
            boolean hasCycle = false;
            int cycle = 0;
            HashSet<String> set = new HashSet<>(); 
            for(int i=0;i<N;i++){
                int[] next = nextDay(cells);
                String key = Arrays.toString(next);
                if(!set.contains(key)){ //store cell state
                    set.add(key);
                    cycle++;
                }
                else{ //hit a cycle
                    hasCycle = true;
                    break;
                }
                cells = next;
            }
            if(hasCycle){
                N%=cycle;
                for(int i=0;i<N;i++){
                    cells = nextDay(cells);
                }   
            }
            return cells;
        }
        
        private int[] nextDay(int[] cells){
            int[] tmp = new int[cells.length];
            for(int i=1;i<cells.length-1;i++){
                tmp[i]=cells[i-1]==cells[i+1]?1:0;
            }
            return tmp;
        }
    }
    

Complexity Analysis:  
As the cells have a fixed size of 8 but the first and last cell will not be
updated because they do not have two adjacent neighbors, we have at most 2^6 =
64 states. So regardless of input N, we have both space and time complexities
as O(1).


### Solution 3
We perform the simulation untill we find a cycle. Note that the cycle can
start after the first simulation (`f_c`), since edge cells can be occupied
initially.

Note that this solution does not rely on the cycle size (which can be 1, 7, or
14 for 8 cells). Therefore, it works for arbitrary number of cells, not just
8.

    
    
    vector<int> prisonAfterNDays(vector<int>& c, int N) {
      vector<int> f_c, next_c(c.size(), 0);
      for (int cycle = 0; N-- > 0; c = next_c, ++cycle) {
        for (auto i = 1; i < c.size() - 1; ++i) next_c[i] = c[i - 1] == c[i + 1];
        if (cycle == 0) f_c = next_c;
        else if (next_c == f_c) N %= cycle;
      }
      return c;
    }
    

Java version:

    
    
    public int[] prisonAfterNDays(int[] c, int N) {
      int[] f_c = new int[c.length], next_c = new int[c.length];
      for (int cycle = 0; N-- > 0; c = next_c.clone(), ++cycle) {
        for (int i = 1; i < c.length - 1; ++i) next_c[i] = (c[i - 1] == c[i + 1] ? 1 : 0);
        if (cycle == 0) f_c = next_c.clone();
        else if (Arrays.equals(next_c, f_c)) N %= cycle;
      }
      return c;
    }
    

# Complexity Analysis

Runtime: O(2 ^ n). For 8 cells, we can have 64 different states.  
Memory: O(n). We need to remember a single state of all cells for the loop
detection.



