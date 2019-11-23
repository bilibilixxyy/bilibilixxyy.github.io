---
layout: post
title: 998. Check Completeness of a Binary Tree
---
### Question
Given a binary tree, determine if it is a _complete binary tree_.

 _ **Definition of a complete binary tree
from[Wikipedia](http://en.wikipedia.org/wiki/Binary_tree#Types_of_binary_trees):**_  
In a complete binary tree every level, except possibly the last, is completely
filled, and all nodes in the last level are as far left as possible. It can
have between 1 and 2h nodes inclusive at the last level h.



 **Example 1:**

 **![](https://assets.leetcode.com/uploads/2018/12/15/complete-binary-
tree-1.png)**

    
    
     **Input:** [1,2,3,4,5,6]
    **Output:** true
    **Explanation:** Every level before the last is full (ie. levels with node-values {1} and {2, 3}), and all nodes in the last level ({4, 5, 6}) are as far left as possible.
    

**Example 2:**

**![](https://assets.leetcode.com/uploads/2018/12/15/complete-binary-
tree-2.png)**

    
    
    **Input:** [1,2,3,4,5,null,7]
    **Output:** false
    **Explanation:** The node with value 7 isn't as far left as possible.
    



 **Note:**

  1. The tree will have between 1 and 100 nodes.

### Solution 1
Use BFS to do a level order traversal,  
add childrens to the bfs queue,  
until we met the first empty node.

For a complete binary tree,  
there should not be any node after we met an empty one.

 **Java:**

    
    
        public  boolean isCompleteTree(TreeNode root) {
            Queue<TreeNode> bfs = new LinkedList<TreeNode>();
            bfs.offer(root);
            while (bfs.peek() != null) {
                TreeNode node = bfs.poll();
                bfs.offer(node.left);
                bfs.offer(node.right);
            }
            while (!bfs.isEmpty() && bfs.peek() == null)
                bfs.poll();
            return bfs.isEmpty();
        }
    

**C++:**

    
    
        bool isCompleteTree(TreeNode* root) {
            vector<TreeNode*> bfs;
            bfs.push_back(root);
            int  i = 0;
            while (i < bfs.size() && bfs[i]) {
                bfs.push_back(bfs[i]->left);
                bfs.push_back(bfs[i]->right);
                i++;
            }
            while (i < bfs.size() && !bfs[i])
                i++;
            return i == bfs.size();
        }
    

**Python:**

    
    
        def isCompleteTree(self, root):
            bfs = [root]
             i = 0
            while bfs[i]:
                bfs.append(bfs[i].left)
                bfs.append(bfs[i].right)
                i += 1
            return not any(bfs[i:])
    

Also you may want to return earlier.  
We can stop the first while loop when met the first null child.  
From then on there should not be any more child.  
This optimisation help reduce half of operations.

    
    
        public boolean isCompleteTree(TreeNode root) {
            Queue<TreeNode> bfs = new LinkedList<TreeNode>();
            bfs.offer(root);
            while (true) {
                TreeNode node = bfs.poll();
                if (node.left == null) {
                    if (node.right != null)
                        return false;
                    break;
                }
                bfs.offer(node.left);
                if (node.right == null) break;
                bfs.offer(node.right);
            }
            while (!bfs.isEmpty()) {
                TreeNode node = bfs.poll();
                if (node.left != null || node.right != null)
                    return false;
            }
            return true;
        }
    


### Solution 2
When level-order traversal in a complete tree, after the last node, all nodes
in the queue should be null.  
Otherwise, the tree is not complete.

    
    
        public boolean isCompleteTree(TreeNode root) {
            boolean end = false;
            Queue<TreeNode> queue = new LinkedList<>();
            queue.add(root);
            while(!queue.isEmpty()) {
                TreeNode cur = queue.poll();
                if(cur == null) end = true;
                else{
                    if(end) return false;
                    queue.add(cur.left);
                    queue.add(cur.right);
                }
            }
            return true;
        }
    

Time Complexity: O(N), where N is the number of nodes.  
Space Complexity: O(N)


### Solution 3
We traverse the tree and track the current height (`h`). The very first leaf
is the leftmost node, so it sets the target height (`t_h`). If any further
leaf height is different, except for `t_h - 1`, then the tree is not complete.
If the leaf height equals `t_h - 1`, remaining leaf heights should be equal to
`t_h - 1` for the tree to be complete.

    
    
    int t_h = 0, end = 0;
    bool isCompleteTree(TreeNode* r, int h = 0) {
      if (r == nullptr) {
        if (t_h == 0) t_h = h;
        if (h == t_h - 1) end = true;
        if ((h == t_h && !end) || (h == t_h - 1 && end)) return true;
      }
      return r != nullptr && isCompleteTree(r->left, h + 1) && isCompleteTree(r->right, h + 1);
    }
    



