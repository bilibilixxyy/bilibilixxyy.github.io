---
layout: post
title: 1207. Delete Nodes And Return Forest
---
### Question
Given the `root` of a binary tree, each node in the tree has a distinct value.

After deleting all nodes with a value in `to_delete`, we are left with a
forest (a disjoint union of trees).

Return the roots of the trees in the remaining forest.  You may return the
result in any order.



 **Example 1:**

 **![](https://assets.leetcode.com/uploads/2019/07/01/screen-
shot-2019-07-01-at-53836-pm.png)**

    
    
     **Input:** root = [1,2,3,4,5,6,7], to_delete = [3,5]
    **Output:** [[1,2,null,4],[6],[7]]
    



 **Constraints:**

  * The number of nodes in the given tree is at most `1000`.
  * Each node has a distinct value between `1` and `1000`.
  * `to_delete.length <= 1000`
  * `to_delete` contains distinct values between `1` and `1000`.

### Solution 1
##  **Intuition**

As I keep saying in my "courses",  
solve tree problem with recursion first.  
  

##  **Explanation**

If a `node` is root (has no parent) and isn't deleted,  
when will we add it to the `result`.  
  

##  **Complexity**

Time `O(N)`  
Space `O(N)`

  

 **Java:**

    
    
        Set<Integer> to_delete_set;
        List<TreeNode> res =  new ArrayList<>();
        public List<TreeNode> delNodes(TreeNode root, int[] to_delete) {
            res = new ArrayList<>();
            to_delete_set = new HashSet<>();
            for (int i : to_delete)
                to_delete_set.add(i);
            helper(root, true);
            return res;
        }
    
        private TreeNode helper(TreeNode node, boolean is_root) {
            if (node == null) return null;
            boolean deleted = to_delete_set.contains(node.val);
            if (is_root && !deleted) res.add(node);
            node.left = helper(node.left, deleted);
            node.right =  helper(node.right, deleted);
            return deleted ? null : node;
        }
    

**Python:**

    
    
         def delNodes(self, root, to_delete):
            to_delete_set = set(to_delete)
            res = []
    
            def helper(root, is_root):
                if not root: return None
                root_deleted = root.val in to_delete_set
                if is_root and not root_deleted:
                    res.append(root)
                root.left = helper(root.left, root_deleted)
                root.right = helper(root.right, root_deleted)
                return None if root_deleted else root
            helper(root, True)
            return res
    


### Solution 2
### Question analysis

The question is composed of two requirements:

  * To remove a node, the child need to **notify** its parent about the child's existance.
  * To determine whether a node is a root node in the final forest, we need to know [1] whether the node is removed (which is trivial), and [2] whether its parent is removed (which requires the parent to **notify** the child)

It is very obvious that a tree problem is likely to be solved by recursion.
The two components above are actually examining interviewees' understanding to
the two key points of **recursion** :

  * passing info downwards -- by arguments
  * passing info upwards -- by return value

* * *

### Fun fact

I've seen this question in 2016 at a real newgrad interview. It was asked by a
very well-known company and was a publicly known question at that time. I
highly doubt that the company will ask this question again nowadays.

* * *

### Code [Python]

    
    
    class Solution(object):
        def delNodes(self, root, to_delete):
            to_delete = set(to_delete)
            res = []
            def walk(root, parent_exist):
                if root is None:
                    return None
                if root.val in to_delete:
                    root.left = walk(root.left, parent_exist=False)
                    root.right = walk(root.right, parent_exist=False)
                    return None
                else:
                    if not parent_exist:
                        res.append(root)
                    root.left = walk(root.left, parent_exist=True)
                    root.right = walk(root.right, parent_exist=True)
                    return root
            walk(root, parent_exist=False)
            return res
    


### Solution 3
    
    
    public List<TreeNode> delNodes(TreeNode root, int[] to_delete) {
            List<TreeNode> forest = new ArrayList<>();
            if (root == null) return forest;
            Set<Integer> set = new HashSet<>();
            for(int i : to_delete) {
                set.add(i);
            }
            deleteNodes(root, set, forest);
            if (!set.contains(root.val)) {
                forest.add(root);
            }
            return forest;
        }
    
        private TreeNode deleteNodes(TreeNode node, Set<Integer> set, List<TreeNode> forest) {
            if (node == null) return null;
    
            node.left = deleteNodes(node.left, set, forest);
            node.right = deleteNodes(node.right, set, forest);
    
            if (set.contains(node.val)) {
                if (node.left != null) forest.add(node.left);
                if (node.right != null) forest.add(node.right);
                return null;
            }
    
            return node;
        }
    



