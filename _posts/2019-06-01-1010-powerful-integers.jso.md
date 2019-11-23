---
layout: post
title: 1010. Powerful Integers
---
### Question
Given two positive integers `x` and `y`, an integer is _powerful_  if it is
equal to `x^i + y^j` for some integers `i >= 0` and `j >= 0`.

Return a list of all _powerful_ integers that have value less than or equal to
`bound`.

You may return the answer in any order.  In your answer, each value should
occur at most once.



 **Example 1:**

    
    
     **Input:** x = 2, y = 3, bound = 10
    **Output:** [2,3,4,5,7,9,10]
    **Explanation:**
    2 = 2^0 + 3^0
    3 = 2^1 + 3^0
    4 = 2^0 + 3^1
    5 = 2^1 + 3^1
    7 = 2^2 + 3^1
    9 = 2^3 + 3^0
    10 = 2^0 + 3^2
    

**Example 2:**

    
    
    **Input:** x = 3, y = 5, bound = 15
    **Output:** [2,4,6,8,10,14]
    



 **Note:**

  * `1 <= x <= 100`
  * `1 <= y <= 100`
  * `0 <= bound <= 10^6`

### Solution 1
    
    
     class Solution {
        public List<Integer> powerfulIntegers(int x, int y, int bound) {
            Set<Integer> result = new HashSet<>();
            for (int a = 1; a < bound; a *= x) {
                for (int b = 1; a + b <= bound; b *= y) {
                    result.add(a + b);
                    if (y == 1) {
                        break;
                    }
                }
                if (x == 1) {
                    break;
                }
            }
            return new ArrayList<>(result);
        }
    }
    


### Solution 2
**Java:**

    
    
         public List<Integer> powerfulIntegers(int x, int y, int bound) {
            Set<Integer> s = new HashSet<>();
            for (int i = 1; i < bound; i *= x > 1? x : bound + 1)
                for (int j = 1; i + j <= bound; j *= y > 1 ? y : bound + 1)
                    s.add(i + j);
            return new ArrayList<>(s);
        }
    

**C++:**

    
    
         vector<int> powerfulIntegers(int x, int y, int bound) {
            unordered_set<int> s;
            for (int i = 1; i <= bound; i *= x) {
                for (int j = 1; i + j <= bound; j *= y) {
                    s.insert(i + j);
                    if (y == 1) break;
                }
                if (x == 1) break;
    
            }
            return vector<int>(s.begin(), s.end());
        }
    

**Python:**

    
    
        def powerfulIntegers(self, x, y, bound):
            xs = {x** i for i in range(20) if x**i < bound}
            ys = {y**i for i in range(20) if y**i < bound}
            return list({i + j for i in xs for j in ys if i + j <= bound})
    


### Solution 3
    
    
    class Solution(object):
        def powerfulIntegers(self, x, y, bound):
            """
            :type x: int
            :type y: int
            :type bound: int
            :rtype: List[int]
            """
            s = set()
            stack = [(0, 0)]
            while stack:
                i, j = stack.pop()
                t = x ** i + y ** j
                if t <= bound:
                    s.add(t)
                    if x > 1:
                        stack.append((i+1, j))
                    if y > 1:
                        stack.append((i, j+1))
            
            return list(s)
    



