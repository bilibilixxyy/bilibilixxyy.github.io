---
layout: post
title: 783. Search in a Binary Search Tree
---
### Question
Given the root node of a binary search tree (BST) and a value. You need to
find the node in the BST that the node's value equals the given value. Return
the subtree rooted with that node. If such node doesn't exist, you should
return NULL.

For example,

    
    
    Given the tree:
            4
           / \
          2   7
         / \
        1   3
    
    And the value to search: 2
    

You should return this subtree:

    
    
          2     
         / \   
        1   3
    

In the example above, if we want to search the value `5`, since there is no
node with value `5`, we should return `NULL`.

Note that an empty tree is represented by `NULL`, therefore you would see the
expected output (serialized tree format) as `[]`, not `null`.

### Solution 1
recursion:

    
    
    public TreeNode searchBST(TreeNode root, int val) {
            if(root == null) return root;
            if(root.val == val){
                return root;
            }
            else{
                return val<root.val? searchBST(root.left,val):searchBST(root.right,val);
            }
        }
    

iteration:

    
    
    public TreeNode searchBST(TreeNode root, int val) {
            while(root != null && root.val != val){
                root = val<root.val? root.left:root.right;
            }
            return root;
        }
    


### Solution 2
One way or another, DFS will continue till found target value val or will stop
and return None from a leaf node

    
    
    class Solution:
        def searchBST(self, root, val):
            if root and val < root.val: return self.searchBST(root.left, val)
            elif root and val > root.val: return self.searchBST(root.right, val)
            return root
    

1-liner for fun

    
    
    class Solution:
        def searchBST(self, root, val):
            return self.searchBST(root.left,val) if root and val<root.val else self.searchBST(root.right,val) if root and val>root.val else root
    


### Solution 3
    
    
    TreeNode* searchBST(TreeNode* root, int val) {
        while (root != nullptr && root->val != val) {
          root = (root->val > val) ? root->left : root->right;
        }
        return root;
    }
    



