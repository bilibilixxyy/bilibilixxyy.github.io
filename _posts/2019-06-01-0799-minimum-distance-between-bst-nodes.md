---
layout: post
title: 799. Minimum Distance Between BST Nodes
---
### Question
Given a Binary Search Tree (BST) with the root node `root`, return the minimum
difference between the values of any two different nodes in the tree.

 **Example :**

    
    
     **Input:** root = [4,2,6,1,3,null,null]
    **Output:** 1
    **Explanation:**
    Note that root is a TreeNode object, not an array.
    
    The given tree [4,2,6,1,3,null,null] is represented by the following diagram:
    
              4
            /   \
          2      6
         / \    
        1   3  
    
    while the minimum difference in this tree is 1, it occurs between node 1 and node 2, also between node 3 and node 2.
    

**Note:**

  1. The size of the BST will be between 2 and `100`.
  2. The BST is always valid, each node's value is an integer, and each node's value is different.

### Solution 1
I submitted the same codes and received two Accepted!


### Solution 2
Classical inorder traverse. Time complexity O(N). Space complexity O(h)

This question is the same as problem 530.Minimum Absolute Difference in BST.
Except that in 530th, we are given a binary search tree with **non-negative
values**.  
However, it seems that it doesn't have any negative case and my solution in
cpp get accepted.

C++

    
    
    class Solution {
      public:
        int res = INT_MAX, pre = -1;
        int minDiffInBST(TreeNode* root) {
            if (root->left != NULL) minDiffInBST(root->left);
            if (pre >= 0) res = min(res, root->val - pre);
            pre = root->val;
            if (root->right != NULL) minDiffInBST(root->right);
            return res;
        }
    };
    

Java

    
    
    class Solution {
        Integer res = Integer.MAX_VALUE, pre = null;
        public int minDiffInBST(TreeNode root) {
            if (root.left != null) minDiffInBST(root.left);
            if (pre != null) res = Math.min(res, root.val - pre);
            pre = root.val;
            if (root.right != null) minDiffInBST(root.right);
            return res;
        }
    }
    

Python

    
    
    class Solution(object):
        pre = -float('inf')
        res = float('inf')
    
        def minDiffInBST(self, root):
            if root.left:
                self.minDiffInBST(root.left)
            self.res = min(self.res, root.val - self.pre)
            self.pre = root.val
            if root.right:
                self.minDiffInBST(root.right)
            return self.res


### Solution 3
I think the problem title ' **Minimum Distance between BST nodes** ' is quite
confusing here. It should be ' **Minimun difference between BST nodes** '.



