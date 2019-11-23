---
layout: post
title: 1005. Univalued Binary Tree
---
### Question
A binary tree is _univalued_ if every node in the tree has the same value.

Return `true` if and only if the given tree is univalued.



 **Example 1:**

![](https://assets.leetcode.com/uploads/2018/12/28/unival_bst_1.png)

    
    
     **Input:** [1,1,1,1,1,null,1]
    **Output:** true
    

**Example 2:**

![](https://assets.leetcode.com/uploads/2018/12/28/unival_bst_2.png)

    
    
    **Input:** [2,2,2,5,2]
    **Output:** false
    



 **Note:**

  1. The number of nodes in the given tree will be in the range `[1, 100]`.
  2. Each node's value will be an integer in the range `[0, 99]`.

### Solution 1
If a node has children,  
every child should be unival tree and has same value.

 **Java:**

    
    
         public boolean isUnivalTree(TreeNode root) {
            if (root.left != null) {
                if (!isUnivalTree(root.left))
                    return false;
                if (root.left.val != root.val)
                    return false;
            }
    
            if (root.right != null) {
                if (!isUnivalTree(root.right))
                    return false;
                if (root.right.val != root.val)
                    return false;
            }
            return true;
        }
    

**Java:**

    
    
        public boolean is UnivalTree(TreeNode root) {
            return (root.left == null || root.left.val == root.val && isUnivalTree(root.left)) &&
                   (root.right == null || root.right.val == root.val && isUnivalTree(root.right));
        }
    

**C++:**

    
    
        bool isUnivalTree(TreeNode* root) {
             return (!root->left || root->left->val == root->val && isUnivalTree(root->left)) &&
                   (!root->right || root->right->val == root->val && isUnivalTree(root->right));
        }
    

In python solution,  
I use a dfs recusion to checked  
if all nodes in the tree has value equal to `root.val`.

**Python:**

    
    
        def isUnivalTree(self, root):
            def dfs( node):
                return not node or node.val == root.val and dfs(node.left) and dfs(node.right)
            return dfs(root)
    


### Solution 2
Breadth/Depth first search to check if any node's value is different from the
root's.

 **Method 1:**

Iterative version - BFS.

 **Analysis: Time & space: O(n).**

    
    
        public boolean isUnivalTree(TreeNode root) {
            Queue<TreeNode> q = new LinkedList<>();
            q.offer(root);
            while (!q.isEmpty()) {
                TreeNode n = q.poll();
                if (n.val != root.val) { return false; }
                if (n.left != null) { q.offer(n.left); }        
                if (n.right != null) { q.offer(n.right); }        
            }
            return true;
        }
    

**Method 2:**

Recursive version - DFS.

**Analysis: Time & space: O(n).**

    
    
        public boolean isUnivalTree(TreeNode root) {
            return dfs(root, root.val);
        }
        private boolean dfs(TreeNode n, int v) {
            if (n == null) { return true; }
            if (n.val != v) { return false; }
            return dfs(n.left, v) && dfs(n.right, v);
        }    
    

**Q & A**  
Q:  
What if the root is null?

A:  
We can add the following statement as the first line in isUnivalTree(TreeNode)
for both method 1 & 2:

`if (root == null) return true;`

In fact, the problem description states **"The number of nodes in the given
tree will be in the range [1, 100]."** , which implies `root != null`.


### Solution 3
# Python3

    
    
    class Solution:
        def isUnivalTree(self, root):
            if not root:
                return True
            
            if root.right:
                if root.val != root.right.val: #Equavalent
                    return False
                
            if root.left:
                if root.val != root.left.val: #Equavalent
                    return False
            
            return self.isUnivalTree(root.right) and self.isUnivalTree(root.left)
            
            
    



