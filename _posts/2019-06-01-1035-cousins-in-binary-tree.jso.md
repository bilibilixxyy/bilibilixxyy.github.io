---
layout: post
title: 1035. Cousins in Binary Tree
---
### Question
In a binary tree, the root node is at depth `0`, and children of each depth
`k` node are at depth `k+1`.

Two nodes of a binary tree are _cousins_ if they have the same depth, but have
**different parents**.

We are given the `root` of a binary tree with unique values, and the values
`x` and `y` of two different nodes in the tree.

Return `true` if and only if the nodes corresponding to the values `x` and `y`
are cousins.



 **Example 1:  
![](https://assets.leetcode.com/uploads/2019/02/12/q1248-01.png)**

    
    
     **Input:** root = [1,2,3,4], x = 4, y = 3
    **Output:** false
    

**Example 2:  
![](https://assets.leetcode.com/uploads/2019/02/12/q1248-02.png)**

    
    
    **Input:** root = [1,2,3,null,4,null,5], x = 5, y = 4
    **Output:** true
    

**Example 3:**

**![](https://assets.leetcode.com/uploads/2019/02/13/q1248-03.png)**

    
    
    **Input:** root = [1,2,3,null,4], x = 2, y = 3
    **Output:** false



 **Note:**

  1. The number of nodes in the tree will be between `2` and `100`.
  2. Each node has a unique integer value from `1` to `100`.

### Solution 1
    
    
     public boolean isCousins(TreeNode root, int A, int B) {
        if (root == null) return false;
    	Queue<TreeNode> queue = new LinkedList<>();
    	queue.offer(root);
    	while (!queue.isEmpty()) {
    		int size = queue.size();
    		boolean isAexist = false;		
    		boolean isBexist = false;		
    		for (int i = 0; i < size; i++) {
    			TreeNode cur = queue.poll();
                if (cur.val == A) isAexist = true;
                if (cur.val == B) isBexist = true;
    			if (cur.left != null && cur.right != null) { 
    				if (cur.left.val == A && cur.right.val == B) { 
    					return false;
    				}
    				if (cur.left.val == B && cur.right.val == A) { 
    					return false;
    				}
    			}
    			if (cur.left != null) {
    				queue.offer(cur.left);
    			}
    			if (cur.right != null) {
    				queue.offer(cur.right);
    			}
    		}
    		if (isAexist && isBexist)  return true;
    	}
    	return false;
    }


### Solution 2
    
    
    //dfs
    class Solution {
        TreeNode xParent = null;
        TreeNode yParent = null;
        int xDepth = -1, yDepth = -1;
        
        public boolean isCousins(TreeNode root, int x, int y) {
            getDepthAndParent(root, x, y, 0, null);
            return xDepth == yDepth && xParent != yParent? true: false;
        }
        //get both the depth and parent for x and y
        public void getDepthAndParent(TreeNode root, int x, int y, int depth, TreeNode parent){
            if(root == null){
                return;
            }
            if(root.val == x){
                xParent = parent;
                xDepth = depth;
            }else if(root.val == y){
                yParent = parent;
                yDepth = depth;
            }       
            getDepthAndParent(root.left, x, y, depth + 1, root);
            getDepthAndParent(root.right, x, y, depth + 1, root);
        }
    }
    


### Solution 3
The level-order traversal is the most time-efficient solution for this problem
since we only go as deep as the first potential cousin. The memory complexity
is `O(n/2)` to accommodate the longest level, vs. `O(h)` for recursive
solutions, where `h` is the height of the tree (could be `n` in the worst
case).

We use queue `q` to iterate through the current level nodes and populate their
children into `q1`. We also insert `nullptr` into `q1` after inserting each
node's children (to separate siblings froum cousins).

If we find a node with value `x` or `y`, we have one potential `cousin`. If we
find another potential cousin, we return true if they are not `siblings`
(`nullptr` sets `siblings` to false). If we finished the level with just one
potential `cousin`, we stop and return false.

    
    
    bool isCousins(TreeNode* root, int x, int y, bool siblings = false, bool cousin = false) {
      queue<TreeNode*> q, q1;
      q.push(root);
      while (!q.empty() && !cousin) {
        while (!q.empty()) {
          auto n = q.front();
          q.pop();
          if (n == nullptr) siblings = false;
          else {
            if (n->val == x || n->val == y) {
              if (!cousin) cousin = siblings = true;
              else return !siblings;
            }
            q1.push(n->left), q1.push(n->right), q1.push(nullptr);
          }
        }
        swap(q, q1);
      }
      return false;
    }
    



