---
layout: post
title: 404. Sum of Left Leaves
---
### Question
Find the sum of all left leaves in a given binary tree.

 **Example:**

    
    
        3
       / \
      9  20
        /  \
       15   7
    
    There are two left leaves in the binary tree, with values **9** and **15** respectively. Return **24**.
    

### Solution 1
**Recursive method.** For given node we check whether its left child is a
leaf. If it is the case, we add its value to answer, otherwise recursively
call method on left child. For right child we call method only if it has at
least one nonnull child.

    
    
     public int sumOfLeftLeaves(TreeNode root) {
        if(root == null) return 0;
        int ans = 0;
        if(root.left != null) {
            if(root.left.left == null && root.left.right == null) ans += root.left.val;
            else ans += sumOfLeftLeaves(root.left);
        }
        ans += sumOfLeftLeaves(root.right);
        
        return ans;
    }
    

**Iterative method.** Here for each node in the tree we check whether its left
child is a leaf. If it is true, we add its value to answer, otherwise add left
child to the stack to process it later. For right child we add it to stack
only if it is not a leaf.

    
    
    public int sumOfLeftLeaves(TreeNode root) {
        if(root == null) return  0;
        int ans = 0;
        Stack<TreeNode> stack = new Stack<TreeNode>();
        stack.push(root);
        
        while(!stack.empty()) {
            TreeNode node = stack.pop();
            if(node.left != null) {
                if (node.left.left == null && node.left.right == null)
                    ans += node.left.val;
                else
                    stack.push(node.left);
            }
            if(node.right != null) {
                if (node.right.left != null || node.right.right != null)
                    stack.push(node.right);
            }
        }
        return ans;
    }
    


### Solution 2
    
    
    public class Solution {
        public int sumOfLeftLeaves(TreeNode root) {
            if(root == null || root.left == null && root.right == null) return 0;
            
            int res = 0;
            Queue<TreeNode> queue = new LinkedList<>();
            queue.offer(root);
            
            while(!queue.isEmpty()) {
                TreeNode curr = queue.poll();
    
                if(curr.left != null && curr.left.left == null && curr.left.right == null) res += curr.left.val;
                if(curr.left != null) queue.offer(curr.left);
                if(curr.right != null) queue.offer(curr.right);
            }
            return res;
        }
    }
    


### Solution 3
    
    
    class Solution {
    public:
        int sumOfLeftLeaves(TreeNode* root) {
            if (!root) return 0;
            if (root->left && !root->left->left && !root->left->right) return root->left->val + sumOfLeftLeaves(root->right);
            return sumOfLeftLeaves(root->left) + sumOfLeftLeaves(root->right);
        }
    };
    



