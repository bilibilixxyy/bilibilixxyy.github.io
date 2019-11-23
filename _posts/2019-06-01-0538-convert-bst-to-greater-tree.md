---
layout: post
title: 538. Convert BST to Greater Tree
---
### Question
Given a Binary Search Tree (BST), convert it to a Greater Tree such that every
key of the original BST is changed to the original key plus sum of all keys
greater than the original key in BST.

 **Example:**

    
    
     **Input:** The root of a Binary Search Tree like this:
                  5
                /   \
               2     13
    
    **Output:** The root of a Greater Tree like this:
                 18
                /   \
              20     13
    

### Solution 1
Since this is a BST, we can do a reverse inorder traversal to traverse the
nodes of the tree in descending order. In the process, we keep track of the
running sum of all nodes which we have traversed thus far.

    
    
    public class Solution {
    
        int sum = 0;
        
        public TreeNode convertBST(TreeNode root) {
            convert(root);
            return root;
        }
        
        public void convert(TreeNode cur) {
            if (cur == null) return;
            convert(cur.right);
            cur.val += sum;
            sum = cur.val;
            convert(cur.left);
        }
        
    }
    


### Solution 2
The solution is the modification of inorder travel. Namely, travel right
subtree, change the root value, and travel left subtree.

    
    
    class Solution {
    private:
        int cur_sum = 0;
    public:
        void travel(TreeNode* root){
            if (!root) return;
            if (root->right) travel(root->right);
            
            root->val = (cur_sum += root->val);
            if (root->left) travel(root->left);
        }
        TreeNode* convertBST(TreeNode* root) {
            travel(root);
            return root;
        }
    };
    


### Solution 3
Idea: `Reversely` traverse the tree and keep a sum of all previously visited
values. Because its a BST, values seen before are all greater than current
`node.val`. That's what we want according to the problem.

    
    
    public class Solution {
        int sum = 0;
        
        public TreeNode convertBST(TreeNode root) {
            if (root == null) return null;
            
            convertBST(root.right);
            
            root.val += sum;
            sum = root.val;
            
            convertBST(root.left);
            
            return root;
        }
    }
    



