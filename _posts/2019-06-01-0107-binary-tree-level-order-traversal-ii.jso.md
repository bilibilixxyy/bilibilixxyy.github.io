---
layout: post
title: 107. Binary Tree Level Order Traversal II
---
### Question
Given a binary tree, return the _bottom-up level order_ traversal of its
nodes' values. (ie, from left to right, level by level from leaf to root).

For example:  
Given binary tree `[3,9,20,null,null,15,7]`,  

    
    
        3
       / \
      9  20
        /  \
       15   7
    

return its bottom-up level order traversal as:  

    
    
    [
      [15,7],
      [9,20],
      [3]
    ]
    

### Solution 1
DFS solution:

    
    
    public class Solution {
        public List<List<Integer>> levelOrderBottom(TreeNode root) {
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
                wrapList.add(0, subList);
            }
            return wrapList;
        }
    }
    

BFS solution:

    
    
    public class Solution {
            public List<List<Integer>> levelOrderBottom(TreeNode root) {
                List<List<Integer>> wrapList = new LinkedList<List<Integer>>();
                levelMaker(wrapList, root, 0);
                return wrapList;
            }
            
            public void levelMaker(List<List<Integer>> list, TreeNode root, int level) {
                if(root == null) return;
                if(level >= list.size()) {
                    list.add(0, new LinkedList<Integer>());
                }
                levelMaker(list, root.left, level+1);
                levelMaker(list, root.right, level+1);
                list.get(list.size()-level-1).add(root.val);
            }
        }


### Solution 2
The way I see this problem is that it is EXACTLY the same as "Level-Order
Traversal I" except that we need to reverse the final container for output,
which is trivial. Is there a better idea that fits this problem specifically?

The attached is my current recursive solution. In each function call, we pass
in the current node and its level. If this level does not yet exist in the
output container, then we should add a new empty level. Then, we add the
current node to the end of the current level, and recursively call the
function passing the two children of the current node at the next level. This
algorithm is really a DFS, but it saves the level information for each node
and produces the same result as BFS would.

    
    
    vector<vector<int> > res;
    
    void DFS(TreeNode* root, int level)
    {
        if (root == NULL) return;
        if (level == res.size()) // The level does not exist in output
        {
            res.push_back(vector<int>()); // Create a new level
        }
        
        res[level].push_back(root->val); // Add the current value to its level
        DFS(root->left, level+1); // Go to the next level
        DFS(root->right,level+1);
    }
    
    vector<vector<int> > levelOrderBottom(TreeNode *root) {
        DFS(root, 0);
        return vector<vector<int> > (res.rbegin(), res.rend());
    }


### Solution 3
    
    
    # dfs recursively
    def levelOrderBottom1(self, root):
        res = []
        self.dfs(root, 0, res)
        return res
    
    def dfs(self, root, level, res):
        if root:
            if len(res) < level + 1:
                res.insert(0, [])
            res[-(level+1)].append(root.val)
            self.dfs(root.left, level+1, res)
            self.dfs(root.right, level+1, res)
            
    # dfs + stack
    def levelOrderBottom2(self, root):
        stack = [(root, 0)]
        res = []
        while stack:
            node, level = stack.pop()
            if node:
                if len(res) < level+1:
                    res.insert(0, [])
                res[-(level+1)].append(node.val)
                stack.append((node.right, level+1))
                stack.append((node.left, level+1))
        return res
     
    # bfs + queue   
    def levelOrderBottom(self, root):
        queue, res = collections.deque([(root, 0)]), []
        while queue:
            node, level = queue.popleft()
            if node:
                if len(res) < level+1:
                    res.insert(0, [])
                res[-(level+1)].append(node.val)
                queue.append((node.left, level+1))
                queue.append((node.right, level+1))
        return res



