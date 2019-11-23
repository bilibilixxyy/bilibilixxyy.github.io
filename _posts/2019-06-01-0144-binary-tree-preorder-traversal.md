---
layout: post
title: 144. Binary Tree Preorder Traversal
---
### Question
Given a binary tree, return the _preorder_ traversal of its nodes' values.

 **Example:**

    
    
     **Input:**  [1,null,2,3]
       1
        \
         2
        /
       3
    
    **Output:**  [1,2,3]
    

**Follow up:** Recursive solution is trivial, could you do it iteratively?

### Solution 1
Note that in this solution only right children are stored to stack.

    
    
    public List<Integer> preorderTraversal(TreeNode node) {
    	List<Integer> list = new LinkedList<Integer>();
    	Stack<TreeNode> rights = new Stack<TreeNode>();
    	while(node != null) {
    		list.add(node.val);
    		if (node.right != null) {
    			rights.push(node.right);
    		}
    		node = node.left;
    		if (node == null && !rights.isEmpty()) {
    			node = rights.pop();
    		}
    	}
        return list;
    }


### Solution 2
Recursive method with List as returning value:

    
    
    	public List<Integer> preorderTraversal(TreeNode root) {
    		List<Integer> pre = new LinkedList<Integer>();
    		if(root==null) return pre;
    		pre.add(root.val);
    		pre.addAll(preorderTraversal(root.left));
    		pre.addAll(preorderTraversal(root.right));
    		return pre;
    	}
    

Recursive method with Helper method to have a List as paramater, so we can
modify the parameter and don't have to instantiate a new List at each
recursive call:

    
    
    	public List<Integer> preorderTraversal(TreeNode root) {
    		List<Integer> pre = new LinkedList<Integer>();
    		preHelper(root,pre);
    		return pre;
    	}
    	public void preHelper(TreeNode root, List<Integer> pre) {
    		if(root==null) return;
    		pre.add(root.val);
    		preHelper(root.left,pre);
    		preHelper(root.right,pre);
    	}
    

Iterative method with Stack:

    
    
    	public List<Integer> preorderIt(TreeNode root) {
    		List<Integer> pre = new LinkedList<Integer>();
    		if(root==null) return pre;
    		Stack<TreeNode> tovisit = new Stack<TreeNode>();
    		tovisit.push(root);
    		while(!tovisit.empty()) {
    			TreeNode visiting = tovisit.pop();
    			pre.add(visiting.val);
    			if(visiting.right!=null) tovisit.push(visiting.right);
    			if(visiting.left!=null) tovisit.push(visiting.left);
    		}
    		return pre;
    	}


### Solution 3
Classical usage of stack's LIFO feature, very easy to grasp:

    
    
    def preorderTraversal(self, root):
        ret = []
        stack = [root]
        while stack:
            node = stack.pop()
            if node:
                ret.append(node.val)
                stack.append(node.right)
                stack.append(node.left)
        return ret



