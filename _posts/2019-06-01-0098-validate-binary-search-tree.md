---
layout: post
title: 98. Validate Binary Search Tree
---
### Question
Given a binary tree, determine if it is a valid binary search tree (BST).

Assume a BST is defined as follows:

  * The left subtree of a node contains only nodes with keys **less than** the node's key.
  * The right subtree of a node contains only nodes with keys **greater than** the node's key.
  * Both the left and right subtrees must also be binary search trees.



 **Example 1:**

    
    
        2
       / \
      1   3
    
    **Input:**  [2,1,3]
    **Output:** true
    

**Example 2:**

    
    
        5
       / \
      1   4
         / \
        3   6
    
    **Input:** [5,1,4,null,null,3,6]
    **Output:** false
    **Explanation:** The root node's value is 5 but its right child's value is 4.
    

### Solution 1
I will show you all how to tackle various tree questions using iterative
inorder traversal. First one is the standard iterative inorder traversal using
stack. Hope everyone agrees with this solution.

Question : [Binary Tree Inorder
Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)

    
    
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        if(root == null) return list;
        Stack<TreeNode> stack = new Stack<>();
        while(root != null || !stack.empty()){
            while(root != null){
                stack.push(root);
                root = root.left;
            }
            root = stack.pop();
            list.add(root.val);
            root = root.right;
            
        }
        return list;
    }
    

Now, we can use this structure to find the Kth smallest element in BST.

Question : [Kth Smallest Element in a BST](https://leetcode.com/problems/kth-
smallest-element-in-a-bst/)

    
    
     public int kthSmallest(TreeNode root, int k) {
         Stack<TreeNode> stack = new Stack<>();
         while(root != null || !stack.isEmpty()) {
             while(root != null) {
                 stack.push(root);    
                 root = root.left;   
             } 
             root = stack.pop();
             if(--k == 0) break;
             root = root.right;
         }
         return root.val;
     }
    

We can also use this structure to solve BST validation question.

Question : [Validate Binary Search
Tree](https://leetcode.com/problems/validate-binary-search-tree/)

    
    
    public boolean isValidBST(TreeNode root) {
       if (root == null) return true;
       Stack<TreeNode> stack = new Stack<>();
       TreeNode pre = null;
       while (root != null || !stack.isEmpty()) {
          while (root != null) {
             stack.push(root);
             root = root.left;
          }
          root = stack.pop();
          if(pre != null && root.val <= pre.val) return false;
          pre = root;
          root = root.right;
       }
       return true;
    }
    


### Solution 2
    
    
    public class Solution {
        public boolean isValidBST(TreeNode root) {
            return isValidBST(root, Long.MIN_VALUE, Long.MAX_VALUE);
        }
        
        public boolean isValidBST(TreeNode root, long minVal, long maxVal) {
            if (root == null) return true;
            if (root.val >= maxVal || root.val <= minVal) return false;
            return isValidBST(root.left, minVal, root.val) && isValidBST(root.right, root.val, maxVal);
        }
    }
    

Basically what I am doing is recursively iterating over the tree while
defining interval `<minVal, maxVal>` for each node which value must fit in.


### Solution 3
    
    
    class Solution {
    public:
        bool isValidBST(TreeNode* root) {
            TreeNode* prev = NULL;
            return validate(root, prev);
        }
        bool validate(TreeNode* node, TreeNode* &prev) {
            if (node == NULL) return true;
            if (!validate(node->left, prev)) return false;
            if (prev != NULL && prev->val >= node->val) return false;
            prev = node;
            return validate(node->right, prev);
        }
    };
    

> Update:

>

> If we use in-order traversal to serialize a binary search tree, we can  
> get a list of values in ascending order. It can be proved with the  
> definition of BST. And here I use the reference of TreeNode  
> pointer `prev` as a global variable to mark the address of previous node in
the  
> list.

>

> “In-order Traversal”:  
> [https://en.wikipedia.org/wiki/Tree_traversal#In-order]()

If you know what `INT_MAX` or `INT_MIN` is, then it is no excuse for your
carelessness.



