---
layout: post
title: 1194. Path In Zigzag Labelled Binary Tree
---
### Question
In an infinite binary tree where every node has two children, the nodes are
labelled in row order.

In the odd numbered rows (ie., the first, third, fifth,...), the labelling is
left to right, while in the even numbered rows (second, fourth, sixth,...),
the labelling is right to left.

![](https://assets.leetcode.com/uploads/2019/06/24/tree.png)

Given the `label` of a node in this tree, return the labels in the path from
the root of the tree to the node with that `label`.



 **Example 1:**

    
    
     **Input:** label = 14
    **Output:** [1,3,4,14]
    

**Example 2:**

    
    
    **Input:** label = 26
    **Output:** [1,2,6,10,26]
    



 **Constraints:**

  * `1 <= label <= 10^6`

### Solution 1
# Intuition

If the tree is numbered left-to-right (not zigzag), the parent's label can be
always determined as `label / 2`. For zigzag, we need to "invert" the parent
label.

# Solution

Determine the tree `level` where our value is located. The maximum label in
the level is `1 << level - 1`, and minimum is `1 << (level - 1)`. We will use
this fact to "invert" the parent label.

    
    
    vector<int> pathInZigZagTree(int label, int level = 0) {
      while (1 << level <= label) ++level;
      vector<int> res(level);
      for(; label >= 1; label /= 2, --level) {
        res[level - 1] = label;
        label = (1 << level) - 1 - label + (1 << (level - 1));
      }
      return res;
    }
    

# Complexity Analysis

Runtime: _O(log n)_  
Memory: _O(1)_ or _O(log n)_ if we consider the memory required for the
result.


### Solution 2
If this question is `Easy` than why 2/3 of participants (including me) cannot
solve it?


### Solution 3
In the same line, the biggest = smallest * 2 - 1, which leads to the factor 3.  
And also, each parent is the half of their children, which leads to the pow of
2.

 **Python:**

    
    
        def pathInZigZagTree(self, x):
             return self.pathInZigZagTree(3 * 2 ** (len(bin(x)) - 4) - 1 - x / 2) + [x] if x > 1 else [1]
    



