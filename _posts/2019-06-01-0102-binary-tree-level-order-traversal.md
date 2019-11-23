---
layout: post
title: 102. Binary Tree Level Order Traversal
---
### Question
Given a binary tree, return the _level order_ traversal of its nodes' values.
(ie, from left to right, level by level).

For example:  
Given binary tree `[3,9,20,null,null,15,7]`,  

    
    
        3
       / \
      9  20
        /  \
       15   7
    

return its level order traversal as:  

    
    
    [
      [3],
      [9,20],
      [15,7]
    ]
    

### Solution 1
    
    
    public class Solution {
        public List<List<Integer>> levelOrder(TreeNode root) {
            Queue<TreeNode> queue = new LinkedList<TreeNode>();
            List<List<Integer>> wrapList = new LinkedList<List<Integer>>();
            
            if(root == null) return wrapList;
            
            queue.offer(root);
            while(!queue.isEmpty()){
                int levelNum = queue.size();
                List<Integer> subList = new LinkedList<Integer>();
                for(int i=0; i<levelNum; i++) {
                    if(queue.peek().left != null) queue.offer(queue.peek().left);
                    if(queue.peek().right != null) queue.offer(queue.peek().right);
                    subList.add(queue.poll().val);
                }
                wrapList.add(subList);
            }
            return wrapList;
        }
    }


### Solution 2
Nothing special. Just wanna provide a different way from BFS.

    
    
    public List<List<Integer>> levelOrder(TreeNode root) {
            List<List<Integer>> res = new ArrayList<List<Integer>>();
            levelHelper(res, root, 0);
            return res;
        }
        
        public void levelHelper(List<List<Integer>> res, TreeNode root, int height) {
            if (root == null) return;
            if (height >= res.size()) {
                res.add(new LinkedList<Integer>());
            }
            res.get(height).add(root.val);
            levelHelper(res, root.left, height+1);
            levelHelper(res, root.right, height+1);
        }


### Solution 3
    
    
    vector<vector<int>> ret;
    
    void buildVector(TreeNode *root, int depth)
    {
        if(root == NULL) return;
        if(ret.size() == depth)
            ret.push_back(vector<int>());
        
        ret[depth].push_back(root->val);
        buildVector(root->left, depth + 1);
        buildVector(root->right, depth + 1);
    }
    
    vector<vector<int> > levelOrder(TreeNode *root) {
        buildVector(root, 0);
        return ret;
    }



