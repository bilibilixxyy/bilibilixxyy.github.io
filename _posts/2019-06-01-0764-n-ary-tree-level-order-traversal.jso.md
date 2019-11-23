---
layout: post
title: 764. N-ary Tree Level Order Traversal
---
### Question
Given an n-ary tree, return the level order traversal of its nodes' values.
(ie, from left to right, level by level).

For example, given a `3-ary` tree:



![](https://assets.leetcode.com/uploads/2018/10/12/narytreeexample.png)



We should return its level order traversal:

    
    
    [
         [1],
         [3,2,4],
         [5,6]
    ]
    



 **Note:**

  1. The depth of the tree is at most `1000`.
  2. The total number of nodes is at most `5000`.

### Solution 1
    
    
    class Solution(object):
        def levelOrder(self, root):
            q, ret = [root], []
            while any(q):
                ret.append([node.val for node in q])
                q = [child for node in q for child in node.children if child]
            return ret
    


### Solution 2
Beats 98.27%. It's pretty easy to do a further optimization, but as the title
suggests, it's for beginners.  
Before you rush into coding, it's highly recommended to finish the queue &
stack explore section, where you can review the basics of BFS traversal, so
that you are familiar with all techniques needed!

  * Here's the link:  
<https://leetcode.com/explore/learn/card/queue-stack/231/practical-
application-queue/1376/>

  * Another good source to understand this problem:  
<https://www.geeksforgeeks.org/level-order-tree-traversal/>

This algo can be applied to the following problems with some tweaks. Try them
out!

  * two slighly modified problems:  
<https://leetcode.com/problems/binary-tree-level-order-traversal/>  
<https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/>

  * Calculate the depth of a tree!  
<https://leetcode.com/problems/maximum-depth-of-binary-tree/>

 **Note** : It's definitely not the best solution, but please take it as the
minimum requirement before you come up with any other elegent solutions to
solve this problem.

**edited Aug 19 2018 : line-by-line comments for pure beginners.

    
    
    class Solution {
    public:
        vector<vector<int>> levelOrder(Node* root) 
    	{
            if (root == NULL) 
    			return vector<vector<int>>(); // We could also "return {};" here thanks to C++11. 
            vector<vector<int>> res; // Define a vector of vector for storing values of nodes. Data type: int
            queue<Node*> q; // Define the queue. Data type : pointers that point to nodes
            q.push(root); // Push the root node
            while (!q.empty()) // Whenever the queue is not empty
            {
                int size = q.size(); // Store the size of queue, which is the number of nodes in the current level
                vector<int> curLevel; // Store the result per level. 
                for (int i = 0; i < size; i++) // For each node of the current level
                {
                    Node* tmp = q.front(); // Get the first node from the queue
                    q.pop(); // Pop this node since we no longer need it.
                    curLevel.push_back(tmp -> val); // Store values of tmp nodes
                    for (auto n : tmp -> children) // Push every child node of the tmp node back to the queue. FIFO(first in first out)
                         q.push(n); 
                }
                res.push_back(curLevel); // Store the current level values to res.
            }
            return res; 
        }
    };
    


### Solution 3
    
    
    public List<List<Integer>> levelOrder(Node root) {
            List<List<Integer>> ret = new LinkedList<>();
            
            if (root == null) return ret;
            
            Queue<Node> queue = new LinkedList<>();
            
            queue.offer(root);
            
            while (!queue.isEmpty()) {
                List<Integer> curLevel = new LinkedList<>();
                int len = queue.size();
                for (int i = 0; i < len; i++) {
                    Node curr = queue.poll();
                    curLevel.add(curr.val);
                    for (Node c : curr.children)
                        queue.offer(c);
                }
                ret.add(curLevel);
            }
            
            return ret;
        }
    



