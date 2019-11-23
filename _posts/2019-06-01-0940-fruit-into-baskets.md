---
layout: post
title: 940. Fruit Into Baskets
---
### Question
In a row of trees, the `i`-th tree produces fruit with type `tree[i]`.

You **start at any tree  of your choice**, then repeatedly perform the
following steps:

  1. Add one piece of fruit from this tree to your baskets.  If you cannot, stop.
  2. Move to the next tree to the right of the current tree.  If there is no tree to the right, stop.

Note that you do not have any choice after the initial choice of starting
tree: you must perform step 1, then step 2, then back to step 1, then step 2,
and so on until you stop.

You have two baskets, and each basket can carry any quantity of fruit, but you
want each basket to only carry one type of fruit each.

What is the total amount of fruit you can collect with this procedure?



 **Example 1:**

    
    
     **Input:** [1,2,1]
    **Output:** 3
    **Explanation:** We can collect [1,2,1].
    

**Example 2:**

    
    
    **Input:** [0,1,2,2]
    **Output:** 3
    **Explanation:** We can collect [1,2,2].
    If we started at the first tree, we would only collect [0, 1].
    

**Example 3:**

    
    
    **Input:** [1,2,3,2,2]
    **Output:** 4
    **Explanation:** We can collect [2,3,2,2].
    If we started at the first tree, we would only collect [1, 2].
    

**Example 4:**

    
    
    **Input:** [3,3,3,1,2,1,1,2,3,3,4]
    **Output:** 5
    **Explanation:** We can collect [1,2,1,1,2].
    If we started at the first tree or the eighth tree, we would only collect 4 fruits.
    



 **Note:**

  1. `1 <= tree.length <= 40000`
  2. `0 <= tree[i] < tree.length`

### Solution 1
If I start from a tree I can't stop and have to put the fruit in a basket, but
I want basket to have only one type of fruit. It is not clear if one needs to
stop after a 3rd type of fruit is encountered.

Since about 4 contests there is at least one question that is harder to
understand than to solve. Is it too hard to have someone proofread it before
posting?


### Solution 2
##  **Problem**

"Start from any index, we can collect at most two types of fruits. What is the
maximum amount"

##  **Translation**

Find out the longest length of subarrays with at most 2 different numbers?

Solution of sliding window will be easier to understand.  
Here I share another solution wihtout hash map.  
Hope it's not damn hard to understand.

##  **Explanation** :

Loop all fruit `c` in `tree`,  
Note that `a` and `b` are the last two different types of fruit that we met,  
`c` is the current fruit type,  
so it's something like "....aaabbbc..."

 **Case 1** `c == b`:  
fruit `c` already in the basket,  
and it's same as the last type of fruit  
`cur += 1`  
`count_b += 1`

 **Case 2** `c == a`:  
fruit `c` already in the basket,  
but it's not same as the last type of fruit  
`cur += 1`  
`count_b = 1`  
`a = b, b = c`

 **Case 3** `c != b && c!= a`:  
fruit `c` not in the basket,  
`cur = count_b + 1`  
`count_b = 1`  
`a = b, b = c`

Of course, in each turn we need to update `res = max(res, cur)`

##  **Complexity** :

`O(N)` time, `O(1)` space

 **C++:**

    
    
         int totalFruit(vector<int> tree) {
            int res = 0, cur = 0, count_b = 0, a = 0, b = 0;
            for (int c :  tree) {
                cur = c == a || c == b ? cur + 1 : count_b + 1;
                count_b = c == b ? count_b + 1 : 1;
                if (b != c) a = b, b = c;
                res = max(res, cur);
            }
            return res;
        }
    

**Java:**

    
    
         public int totalFruit(int[] tree) {
            int res = 0, cur = 0, count_b = 0, a = 0, b = 0;
            for (int c :  tree) {
                cur = c == a || c == b ? cur + 1 : count_b + 1;
                count_b = c == b ? count_b + 1 : 1;
                if (b != c) {a = b; b = c;}
                res = Math.max(res, cur);
            }
            return res;
        }
    

**Python:**

    
    
        def totalFruit(self, tree):
             res = cur = count_b = a = b = 0
            for c in tree:
                cur = cur + 1 if c in (a, b) else count_b + 1
                count_b = count_b + 1 if c == b else 1
                if b != c: a, b = b, c
                res = max(res, cur)
            return res
    


### Solution 3
**Problem**  
"Start from any index, we can collect at most two types of fruits. What is the
maximum amount"

 **Translation**  
Find out the longest length of subarrays with at most 2 different numbers?  
Similar as Leetcode 159. Longest Substring with At Most Two Distinct
Characters  
These solutions also works for the case of `K` elements.

 **Java:**  
Time `O(N)`, Space `O(N)`

    
    
        public int totalFruit(int[] tree) {
            Map<Integer, Integer> count = new HashMap<>();
            int i = 0, j;
            for (j = 0; j < tree.length; ++j) {
                count.put(tree[j], count.getOrDefault(tree[j], 0) + 1);
                if (count.size() > 2) {
                    count.put(tree[i], count.get(tree[i]) - 1);
                    count.remove(tree[i++], 0);
                }
            }
            return j - i;
        }
    

**Java**  
Time `O(N)`, Space `O(2)`

    
    
         public int totalFruit2(int[] tree) {
            Map<Integer, Integer> count = new HashMap<Integer, Integer>();
            int res = 0, i = 0;
            for (int j = 0; j < tree.length; ++j) {
                count.put(tree[j], count.getOrDefault(tree[j], 0) + 1);
                while (count.size() > 2) {
                    count.put(tree[i], count.get(tree[i]) - 1);
                    if (count.get(tree[i]) == 0) count.remove(tree[i]);
                    i++;
                }
                res = Math.max(res, j - i + 1);
            }
            return res;
        }
    

**C++:**  
Time `O(N)`, Space `O(N)`

    
    
        int totalFruit(vector<int> &tree) {
            unordered_map<int, int> count;
            int  i, j;
            for (i = 0, j = 0; j < tree.size(); ++j) {
                count[tree[j]]++;
                if (count.size() > 2) {
                    if (--count[tree[i]] == 0)count.erase(tree[i]);
                    i++;
                }
            }
            return j - i;
        }
    

**Python:**  
Time `O(N)`, Space `O(N)`

    
    
        def totalFruit( self, tree):
            count, i = {}, 0
            for j, v in enumerate(tree):
                count[v] = count.get(v, 0) + 1
                if len(count) > 2:
                    count[tree[i]] -= 1
                    if count[tree[i]] == 0: del count[tree[i]]
                    i += 1
            return j - i + 1
    



