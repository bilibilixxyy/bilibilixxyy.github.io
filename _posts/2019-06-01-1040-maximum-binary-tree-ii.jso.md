---
layout: post
title: 1040. Maximum Binary Tree II
---
### Question
We are given the `root` node of a _maximum tree:_ a tree where every node has
a value greater than any other value in its subtree.

Just as in the [previous problem](https://leetcode.com/problems/maximum-
binary-tree/), the given tree was constructed from an list `A` (`root =
Construct(A)`) recursively with the following `Construct(A)` routine:

  * If `A` is empty, return `null`.
  * Otherwise, let `A[i]` be the largest element of `A`.  Create a `root` node with value `A[i]`.
  * The left child of `root` will be `Construct([A[0], A[1], ..., A[i-1]])`
  * The right child of `root` will be `Construct([A[i+1], A[i+2], ..., A[A.length - 1]])`
  * Return `root`.

Note that we were not given A directly, only a root node `root =
Construct(A)`.

Suppose `B` is a copy of `A` with the value `val` appended to it.  It is
guaranteed that `B` has unique values.

Return `Construct(B)`.



 **Example 1:**

 **![](https://assets.leetcode.com/uploads/2019/02/21/maximum-binary-
tree-1-1.png)![](https://assets.leetcode.com/uploads/2019/02/21/maximum-
binary-tree-1-2.png)**

    
    
     **Input:** root = [4,1,3,null,null,2], val = 5
    **Output:** [5,4,null,1,3,null,null,2]
    **Explanation:** A =  [1,4,2,3], B = [1,4,2,3,5]
    

**Example 2:  
![](https://assets.leetcode.com/uploads/2019/02/21/maximum-binary-
tree-2-1.png)![](https://assets.leetcode.com/uploads/2019/02/21/maximum-
binary-tree-2-2.png)**

    
    
    **Input:** root = [5,2,4,null,1], val = 3
    **Output:** [5,2,4,null,1,null,3]
    **Explanation:** A =  [2,1,5,4], B = [2,1,5,4,3]
    

**Example 3:  
![](https://assets.leetcode.com/uploads/2019/02/21/maximum-binary-
tree-3-1.png)![](https://assets.leetcode.com/uploads/2019/02/21/maximum-
binary-tree-3-2.png)**

    
    
    **Input:** root = [5,2,3,null,1], val = 4
    **Output:** [5,2,4,null,1,3]
    **Explanation:** A =  [2,1,5,3], B = [2,1,5,3,4]
    



 **Note:**

  1. `1 <= B.length <= 100`

### Solution 1
##  **Solution 1: Recursion**

If root.val > val, recusion on the right.  
Else, put right subtree on the left of new node `TreeNode(val)`

 **Time Complexity** :  
O(N) time,  
O(N) recursion space.

  

 **Java:**

    
    
        public TreeNode insert IntoMaxTree(TreeNode root, int val) {
            if (root != null && root.val > val) {
                root.right = insertIntoMaxTree(root.right, val);
                return root;
            }
            TreeNode node = new TreeNode(val);
            node.left = root;
            return node;
        }
    

**C++:**

    
    
        TreeNode* insert IntoMaxTree(TreeNode* root, int val) {
            if (root && root->val > val) {
                root->right = insertIntoMaxTree(root->right, val);
                return root;
            }
            TreeNode* node = new TreeNode(val);
            node->left = root;
            return node;        
        }
    

**Python:**

    
    
        def insert IntoMaxTree(self, root, val):
            if root and root.val > val:
                root.right = self.insertIntoMaxTree(root.right, val)
                return root
            node = TreeNode(val)
            node.left = root
            return node
    

  

## **Solution 2: Iteration**

Search on the right, find the node that `cur.val > val > cur.right.val`  
Then create new node `TreeNode(val)`,  
put old `cur.right` as `node.left`,  
put `node` as new `cur.right`.

**Time Complexity** :  
`O(N)` time,  
`O(1)` space

**Java:**

    
    
         public TreeNode insertIntoMaxTree(TreeNode root, int val) {
            TreeNode node = new TreeNode(val), cur = root;
            if (root.val < val) {
                node.left = root;
                return node;
            }
            while (cur.right != null && cur.right.val > val) {
                cur = cur.right;
            }
            node.left = cur.right;
            cur.right = node;
            return root;
        }
    

**C++:**

    
    
        TreeNode* insertIntoMaxTree(TreeNode* root, int val) {
            TreeNode* node = new TreeNode(val), *cur = root;
             if (root->val < val) {
                node->left = root;
                return node;
            }
            while (cur->right && cur->right->val > val) {
                cur = cur->right;
            }
            node->left = cur->right;
            cur->right = node;
            return root;
        }
    

**Python:**

    
    
        def insertIntoMaxTree(self, root, val):
            pre,cur = None, root
            while cur  and cur.val > val:
                pre, cur = cur, cur.right
            node = TreeNode(val)
            node.left = cur
            if pre: pre.right = node
            return root if root.val > val else node
    


### Solution 2
The idea is to insert node to the right parent or right sub-tree of current
node. Using recursion can achieve this:  
If inserted value is greater than current node, the inserted goes to right
parent  
If inserted value is smaller than current node, we recursively re-cauculate
right subtree

    
    
    class Solution {
        public TreeNode insertIntoMaxTree(TreeNode root, int v) {
            if(root==null)return new TreeNode(v);
            if(root.val<v){
                TreeNode node = new TreeNode(v);
                node.left=root;
                return node;
            }
            root.right=insertIntoMaxTree(root.right,v);
            return root;
        }
    }
    


### Solution 3
If the value is greater than the root value, it becomes the new root, and the
old root becomes the left child (`5` becomes the new root):  
![image](https://assets.leetcode.com/uploads/2019/02/21/maximum-binary-
tree-1-1.png)![image](https://assets.leetcode.com/uploads/2019/02/21/maximum-
binary-tree-1-2.png)  
Otherwise, the value needs to be inserted into the right subtree of the root.
So we repeat this procedure recursively for the right subtree (`4` is inserted
into the right subtree, and becomes the new root of that subtree):  
![image](https://assets.leetcode.com/uploads/2019/02/21/maximum-binary-
tree-3-1.png)![image](https://assets.leetcode.com/uploads/2019/02/21/maximum-
binary-tree-3-2.png)

    
    
    TreeNode* insertIntoMaxTree(TreeNode* root, int val) {
      if (root == nullptr || val > root->val) {
        auto n = new TreeNode(val);
        n->left = root;
        return n;
      }
      root->right = insertIntoMaxTree(root->right, val);
      return root;
    }
    

Java version:

    
    
    public TreeNode insertIntoMaxTree(TreeNode root, int val) {
      if (root == null || val > root.val) {
        TreeNode n = new TreeNode(val);
        n.left = root;
        return n;
      }
      root.right = insertIntoMaxTree(root.right, val);
      return root;
    }
    



