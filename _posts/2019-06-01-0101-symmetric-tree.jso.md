---
layout: post
title: 101. Symmetric Tree
---
### Question
Given a binary tree, check whether it is a mirror of itself (ie, symmetric
around its center).

For example, this binary tree `[1,2,2,3,4,4,3]` is symmetric:

    
    
        1
       / \
      2   2
     / \ / \
    3  4 4  3
    



But the following `[1,2,2,null,3,null,3]` is not:

    
    
        1
       / \
      2   2
       \   \
       3    3
    



 **Note:**  
Bonus points if you could solve it both recursively and iteratively.

### Solution 1
Recursive--400ms:

    
    
    public boolean isSymmetric(TreeNode root) {
        return root==null || isSymmetricHelp(root.left, root.right);
    }
    
    private boolean isSymmetricHelp(TreeNode left, TreeNode right){
        if(left==null || right==null)
            return left==right;
        if(left.val!=right.val)
            return false;
        return isSymmetricHelp(left.left, right.right) && isSymmetricHelp(left.right, right.left);
    }
    

Non-recursive(use Stack)--460ms:

    
    
    public boolean isSymmetric(TreeNode root) {
        if(root==null)  return true;
        
        Stack<TreeNode> stack = new Stack<TreeNode>();
        TreeNode left, right;
        if(root.left!=null){
            if(root.right==null) return false;
            stack.push(root.left);
            stack.push(root.right);
        }
        else if(root.right!=null){
            return false;
        }
            
        while(!stack.empty()){
            if(stack.size()%2!=0)   return false;
            right = stack.pop();
            left = stack.pop();
            if(right.val!=left.val) return false;
            
            if(left.left!=null){
                if(right.right==null)   return false;
                stack.push(left.left);
                stack.push(right.right);
            }
            else if(right.right!=null){
                return false;
            }
                
            if(left.right!=null){
                if(right.left==null)   return false;
                stack.push(left.right);
                stack.push(right.left);
            }
            else if(right.left!=null){
                return false;
            }
        }
        
        return true;
    }


### Solution 2
    
    
     public boolean isSymmetric(TreeNode root) {
        if(root==null) return true;
        return isMirror(root.left,root.right);
    }
    public boolean isMirror(TreeNode p, TreeNode q) {
        if(p==null && q==null) return true;
        if(p==null || q==null) return false;
        return (p.val==q.val) && isMirror(p.left,q.right) && isMirror(p.right,q.left);
    }


### Solution 3
Basically, this question is recursively. Or we can say, the tree structure is
recursively, so the recursively solution maybe easy to write:

TC: O(b) SC: O(log n)

    
    
    class Solution:
      def isSymmetric(self, root):
        if root is None:
          return True
        else:
          return self.isMirror(root.left, root.right)
    
      def isMirror(self, left, right):
        if left is None and right is None:
          return True
        if left is None or right is None:
          return False
    
        if left.val == right.val:
          outPair = self.isMirror(left.left, right.right)
          inPiar = self.isMirror(left.right, right.left)
          return outPair and inPiar
        else:
          return False
    

The essence of recursively is Stack, so we can use our own stack to rewrite it
into iteratively:

    
    
     class Solution2:
      def isSymmetric(self, root):
        if root is None:
          return True
    
        stack = [[root.left, root.right]]
    
        while len(stack) > 0:
          pair = stack.pop(0)
          left = pair[0]
          right = pair[1]
    
          if left is None and right is None:
            continue
          if left is None or right is None:
            return False
          if left.val == right.val:
            stack.insert(0, [left.left, right.right])
    
            stack.insert(0, [left.right, right.left])
          else:
            return False
        return True



