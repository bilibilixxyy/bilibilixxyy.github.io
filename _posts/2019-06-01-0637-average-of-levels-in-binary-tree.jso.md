---
layout: post
title: 637. Average of Levels in Binary Tree
---
### Question
Given a non-empty binary tree, return the average value of the nodes on each
level in the form of an array.

 **Example 1:**  

    
    
     **Input:**
        3
       / \
      9  20
        /  \
       15   7
    **Output:** [3, 14.5, 11]
    **Explanation:**
    The average value of nodes on level 0 is 3,  on level 1 is 14.5, and on level 2 is 11. Hence return [3, 14.5, 11].
    

**Note:**  

  1. The range of node's value is in the range of 32-bit signed integer.

### Solution 1
Classic bfs problem. At each level, compute the average since you already know
the size of the level.

    
    
    public List<Double> averageOfLevels(TreeNode root) {
        List<Double> result = new ArrayList<>();
        Queue<TreeNode> q = new LinkedList<>();
        
        if(root == null) return result;
        q.add(root);
        while(!q.isEmpty()) {
            int n = q.size();
            double sum = 0.0;
            for(int i = 0; i < n; i++) {
                TreeNode node = q.poll();
                sum += node.val;
                if(node.left != null) q.offer(node.left);
                if(node.right != null) q.offer(node.right);
            }
            result.add(sum / n);
        }
        return result;
    }


### Solution 2
BFS:

    
    
        public List<Double> averageOfLevels(TreeNode root) {
            List<Double> list = new LinkedList<>();
            Queue<TreeNode> queue = new LinkedList<>();
            queue.offer(root);
            while (!queue.isEmpty()) {
                int count = queue.size();
                double sum = 0;
                for (int i = 0; i < count; i++) {
                    TreeNode cur = queue.poll();
                    sum += cur.val;
                    if (cur.left != null) queue.offer(cur.left);
                    if (cur.right != null) queue.offer(cur.right);
                }
                list.add(sum / count);
            }
            return list;
        }
    

DFS: a new node class make it more readable and professional

    
    
        class Node {
            double sum;
            int count;
            Node (double d, int c) {
                sum = d;
                count = c;
            }
        }
        public List<Double> averageOfLevels(TreeNode root) {
            List<Node> temp = new ArrayList<>();
            helper(root, temp, 0);
            List<Double> result = new LinkedList<>();
            for (int i = 0; i < temp.size(); i++) {
                result.add(temp.get(i).sum / temp.get(i).count);
            }
            return result;
        }
        public void helper(TreeNode root, List<Node> temp, int level) {
            if (root == null) return;
            if (level == temp.size()) {
                Node node = new Node((double)root.val, 1);
                temp.add(node);
            } else {
                temp.get(level).sum += root.val;
                temp.get(level).count++;
            }
            helper(root.left, temp, level + 1);
            helper(root.right, temp, level + 1);
        }
    


### Solution 3
Let's visit every node of the tree once, keeping track of what depth we are
on. We can do this with a simple DFS.

When we visit a node, info[depth] will be a two element list, keeping track of
the sum of the nodes we have seen at this depth, and the number of nodes we
have seen. This is necessary and sufficient to be able to compute the average
value at this depth.

At the end of our traversal, we can simply read off the answer.

    
    
    def averageOfLevels(self, root):
        info = []
        def dfs(node, depth = 0):
            if node:
                if len(info) <= depth:
                    info.append([0, 0])
                info[depth][0] += node.val
                info[depth][1] += 1
                dfs(node.left, depth + 1)
                dfs(node.right, depth + 1)
        dfs(root)
    
        return [s/float(c) for s, c in info]
    



