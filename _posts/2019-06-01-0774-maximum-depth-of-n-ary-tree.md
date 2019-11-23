---
layout: post
title: 774. Maximum Depth of N-ary Tree
---
### Question
Given a n-ary tree, find its maximum depth.

The maximum depth is the number of nodes along the longest path from the root
node down to the farthest leaf node.

For example, given a `3-ary` tree:



![](https://assets.leetcode.com/uploads/2018/10/12/narytreeexample.png)



We should return its max depth, which is 3.



**Note:**

  1. The depth of the tree is at most `1000`.
  2. The total number of nodes is at most `5000`.

### Solution 1
    
    
    public int maxDepth(Node root) {
        if(root == null) return 0;
        
        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);
        
        int depth = 0;
        
        while(!queue.isEmpty())
        {
            int size = queue.size();
            
            for(int i = 0; i < size; i++)
            {
                Node current = queue.poll();
                for(Node child: current.children) queue.offer(child);
            }
            
            depth++;
        }
        
        return depth;
    }


### Solution 2
Recursive DFS:

    
    
    class Solution {
    public:
        int maxDepth(Node* root) {
            if (root == nullptr) return 0;
            int depth = 0;
            for (auto child : root->children) depth = max(depth, maxDepth(child));
            return 1 + depth;
        }
    };
    

BFS:

    
    
    class Solution {
    public:
        int maxDepth(Node* root) {
            if (root == nullptr) return 0;
            queue<Node*> q; q.push(root);
            int depth = 0;
            while (!q.empty()) {
                depth += 1;
                int breadth = q.size();
                for (int _ = 0; _ < breadth; ++_) {
                    auto node = q.front(); q.pop();
                    for (auto child : node->children) if (child) q.push(child);
                }
            }
            return depth;
        }
    };
    


### Solution 3
This question is same as the binary one.  
First, let's review the 104. Maximum Depth of Binary Tree

    
    
    /**
     * Definition for a binary tree node.
     * public class TreeNode {
     *     int val;
     *     TreeNode left;
     *     TreeNode right;
     *     TreeNode(int x) { val = x; }
     * }
     */
    class Solution {
        public int maxDepth(TreeNode root) {
            if (root == null) {
                return 0;
            }
            int left = maxDepth(root.left);
            int right = maxDepth(root.right);
            return Math.max(left, right) + 1;
        }
    }
    

For current node, the maximum depth of this node is Math.max(node.left.depth,
node.right.depth).  
So, what is the depth of node.left and node.right? You can put node.left as
node. The recursive method has been built.

Now, for this question, there are not only left and right, but there are
children. So, only change left, right as a children for loop.

    
    
    /*
    // Definition for a Node.
    class Node {
        public int val;
        public List<Node> children;
    
        public Node() {}
    
        public Node(int _val,List<Node> _children) {
            val = _val;
            children = _children;
        }
    };
    */
    class Solution {
        public int maxDepth(Node root) {
            if (root == null) {
                return 0;
            }
    
            int max = 0;
            for (Node child : root.children) { //replace left&right to for loop
                int value = maxDepth(child);
                
                if (value > max) {
                    max = value;
                }
            }
            return max +1;
        }
    }
    

Actually, this code also works for binary tree.



