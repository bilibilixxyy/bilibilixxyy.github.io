---
layout: post
title: 450. Delete Node in a BST
---
### Question
Given a root node reference of a BST and a key, delete the node with the given
key in the BST. Return the root node reference (possibly updated) of the BST.

Basically, the deletion can be divided into two stages:

  1. Search for a node to remove.
  2. If the node is found, delete the node.

 **Note:** Time complexity should be O(height of tree).

 **Example:**

    
    
    root = [5,3,6,2,4,null,7]
    key = 3
    
        5
       / \
      3   6
     / \   \
    2   4   7
    
    Given key to delete is 3. So we find the node with value 3 and delete it.
    
    One valid answer is [5,4,6,2,null,null,7], shown in the following BST.
    
        5
       / \
      4   6
     /     \
    2       7
    
    Another valid answer is [5,2,6,null,4,null,7].
    
        5
       / \
      2   6
       \   \
        4   7
    

### Solution 1
Steps:

  1. Recursively find the node that has the same value as the key, while setting the left/right nodes equal to the returned subtree
  2. Once the node is found, have to handle the below 4 cases

  * node doesn't have left or right - return null
  * node only has left subtree- return the left subtree
  * node only has right subtree- return the right subtree
  * node has both left and right - find the minimum value in the right subtree, set that value to the currently found node, then recursively delete the minimum value in the right subtree

    
    
    public TreeNode deleteNode(TreeNode root, int key) {
        if(root == null){
            return null;
        }
        if(key < root.val){
            root.left = deleteNode(root.left, key);
        }else if(key > root.val){
            root.right = deleteNode(root.right, key);
        }else{
            if(root.left == null){
                return root.right;
            }else if(root.right == null){
                return root.left;
            }
            
            TreeNode minNode = findMin(root.right);
            root.val = minNode.val;
            root.right = deleteNode(root.right, root.val);
        }
        return root;
    }
    
    private TreeNode findMin(TreeNode node){
        while(node.left != null){
            node = node.left;
        }
        return node;
    }
    


### Solution 2
    
    
        private TreeNode deleteRootNode(TreeNode root) {
            if (root == null) {
                return null;
            }
            if (root.left == null) {
                return root.right;
            }
            if (root.right == null) {
                return root.left;
            }
            TreeNode next = root.right;
            TreeNode pre = null;
            for(; next.left != null; pre = next, next = next.left);
            next.left = root.left;
            if(root.right != next) {
                pre.left = next.right;
                next.right = root.right;
            }
            return next;
        }
        
        public TreeNode deleteNode(TreeNode root, int key) {
            TreeNode cur = root;
            TreeNode pre = null;
            while(cur != null && cur.val != key) {
                pre = cur;
                if (key < cur.val) {
                    cur = cur.left;
                } else if (key > cur.val) {
                    cur = cur.right;
                }
            }
            if (pre == null) {
                return deleteRootNode(cur);
            }
            if (pre.left == cur) {
                pre.left = deleteRootNode(cur);
            } else {
                pre.right = deleteRootNode(cur);
            }
            return root;
        }
    

Find the node to be removed and its parent using binary search, and then use
deleteRootNode to delete the root node of the subtree and return the new root
node. This idea is taken from <https://discuss.leetcode.com/topic/67309/an-
easy-understanding-o-h-time-o-1-space-java-solution>.

I'd also like to share my thinkings of the other solutions I've seen.

  1. There are many solutions that got high votes using recursive approach, including the ones from the Princeton's Algorithm and Data Structure book. Don't you notice that recursive approach always takes extra space? Why not consider the iterative approach first?
  2. Some solutions swap the values instead of swapping the nodes. In reality, the value of a node could be more complicated than just a single integer, so copying the contents might take much more time than just copying the reference.
  3. As for the case when both children of the node to be deleted are not null, I transplant the successor to replace the node to be deleted, which is a bit harder to implement than just transplant the left subtree of the node to the left child of its successor. The former way is used in many text books too. Why? My guess is that transplanting the successor can keep the height of the tree almost unchanged, while transplanting the whole left subtree could increase the height and thus making the tree more unbalanced.


### Solution 3
    
    
       def deleteNode(root, key):
    	if not root: # if root doesn't exist, just return it
    		return root
    	if root.val > key: # if key value is less than root value, find the node in the left subtree
    		root.left = deleteNode(root.left, key)
    	elif root.val < key: # if key value is greater than root value, find the node in right subtree
    		root.right= deleteNode(root.right, key)
    	else: #if we found the node (root.value == key), start to delete it
    		if not root.right: # if it doesn't have right children, we delete the node then new root would be root.left
    			return root.left
    		if not root.left: # if it has no left children, we delete the node then new root would be root.right
    			return root.right
                   # if the node have both left and right children,  we replace its value with the minmimum value in the right subtree and then delete that minimum node in the right subtree
    		temp = root.right
    		mini = temp.val
    		while temp.left:
    			temp = temp.left
    			mini = temp.val
    		root.val = mini # replace value
    		root.right = deleteNode(root.right,root.val) # delete the minimum node in right subtree
    	return root
    
    



