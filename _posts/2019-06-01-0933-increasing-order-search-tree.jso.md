---
layout: post
title: 933. Increasing Order Search Tree
---
### Question
Given a binary search tree, rearrange the tree in **in-order** so that the
leftmost node in the tree is now the root of the tree, and every node has no
left child and only 1 right child.

    
    
     **Example 1:**
    **Input:** [5,3,6,2,4,null,8,1,null,null,null,7,9]
    
           5
          / \
        3    6
       / \    \
      2   4    8
      /        / \ 
    1        7   9
    
    **Output:** [1,null,2,null,3,null,4,null,5,null,6,null,7,null,8,null,9]
    
     1
       \
       2
        \
         3
          \
           4
            \
             5
              \
               6
                \
                 7
                  \
                   8
                    \
                     9  

**Note:**

  1. The number of nodes in the given tree will be between 1 and 100.
  2. Each node will have a unique integer value from 0 to 1000.

### Solution 1
I didn't use this condition of BST, and just inorder output the whole tree.

Straigh forward idea:  
`res = inorder(root.left) + root + inorder(root.right)`

Several tips here:

  1. I pass a tail part to the function, so it can link it to the last node.  
This operation takes `O(1)`, instead of `O(N)`.  
Otherwise the whole time complexity will be `O(N^2)`.

  2. Also, remember to set `root.left = null`.  
Otherwise it will be TLE for Leetcode to traverse your tree.

  3. Should arrange the old tree, not create a new tree.  
The judgement won't take it as wrong answer, but it is.

 **C++:**

    
    
        TreeNode* increasing BST(TreeNode* root, TreeNode* tail = NULL) {
            if (!root) return tail;
            TreeNode* res = increasingBST(root->left, root);
            root->left = NULL;
            root->right = increasingBST(root->right, tail);
            return res;
        }
    

**Java:**

    
    
        public TreeNode increasing BST(TreeNode root) {
            return increasingBST(root, null);
        }
    
        public TreeNode increasingBST(TreeNode root, TreeNode tail) {
            if (root == null) return tail;
            TreeNode res = increasingBST(root.left, root);
            root.left = null;
            root.right = increasingBST(root.right, tail);
            return res;
        }
    

**Python:**

    
    
        def increasing BST(self, root, tail = None):
            if not root: return tail
            res = self.increasingBST(root.left, root)
            root.left = None
            root.right = self.increasingBST(root.right, tail)
            return res
    


### Solution 2
    
    
       TreeNode prev=null, head=null;
        public TreeNode increasingBST(TreeNode root) {
            if(root==null) return null;   
            increasingBST(root.left);  
            if(prev!=null) { 
            	root.left=null; // we no  longer needs the left  side of the node, so set it to null
            	prev.right=root; 
            }
            if(head==null) head=root; // record the most left node as it will be our root
            prev=root; //keep track of the prev node
            increasingBST(root.right); 
            return head;
        }
    


### Solution 3
Hi all, this might be a stupid question, but I am just so stuck.  
So I got an TLE error when submitting my code. The test case input is  
[379  
826]  
(in this format). So what does this mean? I used this input and got error as
follows:

Your answer  
JSON string [[379] is not a full JSON packet, more bytes expected.  
Expected answer  
Line 59: ValueError: Expecting object: line 1 column 4 (char 3)

This is so confusing, what does this test case mean?



