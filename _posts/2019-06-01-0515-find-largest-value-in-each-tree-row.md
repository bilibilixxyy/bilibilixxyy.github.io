---
layout: post
title: 515. Find Largest Value in Each Tree Row
---
### Question
You need to find the largest value in each row of a binary tree.

 **Example:**  

    
    
     **Input:** 
    
              1
             / \
            3   2
           / \   \  
          5   3   9 
    
    **Output:** [1, 3, 9]
    

### Solution 1
 **Just a simple pre-order traverse idea. Use depth to expand result list size
and put the max value in the appropriate position.**

    
    
     public class Solution {
        public List<Integer> largestValues(TreeNode root) {
            List<Integer> res = new ArrayList<Integer>();
            helper(root, res, 0);
            return res;
        }
        private void helper(TreeNode root, List<Integer> res, int d){
            if(root == null){
                return;
            }
           //expand list size
            if(d == res.size()){
                res.add(root.val);
            }
            else{
            //or set value
                res.set(d, Math.max(res.get(d), root.val));
            }
            helper(root.left, res, d+1);
            helper(root.right, res, d+1);
        }
    }
    


### Solution 2
    
    
    def findValueMostElement(self, root):
        maxes = []
        row = [root]
        while any(row):
            maxes.append(max(node.val for node in row))
            row = [kid for node in row for kid in (node.left, node.right) if kid]
        return maxes


### Solution 3
    
    
        public int[] findValueMostElement(TreeNode root) {
            Queue<TreeNode> queue = new LinkedList<TreeNode>();
            List<Integer> res = new ArrayList<Integer>();
            queue.add(root);
            int queueSize = root == null ? 0 : 1;
            while (queueSize > 0) {
                int largestElement = Integer.MIN_VALUE;
                for (int i=0;i<queueSize;i++) {
                    TreeNode cur = queue.poll();
                    largestElement = Math.max(cur.val, largestElement);
                    if (cur.left != null) queue.add(cur.left);
                    if (cur.right != null) queue.add(cur.right);
                }
                res.add(largestElement);
                queueSize = queue.size();
            }
            int[] resArray = new int[res.size()];
            for (int i=0;i<res.size();i++) resArray[i] = res.get(i);
            return resArray;
        }
    



