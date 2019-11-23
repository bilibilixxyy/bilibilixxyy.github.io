---
layout: post
title: 1079. Sum of Root To Leaf Binary Numbers
---
### Question
Given a binary tree, each node has value `0` or `1`.  Each root-to-leaf path
represents a binary number starting with the most significant bit.  For
example, if the path is `0 -> 1 -> 1 -> 0 -> 1`, then this could represent
`01101` in binary, which is `13`.

For all leaves in the tree, consider the numbers represented by the path from
the root to that leaf.

Return the sum of these numbers.



 **Example 1:**

![](https://assets.leetcode.com/uploads/2019/04/04/sum-of-root-to-leaf-binary-
numbers.png)

    
    
     **Input:** [1,0,1,0,1,0,1]
    **Output:** 22
    **Explanation:** (100) + (101) + (110) + (111) = 4 + 5 + 6 + 7 = 22
    



 **Note:**

  1. The number of nodes in the tree is between `1` and `1000`.
  2. node.val is `0` or `1`.
  3. The answer will not exceed `2^31 - 1`.

### Solution 1
##  **Intuition**

Easily decompose this problem into 2 sub-problem:

  1. Find all path from root to leaves. DFS recursion should help do that.
  2. Transform a path from base to base 10.

You can do this separately, and it will be a `O(N^2)` solution.  
In my solution, I combine them together.

##  **Explanation** :

We recursively pass the current value of path to the children.  
If `root == null`, no value, return 0.  
If `root != null`,  
we double the value from its parent and add the node's value,  
like the process of transforming base 2 to base 10.

In the end of recursion,  
if `root.left == root.right == null`,  
return the current `val`.  
  

##  **Time Complexity** :

`O(N)` time, `O(logN)` for recursion.

  

 **Java:**

    
    
         public int sumRootToLeaf(TreeNode root) {
            return dfs(root, 0);
        }
    
        public int dfs(TreeNode root, int val) {
            if (root == null) return 0;
            val = val * 2 + root.val;
            return root.left == root.right ? val : dfs(root.left, val) + dfs(root.right, val);
        }
    

**C++:**

    
    
         int sumRootToLeaf(TreeNode* root, int val = 0) {
            if (!root) return 0;
            val = (val * 2 + root->val);
            return root->left == root->right ? val : sumRootToLeaf(root->left, val) + sumRootToLeaf(root->right, val));
        }
    

**Python:**

    
    
        def sum RootToLeaf(self, root, val=0):
            if not root: return 0
            val = val * 2 + root.val
            if root.left == root.right: return val
            return self.sumRootToLeaf(root.left, val) + self.sumRootToLeaf(root.right, val)
    

## **Update**

In the previous version of problem,  
the length of path may exceed 32 and it will be a huge integer.  
So it request to return the number mod by 10^9+7.  
Now it notes that The "answer will not exceed 2^31 - 1."  
Now it removes this condition to keep this problem as a easy one.


### Solution 2
I was trapped too :) YEAH !!!  
Is there a way to prevent this?  
I'm pretty sure this MODULO thing is not a good algorithmic problem.


### Solution 3
    
    
    class Solution {
        public int sumRootToLeaf(TreeNode root) {
            dfs(root, 0);
            return ans;
        }
        
        int ans = 0;
        void dfs(TreeNode root, int val){
            if(root == null) return;
            val = val << 1 | root.val;
            if(root.left == null && root.right == null) ans += val;
            dfs(root.left, val);
            dfs(root.right, val);
        }
    }
    



