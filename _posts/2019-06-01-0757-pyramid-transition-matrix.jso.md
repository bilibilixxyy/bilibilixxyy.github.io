---
layout: post
title: 757. Pyramid Transition Matrix
---
### Question
We are stacking blocks to form a pyramid. Each block has a color which is a
one letter string.

We are allowed to place any color block `C` on top of two adjacent blocks of
colors `A` and `B`, if and only if `ABC` is an allowed triple.

We start with a bottom row of `bottom`, represented as a single string. We
also start with a list of allowed triples `allowed`. Each allowed triple is
represented as a string of length 3.

Return true if we can build the pyramid all the way to the top, otherwise
false.

 **Example 1:**

    
    
     **Input:** bottom = "BCD", allowed = ["BCG", "CDE", "GEA", "FFF"]
    **Output:** true
    **Explanation:**
    We can stack the pyramid like this:
        A
       / \
      G   E
     / \ / \
    B   C   D
    
    We are allowed to place G on top of B and C because BCG is an allowed triple.  Similarly, we can place E on top of C and D, then A on top of G and E.



**Example 2:**

    
    
    **Input:** bottom = "AABA", allowed = ["AAA", "AAB", "ABA", "ABB", "BAC"]
    **Output:** false
    **Explanation:**
    We can't stack the pyramid to the top.
    Note that there could be allowed triples (A, B, C) and (A, B, D) with C != D.
    



 **Note:**

  1. `bottom` will be a string with length in range `[2, 8]`.
  2. `allowed` will have length in range `[0, 200]`.
  3. Letters in all strings will be chosen from the set `{'A', 'B', 'C', 'D', 'E', 'F', 'G'}`.

### Solution 1
A little long, but very straightforward.

    
    
    class Solution {
        public boolean pyramidTransition(String bottom, List<String> allowed) {
            Map<String, List<String>> map = new HashMap<>();
            for (String s : allowed) {
                String key = s.substring(0,2);
                if (!map.containsKey(key)) map.put(key, new ArrayList<String>());
                map.get(key).add(s.substring(2));
            }
            
            return helper(bottom, map);
        }
        
        private boolean helper(String bottom, Map<String, List<String>> map) {
            if(bottom.length() == 1) return true;
            for (int i = 0; i<bottom.length()-1; i++) {
                if (!map.containsKey(bottom.substring(i,i+2))) return false;
            }
            List<String> ls = new ArrayList<>();
            getList(bottom, 0, new StringBuilder(), ls, map);
            for (String s : ls) {
                if (helper(s, map)) return true;
            }
            return false;
        }
        
        private void getList(String bottom, int idx, StringBuilder sb, List<String> ls, Map<String, List<String>> map) {
            if (idx == bottom.length() - 1) {
                ls.add(sb.toString());
                return;
            }
            for (String s : map.get(bottom.substring(idx, idx+2))) {
                sb.append(s);
                getList(bottom, idx + 1, sb, ls, map);
                sb.deleteCharAt(sb.length()-1);
            }
        }
    }
    


### Solution 2
    
    
    class Solution(object):
    
        def pyramidTransition(self, bottom, allowed):
            f = collections.defaultdict(lambda: defaultdict(list))
            for a, b, c in allowed: f[a][b].append(c)
    
            def pyramid(bottom):
                if len(bottom) == 1: return True
                for i in itertools.product(*(f[a][b] for a, b in zip(bottom, bottom[1:]))):
                    if pyramid(i): return True
                return False
            return pyramid(bottom)
    

Make `pyramid` into one line if you like:

    
    
    def pyramid(bottom):
                return len(bottom) == 1 or any(pyramid(i) for i in product(*(f[a][b] for a, b in zip(bottom, bottom[1:]))))
    


### Solution 3
you're probably gonna read this 1 like 5 times. I'm skipping this one



