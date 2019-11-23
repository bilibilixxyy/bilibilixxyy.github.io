---
layout: post
title: 543. Diameter of Binary Tree
---
### Question
Given a binary tree, you need to compute the length of the diameter of the
tree. The diameter of a binary tree is the length of the **longest** path
between any two nodes in a tree. This path may or may not pass through the
root.

 **Example:**  
Given a binary tree  

    
    
              1
             / \
            2   3
           / \     
          4   5    
    

Return **3** , which is the length of the path [4,2,1,3] or [5,2,1,3].

 **Note:** The length of path between two nodes is represented by the number
of edges between them.

### Solution 1
For `every` node, length of longest path which `pass it` = MaxDepth of its
left subtree + MaxDepth of its right subtree.

    
    
    public class Solution {
        int max = 0;
        
        public int diameterOfBinaryTree(TreeNode root) {
            maxDepth(root);
            return max;
        }
        
        private int maxDepth(TreeNode root) {
            if (root == null) return 0;
            
            int left = maxDepth(root.left);
            int right = maxDepth(root.right);
            
            max = Math.max(max, left + right);
            
            return Math.max(left, right) + 1;
        }
    }
    


### Solution 2
Just go through the tree.

    
    
    class Solution(object):
        def diameterOfBinaryTree(self, root):
            """
            :type root: TreeNode
            :rtype: int
            """
            self.ans = 0
            
            def depth(p):
                if not p: return 0
                left, right = depth(p.left), depth(p.right)
                self.ans = max(self.ans, left+right)
                return 1 + max(left, right)
                
            depth(root)
            return self.ans
    


### Solution 3
We can solve this problem with two different cases:

  1. If the longest path will include the root node, then the longest path must be the depth(root->right) + depth (root->left)
  2. If the longest path does not include the root node, this problem is divided into 2 sub-problem: set left child and right child as the new root separately, and repeat step1.

We could get the solution by returning the max path of 1 and 2.

UPDATED:

    
    
    class Solution {
    public:
    	int diameterOfBinaryTree(TreeNode* root) {
    		if(root == nullptr) return 0;
    		int res = 0;
    		dfs(root, res);
    		return res - 1;
    	}
    
    	vector<int> dfs(TreeNode* root, int& res){
    		if(root == nullptr) return {0, 0};
    		auto l = dfs(root->left, res);
    		auto r = dfs(root->right, res);
    		int path = max(l[0], l[1]) + 1 + max(r[0], r[1]);
    		res = max(res, path);
    		return {max(l[0], l[1]) + 1, 1 + max(r[0], r[1])};
    	}
    };



