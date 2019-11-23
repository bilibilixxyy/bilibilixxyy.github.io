---
layout: post
title: 1008. Binary Tree Cameras
---
### Question
Given a binary tree, we install cameras on the nodes of the tree.

Each camera at a node can monitor **its parent, itself, and its immediate
children**.

Calculate the minimum number of cameras needed to monitor all nodes of the
tree.



 **Example 1:**

![](https://assets.leetcode.com/uploads/2018/12/29/bst_cameras_01.png)

    
    
     **Input:** [0,0,null,0,0]
    **Output:** 1
    **Explanation:** One camera is enough to monitor all nodes if placed as shown.
    

**Example 2:**

![](https://assets.leetcode.com/uploads/2018/12/29/bst_cameras_02.png)

    
    
    **Input:** [0,0,null,0,null,0,null,null,0]
    **Output:** 2
    **Explanation:** At least two cameras are needed to monitor all nodes of the tree. The above image shows one of the valid configurations of camera placement.
    

  
**Note:**

  1. The number of nodes in the given tree will be in the range `[1, 1000]`.
  2. **Every** node has value 0.

### Solution 1
##  **Intuition** :

Consider a node in the tree.  
It can be covered by its parent, itself, its two children.  
Four options.

Consider the root of the tree.  
It can be covered by left child, or right child, or itself.  
Three options.

Consider one leaf of the tree.  
It can be covered by its parent or by itself.  
Two options.

If we set a camera at the leaf, the camera can cover the leaf and its parent.  
If we set a camera at its parent, the camera can cover the leaf, its parent
and its sibling.

We can see that the second plan is always better than the first.  
Now we have only one option, set up camera to all leaves' parent.

Here is our greedy solution:

  1. Set cameras on all leaves' parents, thenremove all covered nodes.
  2. Repeat step 1 until all nodes are covered.

##  **Explanation** :

Apply a recusion function `dfs`.  
Return `0` if it's a leaf.  
Return `1` if it's a parent of a leaf, with a camera on this node.  
Return `2` if it's coverd, without a camera on this node.

For each node,  
if it has a child, which is leaf (node 0), then it needs camera.  
if it has a child, which is the parent of a leaf (node 1), then it's covered.

If it needs camera, then `res++` and we return `1`.  
If it's covered, we return `2`.  
Otherwise, we return `0`.

  

 **Java:**

    
    
         int res = 0;
        public int minCameraCover(TreeNode root) {
            return (dfs(root) < 1 ? 1 : 0) + res;
        }
    
        public int dfs(TreeNode root) {
            if (root == null) return 2;
            int left = dfs(root.left), right = dfs(root.right);
            if (left == 0 || right == 0) {
                res++;
                return 1;
            }
            return left == 1 || right == 1 ? 2 : 0;
        }
    

**C++:**

    
    
         int res = 0;
        int minCameraCover(TreeNode* root) {
            return (dfs(root) < 1 ? 1 : 0) + res;
        }
    
        int dfs(TreeNode* root) {
            if (!root) return 2;
            int left = dfs(root->left), right = dfs(root->right);
            if (left == 0 || right == 0) {
                res++;
                return 1;
            }
            return left == 1 || right == 1 ? 2 : 0;
        }
    

**Python:**

    
    
        def minCameraCover(self, root):
            self.res =  0
            def dfs(root):
                if not root: return 2
                l, r = dfs(root.left), dfs(root.right)
                if l == 0 or r == 0:
                    self.res += 1
                    return 1
                return 2 if l == 1 or r == 1 else 0
            return (dfs(root) == 0) + self.res
    


### Solution 2
We use greedy algorithem here. We put camera at as higher level (closer to
root) as possible. We use post-order DFS here. This would be O(n) time and
O(h) space, where h is height of the tree.  
The return value of DFS() has following meanings. 0: there is no camera at
this node, and it's not monitored by camera at either of its children, which
means neither of child nodes has camera. 1: there is no camera at this node;
however, this node is monitored by at least 1 of its children, which means at
least 1 of its children has camera. 2: there is a camera at this node.

    
    
    class Solution {
    public:
        int minCameraCover(TreeNode* root) {
            int sum=0;
            if(dfs(root,sum)==0)   sum++;// if root is not monitored, we place an additional camera here
            return sum;
        }
        
        int dfs(TreeNode * tr, int& sum){
            if(!tr) return 1;
            int l=dfs(tr->left,sum), r=dfs(tr->right,sum);
            if(l==0||r==0){// if at least 1 child is not monitored, we need to place a camera at current node 
                sum++;
                return 2;
            }else if(l==2||r==2){// if at least 1 child has camera, the current node is monitored. Thus, we don't need to place a camera here 
                return 1;
            }else{// if both children are monitored but have no camera, we don't need to place a camera here. We place the camera at its parent node at the higher level. 
                return 0;
            }
            return -1;// this return statement won't be triggered
        }
    };
    


### Solution 3
Time complexity is O(n), space complexity is O(logn) considering system stack
space.

    
    
    class Solution {
        private int NOT_MONITORED = 0;
        private int MONITORED_NOCAM = 1;
        private int MONITORED_WITHCAM = 2;
        private int cameras = 0;
    	
        public int minCameraCover(TreeNode root) {
            if (root == null) return 0;
            int top = dfs(root);
            return cameras + (top == NOT_MONITORED ? 1 : 0);
        }
        
        private int dfs(TreeNode root) {
            if (root == null) return MONITORED_NOCAM;
            int left = dfs(root.left);
            int right = dfs(root.right);
            if (left == MONITORED_NOCAM && right == MONITORED_NOCAM) {
                return NOT_MONITORED;
            } else if (left == NOT_MONITORED || right == NOT_MONITORED) {
                cameras++;
                return MONITORED_WITHCAM;
            } else {
                return MONITORED_NOCAM;
            }
        }
    }
    



