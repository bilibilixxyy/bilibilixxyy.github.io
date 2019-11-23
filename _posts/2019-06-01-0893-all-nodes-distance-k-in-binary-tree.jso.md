---
layout: post
title: 893. All Nodes Distance K in Binary Tree
---
### Question
We are given a binary tree (with root node `root`), a `target` node, and an
integer value `K`.

Return a list of the values of all nodes that have a distance `K` from the
`target` node.  The answer can be returned in any order.



 **Example 1:**

    
    
     **Input:** root = [3,5,1,6,2,0,8,null,null,7,4], target = 5, K = 2
    
    **Output:** [7,4,1]
    
    **Explanation:**
    The nodes that are a distance 2 from the target node (with value 5)
    have values 7, 4, and 1.
    
    ![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/06/28/sketch0.png)
    
    Note that the inputs "root" and "target" are actually TreeNodes.
    The descriptions of the inputs above are just serializations of these objects.
    



 **Note:**

  1. The given tree is non-empty.
  2. Each node in the tree has unique values `0 <= node.val <= 500`.
  3. The `target` node is a node in the tree.
  4. `0 <= K <= 1000`.

### Solution 1
    
    
    //Method 1: use HashMap
    //1. build a undirected graph using treenodes as vertices, and the parent-child relation as edges
    //2. do BFS with source vertice (target) to find all vertices with distance K to it.
    class Solution {
        Map<TreeNode, List<TreeNode>> map = new HashMap();
    //here can also use Map<TreeNode, TreeNode> to only store the child - parent mapping, since parent-child mapping is inherent in the tree structure
        
        public List<Integer> distanceK(TreeNode root, TreeNode target, int K) {
             List<Integer> res = new ArrayList<Integer> ();
            if (root == null || K < 0) return res;
            buildMap(root, null); 
            if (!map.containsKey(target)) return res;
            Set<TreeNode> visited = new HashSet<TreeNode>();
            Queue<TreeNode> q = new LinkedList<TreeNode>();
            q.add(target);
            visited.add(target);
            while (!q.isEmpty()) {
                int size = q.size();
                if (K == 0) {
                    for (int i = 0; i < size ; i++) res.add(q.poll().val);
                    return res;
                }
                for (int i = 0; i < size; i++) {
                    TreeNode node = q.poll();
                    for (TreeNode next : map.get(node)) {
                        if (visited.contains(next)) continue;
                        visited.add(next);
                        q.add(next);
                    }
                }
                K--;
            }
            return res;
        }
        
        private void buildMap(TreeNode node, TreeNode parent) {
            if (node == null) return;
            if (!map.containsKey(node)) {
                map.put(node, new ArrayList<TreeNode>());
                if (parent != null)  { map.get(node).add(parent); map.get(parent).add(node) ; }
                buildMap(node.left, node);
                buildMap(node.right, node);
            }
        }    
    }
    
    //Method 2: No HashMap
    //kind of like clone the tree, in the meanwhile add a parent link to the node
    class Solution {
        private GNode targetGNode;
        
        private class GNode {
            TreeNode node;
            GNode parent, left, right;
            GNode (TreeNode node) {
                this.node = node;
            }
        }           
        
        public List<Integer> distanceK(TreeNode root, TreeNode target, int K) {
            List<Integer> res = new ArrayList<Integer> ();
            if (root == null || K < 0) return res;
            cloneGraph(root, null, target);
            if (targetGNode == null) return res;
            Set<GNode> visited = new HashSet<GNode>();
            Queue<GNode> q = new LinkedList<GNode>();
            q.add(targetGNode);
            visited.add(targetGNode);
            while (!q.isEmpty()) {
                int size = q.size();
                if (K == 0) {
                    for (int i = 0; i < size ; i++) res.add(q.poll().node.val);
                    return res;
                }
                for (int i = 0; i < size; i++) {
                    GNode gNode = q.poll();
                    if (gNode.left != null && !visited.contains(gNode.left)) { visited.add(gNode.left); q.add(gNode.left); }
                    if (gNode.right != null && !visited.contains(gNode.right)) { visited.add(gNode.right); q.add(gNode.right); }
                    if (gNode.parent != null && !visited.contains(gNode.parent)) { visited.add(gNode.parent); q.add(gNode.parent); }
                }
                K--;
            }
            return res;
        }
        
        private GNode cloneGraph(TreeNode node, GNode parent, TreeNode target) {
            if (node == null) return null;
            GNode gNode = new GNode(node);
            if (node == target) targetGNode = gNode;
            gNode.parent = parent;
            gNode.left = cloneGraph(node.left, gNode, target);
            gNode.right = cloneGraph(node.right, gNode, target);
            return gNode;
        }
    }
    


### Solution 2
A recursive dfs funciton `connect` help to build up a map `conn`.  
The key of map is node's val and the value of map is node's connected nodes'
vals.  
Then we do N times bfs search loop to find all nodes of distance `K`

    
    
        def distanceK(self, root, target, K):
            conn = collections.defaultdict(list)
            def connect(parent, child):
                if parent and child:
                    conn[parent.val].append(child.val)
                    conn[child.val].append(parent.val)
                if child.left: connect(child, child.left)
                if child.right: connect(child, child.right)
            connect(None, root)
            bfs = [target.val]
            seen = set(bfs)
            for i in xrange(K):
                bfs = [y for x in bfs for y in conn[x] if y not in seen]
                seen |= set(bfs)
            return bfs
    


### Solution 3
As we know, if the distance from a node to target node is `k`, the distance
from its child to the target node is `k + 1` unless the child node is closer
to the target node which means the target node is in it's subtree.

To avoid this situation, we need to travel the tree first to find the path
from `root` to `target`, to:

  * store the value of distance in hashamp from the `all nodes in that path` to `target`

Then we can easily use dfs to travel the whole tree. Every time when we meet a
treenode which has already stored in map, use the stored value in hashmap
**instead of** the value from its parent node.

    
    
     class Solution {
        
        Map<TreeNode, Integer> map = new HashMap<>();
            
        public List<Integer> distanceK(TreeNode root, TreeNode target, int K) {
            List<Integer> res = new LinkedList<>();
            find(root, target);
            dfs(root, target, K, map.get(root), res);
            return res;
        }
        
        // find target node first and store the distance in that path that we could use it later directly
        private int find(TreeNode root, TreeNode target) {
            if (root == null) return -1;
            if (root == target) {
                map.put(root, 0);
                return 0;
            }
            int left = find(root.left, target);
            if (left >= 0) {
                map.put(root, left + 1);
                return left + 1;
            }
    		int right = find(root.right, target);
    		if (right >= 0) {
                map.put(root, right + 1);
                return right + 1;
            }
            return -1;
        }
        
        private void dfs(TreeNode root, TreeNode target, int K, int length, List<Integer> res) {
            if (root == null) return;
            if (map.containsKey(root)) length = map.get(root);
            if (length == K) res.add(root.val);
            dfs(root.left, target, K, length + 1, res);
            dfs(root.right, target, K, length + 1, res);
        }
    }
    



