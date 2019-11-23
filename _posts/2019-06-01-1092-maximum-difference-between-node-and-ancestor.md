---
layout: post
title: 1092. Maximum Difference Between Node and Ancestor
---
### Question
Given the `root` of a binary tree, find the maximum value `V` for which there
exists **different** nodes `A` and `B` where `V = |A.val - B.val|` and `A` is
an ancestor of `B`.

(A node A is an ancestor of B if either: any child of A is equal to B, or any
child of A is an ancestor of B.)



 **Example 1:**

![](http://i68.tinypic.com/2whqcep.jpg)

    
    
     **Input:** [8,3,10,1,6,null,14,null,null,4,7,13]
    **Output:** 7
    **Explanation:**
    We have various ancestor-node differences, some of which are given below :
    |8 - 3| = 5
    |3 - 7| = 4
    |8 - 1| = 7
    |10 - 13| = 3
    Among all possible differences, the maximum value of 7 is obtained by |8 - 1| = 7.
    



 **Note:**

  1. The number of nodes in the tree is between `2` and `5000`.
  2. Each node will have value between `0` and `100000`.

### Solution 1
We pass the minimum and maximum values to the children,  
At the leaf node, we return `max - min` through the path from the root to the
leaf.

 **Java:**

    
    
        public  int maxAncestorDiff(TreeNode root) {
            return dfs(root, root.val, root.val);
        }
    
        public int dfs(TreeNode root, int mn, int mx) {
            if (root == null) return mx - mn;
            mx = Math.max(mx, root.val);
            mn = Math.min(mn, root.val);
            return Math.max(dfs(root.left, mn, mx), dfs(root.right, mn, mx));
        }
    

**C++ 1-line**

    
    
         int maxAncestorDiff(TreeNode* r, int mn = 100000, int mx = 0) {
            return r ? max(maxAncestorDiff(r->left, min(mn, r->val), max(mx, r->val)),
            maxAncestorDiff(r->right, min(mn, r->val), max(mx, r->val))) : mx - mn;
        }
    

**Python 1-line**

    
    
        def max AncestorDiff(self, root, mn=100000, mx=0):
            return max(self.maxAncestorDiff(root.left, min(mn, root.val), max(mx, root.val)), \
                self.maxAncestorDiff(root.right, min(mn, root.val), max(mx, root.val))) \
                if root else mx - mn
    


### Solution 2
# Intuition

To make sure min/max values belong to an ancestor, we track min/max from the
root till the leaf, and pick the biggest difference among all leaves.

# Solution

"Unpacked" version:

    
    
    int maxAncestorDiff(TreeNode* r, int mn = 100000, int mx = 0) {
      if (r == nullptr) return mx - mn;
      mx = max(mx, r->val);
      mn = min(mn, r->val);
      return max(maxAncestorDiff(r->left, mn, mx), maxAncestorDiff(r->right, mn, mx));
    }
    

And one-liner:

    
    
    int maxAncestorDiff(TreeNode* r, int mn = 100000, int mx = 0) {
      return r == nullptr ? mx - mn :
        max(maxAncestorDiff(r->left, min(mn, r->val), max(mx, r->val)),
          maxAncestorDiff(r->right, min(mn, r->val), max(mx, r->val)));
    }
    


### Solution 3
    
    
    class Solution(object):
        def maxAncestorDiff(self, root):
            """
            :type root: TreeNode
            :rtype: int
            """
            if not root: return 0
            return self.helper(root, root.val, root.val)
        
        def helper(self, node, high, low):
            if not node:
                return high - low
            high = max(high, node.val)
            low = min(low, node.val)
            return max(self.helper(node.left, high, low), self.helper(node.right,high,low))
    



