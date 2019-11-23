---
layout: post
title: 105. Construct Binary Tree from Preorder and Inorder Traversal
---
### Question
Given preorder and inorder traversal of a tree, construct the binary tree.

 **Note:**  
You may assume that duplicates do not exist in the tree.

For example, given

    
    
    preorder = [3,9,20,15,7]
    inorder = [9,3,15,20,7]

Return the following binary tree:

    
    
        3
       / \
      9  20
        /  \
       15   7

### Solution 1
Hi guys, this is my Java solution. I read this
[post](http://leetcode.com/2011/04/construct-binary-tree-from-inorder-and-
preorder-postorder-traversal.html), which is very helpful.

The basic idea is here:  
Say we have 2 arrays, PRE and IN.  
Preorder traversing implies that PRE[0] is the root node.  
Then we can find this PRE[0] in IN, say it's IN[5].  
Now we know that IN[5] is root, so we know that IN[0] - IN[4] is on the left
side, IN[6] to the end is on the right side.  
Recursively doing this on subarrays, we can build a tree out of it :)

Hope this helps.

    
    
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        return helper(0, 0, inorder.length - 1, preorder, inorder);
    }
    
    public TreeNode helper(int preStart, int inStart, int inEnd, int[] preorder, int[] inorder) {
        if (preStart > preorder.length - 1 || inStart > inEnd) {
            return null;
        }
        TreeNode root = new TreeNode(preorder[preStart]);
        int inIndex = 0; // Index of current root in inorder
        for (int i = inStart; i <= inEnd; i++) {
            if (inorder[i] == root.val) {
                inIndex = i;
            }
        }
        root.left = helper(preStart + 1, inStart, inIndex - 1, preorder, inorder);
        root.right = helper(preStart + inIndex - inStart + 1, inIndex + 1, inEnd, preorder, inorder);
        return root;
    }
    


### Solution 2
    
    
    def buildTree(self, preorder, inorder):
        if inorder:
            ind = inorder.index(preorder.pop(0))
            root = TreeNode(inorder[ind])
            root.left = self.buildTree(preorder, inorder[0:ind])
            root.right = self.buildTree(preorder, inorder[ind+1:])
            return root


### Solution 3
I din't find iterative solutions discussed in the old Discuss. So, I thought,
I will add my solution in here.

The idea is as follows:

  1. Keep pushing the nodes from the preorder into a stack (and keep making the tree by adding nodes to the left of the previous node) until the top of the stack matches the inorder.

  2. At this point, pop the top of the stack until the top does not equal inorder (keep a flag to note that you have made a pop).

  3. Repeat 1 and 2 until preorder is empty. The key point is that whenever the flag is set, insert a node to the right and reset the flag.

class Solution {  
public:  
TreeNode *buildTree(vector &preorder, vector &inorder) {

    
             if(preorder.size()==0)
             return NULL;
         
         stack<int> s;
         stack<TreeNode *> st;
         TreeNode *t,*r,*root;
         int i,j,f;
         
         f=i=j=0;
         s.push(preorder[i]);
         
         root = new TreeNode(preorder[i]);
         st.push(root);
         t = root;
         i++;
         
         while(i<preorder.size())
         {
             if(!st.empty() && st.top()->val==inorder[j])
             {
                 t = st.top();
                 st.pop();
                 s.pop();
                 f = 1;
                 j++;
             }
             else
             {
                 if(f==0)
                 {
                     s.push(preorder[i]);
                     t -> left = new TreeNode(preorder[i]);
                     t = t -> left;
                     st.push(t);
                     i++;
                 }
                 else 
                 {
                     f = 0;
                     s.push(preorder[i]);
                     t -> right = new TreeNode(preorder[i]);
                     t = t -> right;
                     st.push(t);
                     i++;
                 }
             }
         }
         
         return root;
     }
    

};



