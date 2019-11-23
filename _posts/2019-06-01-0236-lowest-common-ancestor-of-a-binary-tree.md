---
layout: post
title: 236. Lowest Common Ancestor of a Binary Tree
---
### Question
Given a binary tree, find the lowest common ancestor (LCA) of two given nodes
in the tree.

According to the [definition of LCA on
Wikipedia](https://en.wikipedia.org/wiki/Lowest_common_ancestor): “The lowest
common ancestor is defined between two nodes p and q as the lowest node in T
that has both p and q as descendants (where we allow **a node to be a
descendant of itself** ).”

Given the following binary tree:  root = [3,5,1,6,2,0,8,null,null,7,4]

![](https://assets.leetcode.com/uploads/2018/12/14/binarytree.png)



 **Example 1:**

    
    
     **Input:** root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
    **Output:** 3
    **Explanation:** The LCA of nodes 5 and 1 is 3.
    

**Example 2:**

    
    
    **Input:** root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
    **Output:** 5
    **Explanation:** The LCA of nodes 5 and 4 is 5, since a node can be a descendant of itself according to the LCA definition.
    



 **Note:**

  * All of the nodes' values will be unique.
  * p and q are different and both values will exist in the binary tree.

### Solution 1
Same solution in several languages. It's recursive and expands the meaning of
the function. If the current (sub)tree contains both p and q, then the
function result is their LCA. If only one of them is in that subtree, then the
result is that one of them. If neither are in that subtree, the result is
null/None/nil.

Update: I also wrote [two iterative
solutions](https://leetcode.com/discuss/45603/iterative-solution) now, one of
them being a version of the solution here. They're more complicated than this
simple recursive solution, but I do find them interesting.

* * *

 **C++**

    
    
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
         if (!root || root == p || root == q) return root;
        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);
        return !left ? right : !right ? left : root;
    }
    

* * *

**Python**

    
    
    def lowestCommonAncestor( self, root, p, q):
        if root in (None, p, q): return root
        left, right = (self.lowestCommonAncestor(kid, p, q)
                       for kid in (root.left, root.right))
        return root if left and right else left or right
    

Or using that `None` is considered smaller than any node:

    
    
    def lowestCommonAncestor(self, root, p, q):
        if root in (None, p, q): return root
        subs = [self.lowestCommonAncestor(kid, p, q)
                for kid in (root.left, root.right)]
        return root if all(subs) else max(subs)
    

* * *

**Ruby**

    
    
    def lowest _common_ancestor(root, p, q)
        return root if [nil, p, q].index root
        left = lowest_common_ancestor(root.left, p, q)
        right = lowest_common_ancestor(root.right, p, q)
        left && right ? root : left || right
    end
    

* * *

**Java**

    
    
    public TreeNode lowest CommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) return root;
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        return left == null ? right : right == null ? left : root;
    }


### Solution 2
    
    
    public class Solution {
        public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
            if(root == null || root == p || root == q)  return root;
            TreeNode left = lowestCommonAncestor(root.left, p, q);
            TreeNode right = lowestCommonAncestor(root.right, p, q);
            if(left != null && right != null)   return root;
            return left != null ? left : right;
        }
    }


### Solution 3
 **Python**

    
    
    def lowestCommonAncestor(self, root, p, q):
        stack = [root]
        parent = {root: None}
        while p not  in parent or q not in parent:
            node = stack.pop()
            if node.left:
                parent[node.left] = node
                stack.append(node.left)
            if node.right:
                parent[node.right] = node
                stack.append(node.right)
        ancestors = set()
        while p:
            ancestors.add(p)
            p = parent[p]
        while q not in ancestors:
            q = parent[q]
        return q
    
    # 31 / 31 test cases passed.
    # Status: Accepted
    # Runtime: 108 ms
    # 99.14%
    

**Java**

    
    
    public class Solution {
        public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
            Map <TreeNode, TreeNode> parent = new HashMap<>();
            Deque<TreeNode> stack = new ArrayDeque<>();
            parent.put(root, null);
            stack.push(root);
    
            while (!parent.containsKey(p) || !parent.containsKey(q)) {
                TreeNode node = stack.pop();
                if (node.left != null) {
                    parent.put(node.left, node);
                    stack.push(node.left);
                }
                if (node.right != null) {
                    parent.put(node.right, node);
                    stack.push(node.right);
                }
            }
            Set<TreeNode> ancestors = new HashSet<>();
            while (p != null) {
                ancestors.add(p);
                p = parent.get(p);
            }
            while (!ancestors.contains(q))
                q = parent.get(q);
            return q;
        }
    }
    

To find the lowest common ancestor, we need to find where is `p` and `q` and a
way to track their ancestors. A `parent` pointer for each node found is good
for the job. After we found both `p` and `q`, we create a set of `p`'s
`ancestors`. Then we travel through `q`'s `ancestors`, the first one appears
in `p`'s is our answer.



