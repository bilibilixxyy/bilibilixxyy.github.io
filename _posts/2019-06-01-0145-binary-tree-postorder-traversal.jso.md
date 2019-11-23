---
layout: post
title: 145. Binary Tree Postorder Traversal
---
### Question
Given a binary tree, return the _postorder_ traversal of its nodes' values.

 **Example:**

    
    
     **Input:**  [1,null,2,3]
       1
        \
         2
        /
       3
    
    **Output:**  [3,2,1]
    

**Follow up:** Recursive solution is trivial, could you do it iteratively?

### Solution 1
Here I summarize the iterative implementation for preorder, inorder, and
postorder traverse.

* * *

### Pre Order Traverse

* * *
    
    
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        Deque<TreeNode> stack = new ArrayDeque<>();
        TreeNode p = root;
        while(!stack.isEmpty() || p != null) {
            if(p != null) {
                stack.push(p);
                result.add(p.val);  // Add before going to children
                p = p.left;
            } else {
                TreeNode node = stack.pop();
                p = node.right;   
            }
        }
        return result;
    }
    

* * *

### In Order Traverse

* * *
    
    
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        Deque<TreeNode> stack = new ArrayDeque<>();
        TreeNode p = root;
        while(!stack.isEmpty() || p != null) {
            if(p != null) {
                stack.push(p);
                p = p.left;
            } else {
                TreeNode node = stack.pop();
                result.add(node.val);  // Add after all left children
                p = node.right;   
            }
        }
        return result;
    }
    

* * *

### Post Order Traverse

* * *
    
    
    public List<Integer> postorderTraversal(TreeNode root) {
        LinkedList<Integer> result = new LinkedList<>();
        Deque<TreeNode> stack = new ArrayDeque<>();
        TreeNode p = root;
        while(!stack.isEmpty() || p != null) {
            if(p != null) {
                stack.push(p);
                result.addFirst(p.val);  // Reverse the process of preorder
                p = p.right;             // Reverse the process of preorder
            } else {
                TreeNode node = stack.pop();
                p = node.left;           // Reverse the process of preorder
            }
        }
        return result;
    }


### Solution 2
pre-order traversal is **root-left-right** , and post order is **left-right-
root**. modify the code for pre-order to make it root-right-left, and then
**reverse** the output so that we can get left-right-root .

  1. Create an empty stack, Push root node to the stack.
  2. Do following while stack is not empty.

2.1. pop an item from the stack and print it.

2.2. push the left child of popped item to stack.

2.3. push the right child of popped item to stack.

  3. reverse the ouput.
    
        class Solution {
    public:
        vector<int> postorderTraversal(TreeNode *root) {
            stack<TreeNode*> nodeStack;
            vector<int> result;
            //base case
            if(root==NULL)
            return result;
            nodeStack.push(root);
        while(!nodeStack.empty())
        {
            TreeNode* node= nodeStack.top();  
            result.push_back(node->val);
            nodeStack.pop();
            if(node->left)
            nodeStack.push(node->left);
            if(node->right)
            nodeStack.push(node->right);
        }
         reverse(result.begin(),result.end());
         return result;
        
    }
    

};


### Solution 3
    
    
    public List<Integer> postorderTraversal(TreeNode root) {
    	LinkedList<Integer> ans = new LinkedList<>();
    	Stack<TreeNode> stack = new Stack<>();
    	if (root == null) return ans;
    	
    	stack.push(root);
    	while (!stack.isEmpty()) {
    		TreeNode cur = stack.pop();
    		ans.addFirst(cur.val);
    		if (cur.left != null) {
    			stack.push(cur.left);
    		}
    		if (cur.right != null) {
    			stack.push(cur.right);
    		} 
    	}
    	return ans;
    }



