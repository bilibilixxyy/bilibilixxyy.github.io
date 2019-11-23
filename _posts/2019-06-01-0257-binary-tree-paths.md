---
layout: post
title: 257. Binary Tree Paths
---
### Question
Given a binary tree, return all root-to-leaf paths.

 **Note:**  A leaf is a node with no children.

 **Example:**

    
    
     **Input:**
    
       1
     /   \
    2     3
     \
      5
    
    **Output:** ["1- >2->5", "1->3"]
    
    **Explanation:** All root-to-leaf paths are: 1- >2->5, 1->3
    

### Solution 1
    
    
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> answer = new ArrayList<String>();
        if (root != null) searchBT(root, "", answer);
        return answer;
    }
    private void searchBT(TreeNode root, String path, List<String> answer) {
        if (root.left == null && root.right == null) answer.add(path + root.val);
        if (root.left != null) searchBT(root.left, path + root.val + "->", answer);
        if (root.right != null) searchBT(root.right, path + root.val + "->", answer);
    }


### Solution 2
    
    
    # dfs + stack
    def binaryTreePaths1(self, root):
        if not root:
            return []
        res, stack = [], [(root, "")]
        while stack:
            node, ls = stack.pop()
            if not node.left and not node.right:
                res.append(ls+str(node.val))
            if node.right:
                stack.append((node.right, ls+str(node.val)+"->"))
            if node.left:
                stack.append((node.left, ls+str(node.val)+"->"))
        return res
        
    # bfs + queue
    def binaryTreePaths2(self, root):
        if not root:
            return []
        res, queue = [], collections.deque([(root, "")])
        while queue:
            node, ls = queue.popleft()
            if not node.left and not node.right:
                res.append(ls+str(node.val))
            if node.left:
                queue.append((node.left, ls+str(node.val)+"->"))
            if node.right:
                queue.append((node.right, ls+str(node.val)+"->"))
        return res
        
    # dfs recursively
    def binaryTreePaths(self, root):
        if not root:
            return []
        res = []
        self.dfs(root, "", res)
        return res
    
    def dfs(self, root, ls, res):
        if not root.left and not root.right:
            res.append(ls+str(root.val))
        if root.left:
            self.dfs(root.left, ls+str(root.val)+"->", res)
        if root.right:
            self.dfs(root.right, ls+str(root.val)+"->", res)


### Solution 3
Lot of recursive solutions on this forum involves creating a helper recursive
function with added parameters. The added parameter which usually is of the
type List , carries the supplementary path information. However, the approach
below doesn't use such a helper function.

    
    
    public List<String> binaryTreePaths(TreeNode root) {
            
            List<String> paths = new LinkedList<>();
    
            if(root == null) return paths;
            
            if(root.left == null && root.right == null){
                paths.add(root.val+"");
                return paths;
            }
    
             for (String path : binaryTreePaths(root.left)) {
                 paths.add(root.val + "->" + path);
             }
    
             for (String path : binaryTreePaths(root.right)) {
                 paths.add(root.val + "->" + path);
             }
    
             return paths;
            
        }



