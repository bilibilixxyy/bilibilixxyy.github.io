---
layout: post
title: 106. Construct Binary Tree from Inorder and Postorder Traversal
---
### Question
Given inorder and postorder traversal of a tree, construct the binary tree.

 **Note:**  
You may assume that duplicates do not exist in the tree.

For example, given

    
    
    inorder = [9,3,15,20,7]
    postorder = [9,15,7,20,3]

Return the following binary tree:

    
    
        3
       / \
      9  20
        /  \
       15   7
    

### Solution 1
The the basic idea is to take the last element in postorder array as the root,
find the position of the root in the inorder array; then locate the range for
left sub-tree and right sub-tree and do recursion. Use a HashMap to record the
index of root in the inorder array.

    
    
    public TreeNode buildTreePostIn(int[] inorder, int[] postorder) {
    	if (inorder == null || postorder == null || inorder.length != postorder.length)
    		return null;
    	HashMap<Integer, Integer> hm = new HashMap<Integer,Integer>();
    	for (int i=0;i<inorder.length;++i)
    		hm.put(inorder[i], i);
    	return buildTreePostIn(inorder, 0, inorder.length-1, postorder, 0, 
                              postorder.length-1,hm);
    }
    
    private TreeNode buildTreePostIn(int[] inorder, int is, int ie, int[] postorder, int ps, int pe, 
                                     HashMap<Integer,Integer> hm){
    	if (ps>pe || is>ie) return null;
    	TreeNode root = new TreeNode(postorder[pe]);
    	int ri = hm.get(postorder[pe]);
    	TreeNode leftchild = buildTreePostIn(inorder, is, ri-1, postorder, ps, ps+ri-is-1, hm);
    	TreeNode rightchild = buildTreePostIn(inorder,ri+1, ie, postorder, ps+ri-is, pe-1, hm);
    	root.left = leftchild;
    	root.right = rightchild;
    	return root;
    }


### Solution 2
    
    
    # Definition for a  binary tree node
    # class TreeNode:
    #     def __init__(self, x):
    #         self.val = x
    #         self.left = None
    #         self.right = None
    
    class Solution:
        # @param inorder, a list of integers
        # @param postorder, a list of integers
        # @return a tree node
        # 12:00
        def buildTree(self, inorder, postorder):
            if not inorder or not postorder:
                return None
            
            root = TreeNode(postorder.pop())
            inorderIndex = inorder.index(root.val)
    
            root.right = self.buildTree(inorder[inorderIndex+1:], postorder)
            root.left = self.buildTree(inorder[:inorderIndex], postorder)
    
            return root


### Solution 3
    
    
    TreeNode *buildTree(vector<int> &inorder, vector<int> &postorder) {
        return create(inorder, postorder, 0, inorder.size() - 1, 0, postorder.size() - 1);
    }
    
    TreeNode* create(vector<int> &inorder, vector<int> &postorder, int is, int ie, int ps, int pe){
        if(ps > pe){
            return nullptr;
        }
        TreeNode* node = new TreeNode(postorder[pe]);
        int pos;
        for(int i = is; i <= ie; i++){
            if(inorder[i] == node->val){
                pos = i;
                break;
            }
        }
        node->left = create(inorder, postorder, is, pos - 1, ps, ps + pos - is - 1);
        node->right = create(inorder, postorder, pos + 1, ie, pe - ie + pos, pe - 1);
        return node;
    }
    

Actually, this problem is pretty similar as the previous one.

[Here is a like to that solution.
](https://oj.leetcode.com/discuss/18101/sharing-my-straightforward-recursive-
solution)



