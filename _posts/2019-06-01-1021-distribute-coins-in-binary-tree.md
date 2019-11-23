---
layout: post
title: 1021. Distribute Coins in Binary Tree
---
### Question
Given the `root` of a binary tree with `N` nodes, each `node` in the tree has
`node.val` coins, and there are `N` coins total.

In one move, we may choose two adjacent nodes and move one coin from one node
to another.  (The move may be from parent to child, or from child to parent.)

Return the number of moves required to make every node have exactly one coin.



 **Example 1:**

 **![](https://assets.leetcode.com/uploads/2019/01/18/tree1.png)**

    
    
     **Input:** [3,0,0]
    **Output:** 2
    **Explanation:** From the root of the tree, we move one coin to its left child, and one coin to its right child.
    

**Example 2:**

**![](https://assets.leetcode.com/uploads/2019/01/18/tree2.png)**

    
    
    **Input:** [0,3,0]
    **Output:** 3
    **Explanation:** From the left child of the root, we move two coins to the root [taking two moves].  Then, we move one coin from the root of the tree to the right child.
    

**Example 3:**

**![](https://assets.leetcode.com/uploads/2019/01/18/tree3.png)**

    
    
    **Input:** [1,0,2]
    **Output:** 2
    

**Example 4:**

**![](https://assets.leetcode.com/uploads/2019/01/18/tree4.png)**

    
    
    **Input:** [1,0,0,null,3]
    **Output:** 4
    



 **Note:**

  1. `1<= N <= 100`
  2. `0 <= node.val <= N`

### Solution 1
We traverse childs first (post-order traversal), and return the ballance of
coins. For example, if we get '+3' from the left child, that means that the
left subtree has 3 extra coins to move out. If we get '-1' from the right
child, we need to move 1 coin in. So, we increase the number of moves by 4 (3
moves out left + 1 moves in right). We then return the final ballance: r->val
(coins in the root) + 3 (left) + (-1) (right) - 1 (keep one coin for the
root).  
![image](https://assets.leetcode.com/users/votrubac/image_1548011422.png)

    
    
    int traverse(TreeNode* r, int &moves) {
      if (r == nullptr) return 0;
      int left = traverse(r->left, moves), right = traverse(r->right, moves);
      moves += abs(left) + abs(right);
      return r->val + left + right - 1;
    }
    int distributeCoins(TreeNode* r, int moves = 0) {
      traverse(r, moves);
      return moves;
    }
    

If we can modify values of tree nodes, we can store the balance in nodes, and
use the return value to accumulate the number of moves. This way we can get
rid of the helper method. The solution below is courtesy of
[Lee](https://leetcode.com/lee215/):

    
    
    int distributeCoins(TreeNode* r, TreeNode* p = nullptr) {
      if (r == nullptr) return 0;
      int res = distributeCoins(r->left, r) + distributeCoins(r->right, r);
      if (p != nullptr) p->val += r->val - 1;
      return res + abs(r->val - 1);
    }
    


### Solution 2
## Solution 1

Quite similar problem as this one 968.Binary Tree Cameras.  
So I put this as the first solution.  
Write a dfs helper, return the number of coins given to the parent.

 **Java:**

    
    
         int res = 0;
        public int distributeCoins(TreeNode root) {
            dfs(root);
            return res;
        }
    
        public int dfs(TreeNode root) {
            if (root == null) return 0;
            int left = dfs(root.left), right = dfs(root.right);
            res += Math.abs(left) + Math.abs(right);
            return root.val + left + right - 1;
        }
    

**C++:**

    
    
        int res =  0;
        int distributeCoins(TreeNode* root) {
            dfs(root);
            return res;
        }
        int dfs(TreeNode* root) {
            if (!root) return 0;
            int left = dfs(root->left), right = dfs(root->right);
            res += abs(left) + abs(right);
            return root->val + left + right - 1;
        }
    

**Python:**

    
    
         res = 0
        def distributeCoins(self, root):
            def dfs(root):
                if not root: return 0
                left = dfs(root.left)
                right = dfs(root.right)
                self.res += abs(left) + abs(right)
                return root.val + left + right - 1
            dfs(root)
            return self.res
    

## Solution 2

As somebody may not like global variable,  
here is the recursive version without helper.

**Java:**

    
    
         public int distributeCoins(TreeNode root) {
            int res = 0;
            if (root.left != null) {
                res += distributeCoins(root.left);
                root.val += root.left.val - 1;
                res += Math.abs(root.left.val - 1);
            }
            if (root.right != null) {
                res += distributeCoins(root.right);
                root.val += root.right.val - 1;
                res += Math.abs(root.right.val - 1);
            }
            return res;
        }
    

**C++:**

    
    
        int distributeCoins(TreeNode* root) {
            int res =  0;
            if (root->left) {
                res += distributeCoins(root->left);
                root->val += root->left->val - 1;
                res += abs(root->left->val - 1);
            }
            if (root->right) {
                res += distributeCoins(root->right);
                root->val += root->right->val - 1;
                res += abs(root->right->val - 1);
            }
            return res;
        }
    

## Solution 3

Give the parent node so we can move the coins to the parent directly.  
**C++:**

    
    
         int distributeCoins(TreeNode* root, TreeNode* pre = NULL) {
            if (!root) return 0;
            int res = distributeCoins(root->left, root) + distributeCoins(root->right, root);
            if (pre) pre->val += root->val - 1;
            return res + abs(root->val - 1);
        }
    

**Python:**

    
    
        def distributeCoins(self, root,  pre=None):
            if not root: return 0
            res = self.distributeCoins(root.left, root) + self.distributeCoins(root.right, root)
            if pre: pre.val += root.val - 1
            return res + abs(root.val - 1)
    


### Solution 3
I'd like to share my recursive solution. I know there are better ways, which
only record one value instead of two. But my solution is more understandable
----

For each node, we use an int array to record the information **[# of nodes in
the subtree (include itself), # of total coins in the subtree (include
itself)]**. Then we know that for a certain node, if there are **more coins
than nodes** in the subtree, the node must "contribute" the redundant coins to
its parent. Else, if there are **more nodes than coins** in the subtree, then
the node must take some coins from the parent.

Both of these two operations will create moves in the tree. And we just need
to add the **absolute value** of the (# nodes - # coins) to the final result
because the move can be "contribute" or "take". The time complexity is O(n)
because we are just traversing the tree.

    
    
     class Solution {
        int moves = 0;
        public int distributeCoins(TreeNode root) {
            getNumAndCoins(root);
            return moves;
        }
        
        /*
         * return [number_of_nodes_in_subtree, number_of_total_coins_in_subtree]
         */
        private int[] getNumAndCoins(TreeNode node) {
            if (node == null) return new int[] {0, 0};
            int[] left = getNumAndCoins(node.left);
            int[] right = getNumAndCoins(node.right);
            moves += Math.abs(left[0] - left[1]) + Math.abs(right[0] - right[1]);
            return new int[] {left[0] + right[0] + 1, left[1] + right[1] + node.val};
        }
    }
    

After understanding this, it will be trivial to understand others' posts,
which just use the value of **(# of nodes in the subtree - # of total coins in
the subtree)** as the return value.



