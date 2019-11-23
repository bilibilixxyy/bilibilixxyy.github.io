---
layout: post
title: 904. Leaf-Similar Trees
---
### Question
Consider all the leaves of a binary tree.  From left to right order, the
values of those leaves form a _leaf value sequence._

![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/16/tree.png)

For example, in the given tree above, the leaf value sequence is `(6, 7, 4, 9,
8)`.

Two binary trees are considered _leaf-similar_  if their leaf value sequence
is the same.

Return `true` if and only if the two given trees with head nodes `root1` and
`root2` are leaf-similar.



**Note:**

  * Both of the given trees will have between `1` and `100` nodes.

### Solution 1
 **General methode is that traverse DFS whole tree to a list and compare two
lists.**

Here I share an idea of comparing node by node using `O(H)` space,  
where `H` is the height of the tree.

Use a `stack<TreeNode>` to keep dfs path.  
`dfs(stack)` will return next leaf.  
Check leaves one by one, until the end or difference.  
Only `O(H)` space for stack, no extra space for comparation.  
`O(1)` is also possible if we can modify the tree.

 **C++:**

    
    
        bool leafSimilar(TreeNode* root1, TreeNode* root2) {
            stack <TreeNode*> s1 , s2;
            s1.push(root1); s2.push(root2);
            while (!s1.empty() && !s2.empty())
                if (dfs(s1) != dfs(s2)) return false;
            return s1.empty() && s2.empty();
        }
    
        int dfs(stack<TreeNode*>& s) {
            while (true) {
                TreeNode* node = s.top(); s.pop();
                if (node->right) s.push(node->right);
                if (node->left) s.push(node->left);
                if (!node->left && !node->right) return node->val;
            }
        }
    

**Java:**

    
    
        public boolean leafSimilar(TreeNode root1, TreeNode root2) {
            Stack <TreeNode> s1 = new Stack<>(), s2 = new Stack<>();
            s1.push(root1); s2.push(root2);
            while (!s1.empty() && !s2.empty())
                if (dfs(s1) != dfs(s2)) return false;
            return s1.empty() && s2.empty();
        }
    
        public int dfs(Stack<TreeNode> s) {
            while (true) {
                TreeNode node = s.pop();
                if (node.right != null) s.push(node.right);
                if (node.left != null) s.push(node.left);
                if (node.left == null && node.right == null) return node.val;
            }
        }
    

**Python:**

    
    
        def leafSimilar(self, root1, root2):
            def dfs( node):
                if not node: return
                if not node.left and not node.right: yield node.val
                for i in dfs(node.left): yield i
                for i in dfs(node.right): yield i
            return all(a == b for a, b in itertools.izip_longest(dfs(root1), dfs(root2)))
    


### Solution 2
This problem is very similar to the tree traversal problems, actually to me,
they are the same, we just return the leaf node if `node.left == None and
node.right == None`. Otherwise, we recursively find the left leaves and right
leaves.

    
    
    class Solution(object):
        def leafSimilar(self, root1, root2):
            """
            :type root1: TreeNode
            :type root2: TreeNode
            :rtype: bool
            """
            return self.findleaf(root1) == self.findleaf(root2)
            
        def findleaf(self, root):
            if not root: return []
            if not (root.left or root.right): return [root.val]
            return self.findleaf(root.left) + self.findleaf(root.right)
    


### Solution 3
All you need to do is traverse for both trees and records their leaves as
string and then compare the strings.

    
    
        	 public boolean leafSimilar(TreeNode root1, TreeNode root2) {
    		 StringBuilder root1Leaves = new StringBuilder(),root2Leaves=new StringBuilder();
    		 traverse(root1,root1Leaves);traverse(root2,root2Leaves);	    
    		 return root1Leaves.toString().equals(root2Leaves.toString());
    	 }
    	 void traverse(TreeNode root,StringBuilder recordLeaves){
    		 if(root==null) return;
    		 if(root.left==null && root.right==null) recordLeaves.append(root.val+"-");
    		 traverse(root.left, recordLeaves);
    		 traverse(root.right, recordLeaves);
    	 }
    

Another solution using String - only 6 lines

    
    
    	 public boolean leafSimilar(TreeNode root1, TreeNode root2) { 
    		 return traverse(root1).equals(traverse(root2));
         }
    	 String traverse(TreeNode root){
    		 if(root==null) return "";
    		 if(root.left==null && root.right==null) return root.val+"-";  
    		 return traverse(root.left)+traverse(root.right);
         }
    



