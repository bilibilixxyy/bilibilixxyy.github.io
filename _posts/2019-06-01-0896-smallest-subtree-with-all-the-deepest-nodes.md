---
layout: post
title: 896. Smallest Subtree with all the Deepest Nodes
---
### Question
Given a binary tree rooted at `root`, the _depth_ of each node is the shortest
distance to the root.

A node is _deepest_ if it has the largest depth possible among  any node in
the _entire tree_.

The subtree of a node is that node, plus the set of all descendants of that
node.

Return the node with the largest depth such that it contains all the deepest
nodes in its subtree.



 **Example 1:**

    
    
     **Input:** [3,5,1,6,2,0,8,null,null,7,4]
    **Output:** [2,7,4]
    **Explanation:**
    ![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/01/sketch1.png)
    
    We return the node with value 2, colored in yellow in the diagram.
    The nodes colored in blue are the deepest nodes of the tree.
    The input "[3, 5, 1, 6, 2, 0, 8, null, null, 7, 4]" is a serialization of the given tree.
    The output "[2, 7, 4]" is a serialization of the subtree rooted at the node with value 2.
    Both the input and output have TreeNode type.
    



 **Note:**

  * The number of nodes in the tree will be between 1 and 500.
  * The values of each node are unique.

### Solution 1
Write a sub function `deep(TreeNode root)`.  
Return a `pair(int depth, TreeNode subtreeWithAllDeepest)`

In sub function `deep(TreeNode root)`:

if root == null, return pair(0, null)  
left = deep(root.left)  
right = deep(root.left)

if left depth == right depth,  
deepest nodes both in the left and right subtree,  
return pair (left.depth + 1, root)

if left depth > right depth,  
deepest nodes only in the left subtree,  
return pair (left.depth + 1, left subtree)

if left depth < right depth,  
deepest nodes only in the right subtree,  
return pair (right.depth + 1, right subtree)

 **C++:**

    
    
        TreeNode* subtreeWithAllDeepest(TreeNode* root) {
             return deep(root).second;
        }
    
        pair<int, TreeNode*> deep(TreeNode* root) {
            if (!root) return {0, NULL};
            pair<int, TreeNode*> l = deep(root->left), r = deep(root->right);
    
            int d1 = l.first, d2 = r.first;
            return {max(d1, d2) + 1, d1 == d2 ? root : d1 > d2 ? l.second : r.second};
        }
    

**Java:**

    
    
         public TreeNode subtreeWithAllDeepest(TreeNode root) {
            return deep(root).getValue();
        }
    
        public Pair<Integer, TreeNode> deep(TreeNode root) {
            if (root == null) return new Pair(0, null);
            Pair<Integer, TreeNode> l = deep(root.left), r = deep(root.right);
    
            int d1 = l.getKey(), d2 = r.getKey();
            return new Pair(Math.max(d1, d2) + 1, d1 == d2 ? root : d1 > d2 ? l.getValue() : r.getValue());
        }
    

**Python:**

    
    
        def subtreeWithAllDeepest(self, root):
            def deep(root):
                 if not root: return 0, None
                l, r = deep(root.left), deep(root.right)
                if l[0] > r[0]: return l[0] + 1, l[1]
                elif l[0] < r[0]: return r[0] + 1, r[1]
                else: return l[0] + 1, root
            return deep(root)[1]
    


### Solution 2
Problem definition can be rephrased or additional sample test cases added to
make the problem statement clear.


### Solution 3
    
    
    	public int depth(TreeNode root){
    		if(root == null ) return 0;
    		return Math.max(depth(root.left),depth(root.right))+1;
    	}
    	public TreeNode subtreeWithAllDeepest(TreeNode root) {
    			if( root == null ) return null;
    			int left =  depth(root.left);
    			int right = depth(root.right);
    			if( left  == right ) return root;
    			if( left > right ) return subtreeWithAllDeepest(root.left);
    			return subtreeWithAllDeepest(root.right);
    	}
    

The time complexity of above code is O(N^2) since a binary tree can degenerate
to a linked list, the worst complexity to calculate depth is O(N) and so the
overall time complexity is O(N^2). Here is the memoized version:

Time complexity: O(N)

    
    
    public int depth(TreeNode root,HashMap<TreeNode,Integer> map){
            if(root == null ) return 0;
            if( map.containsKey(root) ) return map.get(root); 
            int max = Math.max(depth(root.left,map),depth(root.right,map))+1;
            map.put(root,max);   
            return max;
       }
       public TreeNode dfs(TreeNode root, HashMap<TreeNode,Integer> map){
           int left =  depth(root.left,map);
           int right = depth(root.right,map);
           if( left  == right ) return root;
           if( left > right ) return dfs(root.left,map);
           return dfs(root.right,map);
       }
       public TreeNode subtreeWithAllDeepest(TreeNode root) {
           if( root == null ) return null;
           HashMap<TreeNode,Integer> map = new HashMap<>();
           depth(root,map);
           return dfs(root,map);
       }
    



