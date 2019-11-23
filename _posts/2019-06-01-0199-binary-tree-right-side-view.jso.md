---
layout: post
title: 199. Binary Tree Right Side View
---
### Question
Given a binary tree, imagine yourself standing on the _right_ side of it,
return the values of the nodes you can see ordered from top to bottom.

 **Example:**

    
    
     **Input:**  [1,2,3,null,5,null,4]
    **Output:**  [1, 3, 4]
    **Explanation:**
       1             <---
     /   \
    2     3         <---
     \     \
      5     4       <---
    

### Solution 1
The core idea of this algorithm:

1.Each depth of the tree only select one node.  
2\. View depth is current size of result list.

Here is the code:

    
    
    public class Solution {
        public List<Integer> rightSideView(TreeNode root) {
            List<Integer> result = new ArrayList<Integer>();
            rightView(root, result, 0);
            return result;
        }
        
        public void rightView(TreeNode curr, List<Integer> result, int currDepth){
            if(curr == null){
                return;
            }
            if(currDepth == result.size()){
                result.add(curr.val);
            }
            
            rightView(curr.right, result, currDepth + 1);
            rightView(curr.left, result, currDepth + 1);
            
        }
    }


### Solution 2
    
    
    class Solution {
    public:
        void recursion(TreeNode *root, int level, vector<int> &res)
        {
            if(root==NULL) return ;
            if(res.size()<level) res.push_back(root->val);
            recursion(root->right, level+1, res);
            recursion(root->left, level+1, res);
        }
        
        vector<int> rightSideView(TreeNode *root) {
            vector<int> res;
            recursion(root, 1, res);
            return res;
        }
    };


### Solution 3
    
    
    public class Solution {
        public List<Integer> rightSideView(TreeNode root) {
            // reverse level traversal
            List<Integer> result = new ArrayList();
            Queue<TreeNode> queue = new LinkedList();
            if (root == null) return result;
            
            queue.offer(root);
            while (queue.size() != 0) {
                int size = queue.size();
                for (int i=0; i<size; i++) {
                    TreeNode cur = queue.poll();
                    if (i == 0) result.add(cur.val);
                    if (cur.right != null) queue.offer(cur.right);
                    if (cur.left != null) queue.offer(cur.left);
                }
                
            }
            return result;
        }
    }



