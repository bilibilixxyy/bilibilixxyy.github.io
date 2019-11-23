---
layout: post
title: 955. Complete Binary Tree Inserter
---
### Question
A _complete_ binary tree is a binary tree in which every level, except
possibly the last, is completely filled, and all nodes are as far left as
possible.

Write a data structure `CBTInserter` that is initialized with a complete
binary tree and supports the following operations:

  * `CBTInserter(TreeNode root)` initializes the data structure on a given tree with head node `root`;
  * `CBTInserter.insert(int v)` will insert a `TreeNode` into the tree with value `node.val = v` so that the tree remains complete, **and returns the value of the parent of the inserted`TreeNode`** ;
  * `CBTInserter.get_root()` will return the head node of the tree.



 **Example 1:**

    
    
     **Input:** inputs = ["CBTInserter","insert","get_root"], inputs = [[[1]],[2],[]]
    **Output:** [null,1,[1,2]]
    

**Example 2:**

    
    
    **Input:** inputs = ["CBTInserter","insert","insert","get_root"], inputs = [[[1,2,3,4,5,6]],[7],[8],[]]
    **Output:** [null,3,4,[1,2,3,4,5,6,7,8]]



 **Note:**

  1. The initial given tree is complete and contains between `1` and `1000` nodes.
  2. `CBTInserter.insert` is called at most `10000` times per test case.
  3. Every value of a given or inserted node is between `0` and `5000`.

### Solution 1
Store tree nodes to a list `self.tree` in bfs order.  
Node `tree[i]` has left child `tree[2 * i + 1]` and `tree[2 * i + 2]`

So when insert the `N`th node (0-indexed), we push it into the list.  
we can find its parent `tree[(N - 1) / 2]` directly.

 **C++:**

    
    
        vector<TreeNode*>  tree;
        CBTInserter(TreeNode* root) {
            tree.push_back(root);
            for(int i = 0; i < tree.size();++i) {
                if (tree[i]->left) tree.push_back(tree[i]->left);
                if (tree[i]->right) tree.push_back(tree[i]->right);
            }
        }
    
        int insert(int v) {
            int N = tree.size();
            TreeNode* node = new TreeNode(v);
            tree.push_back(node);
            if (N % 2)
                tree[(N - 1) / 2]->left = node;
            else
                tree[(N - 1) / 2]->right = node;
            return tree[(N - 1) / 2]->val;
        }
    
        TreeNode* get_root() {
            return tree[0];
        }
    

**Java:**

    
    
        List<TreeNode> tree;
         public CBTInserter(TreeNode root) {
            tree = new ArrayList<>();
            tree.add(root);
            for (int i = 0; i < tree.size(); ++i) {
                if (tree.get(i).left != null) tree.add(tree.get(i).left);
                if (tree.get(i).right != null) tree.add(tree.get(i).right);
            }
        }
    
        public int insert(int v) {
            int N = tree.size();
            TreeNode node = new TreeNode(v);
            tree.add(node);
            if (N % 2 == 1)
                tree.get((N - 1) / 2).left = node;
            else
                tree.get((N - 1) / 2).right = node;
            return tree.get((N - 1) / 2).val;
        }
    
        public TreeNode get_root() {
            return tree.get(0);
        }
    

**Python:**

    
    
         def __init__(self, root):
            self.tree = [root]
            for i in self.tree:
                if i.left: self.tree.append(i.left)
                if i.right: self.tree.append(i.right)
    
        def insert(self, v):
            N = len(self.tree)
            self.tree.append(TreeNode(v))
            if N % 2:
                self.tree[(N - 1) / 2].left = self.tree[-1]
            else:
                self.tree[(N - 1) / 2].right = self.tree[-1]
            return self.tree[(N - 1) / 2].val
    
        def get_root(self):
            return self.tree[0]
    


### Solution 2
Logic is simple:

  1. Traverse the binary tree by level order;
  2. If the current node has left and right child, pop it out, and add both of its children into the queue; otherwise, insert new node as its child;
  3. repeat the above till encounter the first node that does NOT have two children.

Method 1:  
Init: O(1), insert(): O(n)

    
    
        
        private TreeNode root;
        
        public CBTInserter(TreeNode root) { this.root = root; }
        
        public int insert(int v) {
    		Queue<TreeNode> q = new LinkedList<>();
            q.offer(root);
            while (true) {
                TreeNode n = q.poll();
                if (n.left != null && n.right != null) { // n has 2 children.
                    q.offer(n.left);    
                    q.offer(n.right);
                }else {                                  // n has at most 1 child.
                    if (n.left == null) { n.left = new TreeNode(v); }
                    else { n.right = new TreeNode(v); }
                    return n.val;
                }
            } 
        }
        
        public TreeNode get_root() { return root; }
    
    

Method 2:  
Init: O(n), insert(): O(1)

    
    
        private TreeNode root;
        private Queue<TreeNode> q = new LinkedList<>();
        
        public CBTInserter(TreeNode root) {
            this.root = root;
            q.offer(root);
            while (q.peek().left != null && q.peek().right != null) {
                q.offer(q.peek().left);    
                q.offer(q.poll().right);
            } 
        }
        
        public int insert(int v) {
            TreeNode p = q.peek();
            if (p.left == null) { 
                p.left = new TreeNode(v); 
            }else { 
                p.right = new TreeNode(v); 
                q.offer(p.left);
                q.offer(p.right);
                q.poll();
            }
            return p.val;
        }
    		
        public TreeNode get_root() { return root; }
    


### Solution 3
  * For this question, if you discuss the trade off between O(1) insert and O(1) build tree with interviewer, you are awesome!
  * I was thinking how to solve it by given a complete BST instead of binary tree, which could be a follow up question.

### 1\. O(1) Insert + O(N) Build Tree:

    
    
    class CBTInserter {
        private TreeNode root;
        private Queue<TreeNode> queue;
        //O(N) build tree: Find the first node which doesn't have both left and right child.
        public CBTInserter(TreeNode root) {
            this.root = root;
            queue = new LinkedList<>();
            queue.offer(root);
            while (true) {
                TreeNode cur = queue.peek();
                if (cur.left != null && cur.right != null) {
                    queue.offer(cur.left);
                    queue.offer(cur.right);
                    queue.poll();
                } else {
                    break;
                }
            }
        }
        
        public int insert(int v) {
            TreeNode cur = queue.peek();
            if (cur.left == null) {
                cur.left = new TreeNode(v);
            } else {
                cur.right = new TreeNode(v);
                queue.offer(cur.left);
                queue.offer(cur.right);
                queue.poll();
            }
            return cur.val;
        }
        
        public TreeNode get_root() {
            return root;
        }
    }
    

### 2\. O(1) Build Tree + O(N) Insert:

    
    
    class CBTInserter {
        private TreeNode root;
        
        public CBTInserter(TreeNode root) {
            this.root = root;    
        }
        
        public int insert(int v) {
            Queue<TreeNode> queue = new LinkedList<>();
            queue.offer(root);
            while (!queue.isEmpty()) {
                TreeNode cur = queue.poll();
                if (cur.left != null) {
                    queue.offer(cur.left);
                } else {
                    cur.left = new TreeNode(v);
                    return cur.val;
                }
                
                if (cur.right != null) {
                    queue.offer(cur.right);
                } else {
                    cur.right = new TreeNode(v);
                    return cur.val;
                }
            }
            return 0;
        }
        
        public TreeNode get_root() {
            return root;
        }
    }
    



