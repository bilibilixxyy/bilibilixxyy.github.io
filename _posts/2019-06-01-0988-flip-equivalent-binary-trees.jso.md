---
layout: post
title: 988. Flip Equivalent Binary Trees
---
### Question
For a binary tree T, we can define a flip operation as follows: choose any
node, and swap the left and right child subtrees.

A binary tree X is _flip equivalent_ to a binary tree Y if and only if we can
make X equal to Y after some number of flip operations.

Write a function that determines whether two binary trees are _flip
equivalent_.   The trees are given by root nodes `root1` and `root2`.



 **Example 1:**

    
    
     **Input:** root1 = [1,2,3,4,5,6,null,null,null,7,8], root2 = [1,3,2,null,6,4,5,null,null,null,null,8,7]
    **Output:** true
    **Explanation:** We flipped at nodes with values 1, 3, and 5.
    ![Flipped Trees Diagram](https://assets.leetcode.com/uploads/2018/11/29/tree_ex.png)
    



 **Note:**

  1. Each tree will have at most `100` nodes.
  2. Each value in each tree will be a unique integer in the range `[0, 99]`.

### Solution 1
  1. If at least one of the two root nodes is `null`, are they equal? if yes, `true`; if no, `false`;
  2. otherwise, neither node is `null`; if the values of the two nodes are:  
2a) NOT equal, return `false`;  
2b) equal, compare their children recursively.

    
    
        public boolean flipEquiv(TreeNode r1, TreeNode r2) {
            if (r1 == null || r2 == null) return r1 == r2;
            if (r1.val != r2.val) return false;
            return flipEquiv(r1.left, r2.left) && flipEquiv(r1.right, r2.right) || flipEquiv(r1.left, r2.right) && flipEquiv(r1.right, r2.left);         
        }
    

**Update:**  
For some time, I forgot the following constraint and changed the comlexity
from O(n) to O(n ^ 2):  
`Each value in each tree will be a unique integer in the range [0, 99]`

# The follows are correct **only without the above condition**.

Complexity analysis corrected from `O(n)` to `O(n ^ 2)`, credit to
@coder_coder.  
 **Analysis:**

In worst case, the recursion corresponds to a perfect quaternary (means
`4`-nary) tree, which has `4 ^ d = N ^ 2` nodes, and we have to traverse all
nodes. `d = logN` is the depth of the binary tree.

One worst case for input:  
two perfect binary trees: root1 & root2.

  1. Root1's nodes are all `0`s;
  2. Root2's nodes are all `0`s, with the exception that left and right bottoms are both `1`s.

 **Time & Space: O(n ^ 2).**


### Solution 2
  * Logic is simple
  * DFS both roots simultaneously
  * If one of them is None, both should be None.
  * If root1's left node value is not equal to root2's left node value, flip root1's children
  * If root1's left node is None and root2's left node is not, flip root1's children
  * If root2's left node is None and root1's left node is not, flip root1's children

    
    
    class Solution:
        def flipEquiv(self, root1, root2):
            if not root1 or not root2: 
                return root1 == root2
            if root1.left and root2.left and root1.left.val != root2.left.val or (not root1.left and root2.left) or (root1.left and not root2.left):
                root1.left, root1.right = root1.right, root1.left
            return root1.val == root2.val and self.flipEquiv(root1.left, root2.left) and self.flipEquiv(root1.right, root2.right)
    


### Solution 3
    
    
    public boolean flipEquivIterative(TreeNode root1, TreeNode root2) {
    
            Queue<TreeNode> queue = new LinkedList<>();
    
            queue.offer(root1);
            queue.offer(root2);
            while (!queue.isEmpty()) {
                TreeNode curr1 = queue.poll();
                TreeNode curr2 = queue.poll();
    
                if (curr1 == null && curr2 == null) {
                    continue;
                }
                if (!equals(curr1, curr2)) {
                    return false;
                }
                if (isEquals(curr1.left, curr2.left) && isEquals(curr1.right, curr2.right)) {
                    queue.offer(curr1.left);
                    queue.offer(curr2.left);
                    queue.offer(curr1.right);
                    queue.offer(curr2.right);
                } else if (isEquals(curr1.left, curr2.right) && isEquals(curr1.right, curr2.left)) {
                    queue.offer(curr1.left);
                    queue.offer(curr2.right);
                    queue.offer(curr1.right);
                    queue.offer(curr2.left);
                } else {
                    return false;
                }
            }
            return true;
        }
    
        private boolean isEquals(TreeNode root1, TreeNode root2) {
            if (root1 == null && root2 == null) {
                return true;
            } else if (root1 != null && root2 != null && root1.val == root2.val) {
                return true;
            } else {
                return false;
            }
        }
    



