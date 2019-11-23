---
layout: post
title: 784. Insert into a Binary Search Tree
---
### Question
Given the root node of a binary search tree (BST) and a value to be inserted
into the tree, insert the value into the BST. Return the root node of the BST
after the insertion. It is guaranteed that the new value does not exist in the
original BST.

Note that there may exist multiple valid ways for the insertion, as long as
the tree remains a BST after insertion. You can return any of them.

For example,

    
    
    Given the tree:
            4
           / \
          2   7
         / \
        1   3
    And the value to insert: 5
    

You can return this binary search tree:

    
    
             4
           /   \
          2     7
         / \   /
        1   3 5
    

This tree is also valid:

    
    
             5
           /   \
          2     7
         / \   
        1   3
             \
              4
    

### Solution 1
    
    
         public TreeNode insertIntoBST(TreeNode root, int val) {
            if(root == null) return new TreeNode(val);
            TreeNode cur = root;
            while(true) {
                if(cur.val <= val) {
                    if(cur.right != null) cur = cur.right;
                    else {
                        cur.right = new TreeNode(val);
                        break;
                    }
                } else {
                    if(cur.left != null) cur = cur.left;
                    else {
                        cur.left = new TreeNode(val);
                        break;
                    }
                }
            }
            return root;
        }


### Solution 2
public TreeNode insertIntoBST(TreeNode root, int val) {  
if (root == null) {  
return new TreeNode(val);  
}  
if (root.val > val) {  
root.left = insertIntoBST(root.left, val);  
} else {  
root.right = insertIntoBST(root.right, val);  
}  
return root;  
}


### Solution 3
    
    
    	TreeNode* insertIntoBST(TreeNode *node, int val) {
    		if (!node) {
    			TreeNode *newNode = new TreeNode(val);
    			return newNode;
    		}
    		if (val < node->val) {
    			node->left = insertIntoBST(node->left, val);
    		}
    		else {
    			node->right = insertIntoBST(node->right, val);
    		}
    		return node;
    	}
    



