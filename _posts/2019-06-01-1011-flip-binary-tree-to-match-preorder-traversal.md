---
layout: post
title: 1011. Flip Binary Tree To Match Preorder Traversal
---
### Question
Given a binary tree with `N` nodes, each node has a different value from `{1,
..., N}`.

A node in this binary tree can be _flipped_  by swapping the left child and
the right child of that node.

Consider the sequence of `N` values reported by a preorder traversal starting
from the root.  Call such a sequence of `N` values the  _voyage_  of the tree.

(Recall that a _preorder traversal_  of a node means we report the current
node's value, then preorder-traverse the left child, then preorder-traverse
the right child.)

Our goal is to flip the **least number** of nodes in the tree so that the
voyage of the tree matches the `voyage` we are given.

If we can do so, then return a list of the values of all nodes flipped.  You
may return the answer in any order.

If we cannot do so, then return the list `[-1]`.



 **Example 1:**

 **![](https://assets.leetcode.com/uploads/2019/01/02/1219-01.png)**

    
    
     **Input:** root = [1,2], voyage = [2,1]
    **Output:** [-1]
    

**Example 2:**

**![](https://assets.leetcode.com/uploads/2019/01/02/1219-02.png)**

    
    
    **Input:** root = [1,2,3], voyage = [1,3,2]
    **Output:** [1]
    

**Example 3:**

**![](https://assets.leetcode.com/uploads/2019/01/02/1219-02.png)**

    
    
    **Input:** root = [1,2,3], voyage = [1,2,3]
    **Output:** []
    



 **Note:**

  1. `1 <= N <= 100`

### Solution 1
 **Explanation:**  
Global integer `i` indicates next index in voyage `v`.  
If current `node == null`, it's fine, we return `true`  
If current `node.val != v[i]`, doesn't match wanted value, return `false`  
If left child exist but don't have wanted value, flip it with right child.

 **Java:**

    
    
        List <Integer> res = new ArrayList<>();
        int i = 0;
        public List<Integer> flipMatchVoyage(TreeNode root, int[] v) {
            return dfs(root, v) ? res : Arrays.asList(-1);
        }
    
        public Boolean dfs(TreeNode node, int[] v) {
            if (node == null) return true;
            if (node.val != v[i++]) return false;
            if (node.left != null && node.left.val != v[i]) {
                res.add(node.val);
                return dfs(node.right, v) && dfs(node.left, v);
            }
            return dfs(node.left, v) && dfs(node.right, v);
        }
    

**C++:**

    
    
        vector <int> res;
        int i = 0;
        vector<int> flipMatchVoyage(TreeNode* root, vector<int>& v) {
            return dfs(root, v) ? res : vector<int>{-1};
        }
    
        bool dfs(TreeNode* node, vector<int>& v) {
            if (!node) return true;
            if (node->val != v[i++]) return false;
            if (node->left && node->left->val != v[i]) {
                res.push_back(node->val);
                return dfs(node->right, v) && dfs(node->left, v);
            }
            return dfs(node->left, v) && dfs(node->right, v);
        }
    

**Python:**

    
    
        def flipMatchVoyage(self, root, voyage):
             res = []
            self.i = 0
            def dfs(root):
                if not root: return True
                if root.val != voyage[self.i]: return False
                self.i += 1
                if root.left and root.left.val != voyage[self.i]:
                    res.append(root.val)
                    root.left,root.right = root.right, root.left
                return dfs(root.left) and dfs(root.right)
            return res if dfs(root) else [-1]
    


### Solution 2
Note: the clause "flip the least number of nodes" in the problem statement is
missleading. There could be zero, or only one possible tree re-arrangement
because we have to flip if the next value in the arrangement is not equal to
the left child value.

With that, we can just do pre-order traversal and verify that the current node
value matches the current voyage value. Also, we check if the next voyage
value matches the left child value; if it does not - we flip left and right
child and continue. In order to preserve the original tree structure, we are
flipping local variables `l` and `r`.

    
    
    vector<int> flips;
    bool traverse(TreeNode* root, vector<int>& voyage, int &pos) {
      if (root == nullptr) return true;
      if (root->val != voyage[pos++]) return false;
      auto l = root->left, r = root->right;
      if (l != nullptr && l->val != voyage[pos]) {
        flips.push_back(root->val);
        swap(l, r);
      }
      return traverse(l, voyage, pos) && traverse(r, voyage, pos);
    }
    vector<int> flipMatchVoyage(TreeNode* root, vector<int>& voyage, int pos = 0) {
      return traverse(root, voyage, pos) ? flips : vector<int>() = { -1 };
    }
    


### Solution 3
  1. Input `i` of `dfs()` is the current index in `voyage`, then `root.val == voyage[i]`, otherwise return `-1`.
  2. The return of `dfs()` is the index in `voyage` for the successor of preorder traversal.

    
    
    class Solution {
        public List<Integer> flipMatchVoyage(TreeNode root, int[] voyage) {
            List<Integer> path = new ArrayList<>();
            if (dfs(root, voyage, 0, path) == -1) {
                List<Integer> result = new ArrayList<>();
                result.add(-1);
                return result;
            }
            return path;
        }
        private int dfs(TreeNode root, int[] voyage, int i, List<Integer> path) {
            if (root == null) {
                return i;
            }
            if (root.val != voyage[i]) {
                return -1;
            }
            int left = dfs(root.left, voyage, i + 1, path);
            if (left != -1) {
                return dfs(root.right, voyage, left, path);
            }
    		// need a flip
            path.add(root.val);
            int right = dfs(root.right, voyage, i + 1, path);
            if (right != -1) {
                return dfs(root.left, voyage, right, path);
            }
            return -1;
        }
    }
    



