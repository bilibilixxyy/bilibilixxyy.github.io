---
layout: post
title: 975. Range Sum of BST
---
### Question
Given the `root` node of a binary search tree, return the sum of values of all
nodes with value between `L` and `R` (inclusive).

The binary search tree is guaranteed to have unique values.



 **Example 1:**

    
    
     **Input:** root = [10,5,15,3,7,null,18], L = 7, R = 15
    **Output:** 32
    

**Example 2:**

    
    
    **Input:** root = [10,5,15,3,7,13,18,1,null,6], L = 6, R = 10
    **Output:** 23
    



 **Note:**

  1. The number of nodes in the tree is at most `10000`.
  2. The final answer is guaranteed to be less than `2^31`.

### Solution 1
Three methods and choose one you like.

 **Method 1:**

    
    
        public  int rangeSumBST(TreeNode root, int L, int R) {
            if (root == null) return 0; // base case.
            if (root.val < L) return rangeSumBST(root.right, L, R); // left branch excluded.
            if (root.val > R) return rangeSumBST(root.left, L, R); // right branch excluded.
            return root.val + rangeSumBST(root.right, L, R) + rangeSumBST(root.left, L, R); // count in both children.
        }
    

The following are two more similar recursive codes.

**Method 2:**

    
    
        public  int rangeSumBST(TreeNode root, int L, int R) {
            if (root == null) return 0; // base case.
            return (L <= root.val && root.val <= R ? root.val : 0) + rangeSumBST(root.right, L, R) + rangeSumBST(root.left, L, R);
        }
    

**Method 3:**

    
    
         public int rangeSumBST(TreeNode root, int L, int R) {
            if (root == null) { return 0; }
            int sum = 0;
            if (root.val > L) { sum += rangeSumBST(root.left, L, R); } // left child is a possible candidate.
            if (root.val < R) { sum += rangeSumBST(root.right, L, R); } // right child is a possible candidate.
            if (root.val >= L && root.val <= R) { sum += root.val; } // count root in.
            return sum;
        }
    

**Analysis:**

All 3 methods will DFS traverse all nodes in worst case, and if we count in
the recursion trace space cost, the complexities are as follows:

 **Time & space: O(n)**, where n is the number of total nodes.


### Solution 2
    
    
    # Definition for a binary tree node.
    # class TreeNode:
    #     def __init__(self, x):
    #         self.val = x
    #         self.left = None
    #         self.right = None
    
    class Solution:
    	def rangeSumBST(self, root: TreeNode, L: int, R: int) -> int:
    
    		def sum_tree(root, L, R, total):
    			if root.left:       # in case there is a left noode
    				sum_tree(root.left, L, R, total)        # call sum_tree on the left node
    
    			if root.val >= L and root.val <= R:     # if value between L and R inclusive 
    				total.append(root.val)      # add it to the list
    			if root.right:
    				sum_tree(root.right, L, R, total)       # if there's right node, call function on it
    
    			return total        # when there isn't right and left node, just return a list 
    
    		total = []
    		return sum(sum_tree(root, L, R, total))     # return the sum of the list
    

I hope I helped you in some way. If you like this solution, I'll be very happy
if you click "Star" on Github:  
<https://github.com/JBielan/leetcode-python>

As well feel free to follow my account for daily coding solutions and machine-
learning/data-science projects:  
<https://github.com/JBielan>

May the code be with you!


### Solution 3
    
    
    # Definition for a binary tree node.
    # class TreeNode:
    #     def __init__(self, x):
    #         self.val = x
    #         self.left = None
    #         self.right = None
    
    class Solution:
        def rangeSumBST(self, root, L, R):
            """
            :type root: TreeNode
            :type L: int
            :type R: int
            :rtype: int
            """
            return self.inorder(root, 0, L, R)
                
        def inorder(self, root, value, L, R):
            if root:
                value = self.inorder(root.left, value, L, R)
                if root.val >= L and root.val <= R:
                    value += root.val
                value = self.inorder(root.right, value, L, R)
            
            return value
    



