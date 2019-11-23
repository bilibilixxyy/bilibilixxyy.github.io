---
layout: post
title: 110. Balanced Binary Tree
---
### Question
Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as:

> a binary tree in which the depth of the two subtrees of _every_ node never
differ by more than 1.

 **Example 1:**

Given the following tree `[3,9,20,null,null,15,7]`:

    
    
        3
       / \
      9  20
        /  \
       15   7

Return true.  
  
**Example 2:**

Given the following tree `[1,2,2,3,3,null,null,4,4]`:

    
    
           1
          / \
         2   2
        / \
       3   3
      / \
     4   4
    

Return false.

### Solution 1
This problem is generally believed to have two solutions: the top down
approach and the bottom up way.

1.The first method checks whether the tree is balanced strictly according to
the definition of balanced binary tree: the difference between the heights of
the two sub trees are not bigger than 1, and both the left sub tree and right
sub tree are also balanced. With the helper function depth(), we could easily
write the code;

    
    
    class solution {
    public:
        int depth (TreeNode *root) {
            if (root == NULL) return 0;
            return max (depth(root -> left), depth (root -> right)) + 1;
        }
    
        bool isBalanced (TreeNode *root) {
            if (root == NULL) return true;
            
            int left=depth(root->left);
            int right=depth(root->right);
            
            return abs(left - right) <= 1 && isBalanced(root->left) && isBalanced(root->right);
        }
    };
    

For the current node root, calling depth() for its left and right children
actually has to access all of its children, thus the complexity is O(N). We do
this for each node in the tree, so the overall complexity of isBalanced will
be O(N^2). This is a top down approach.

2.The second method is based on DFS. Instead of calling depth() explicitly for
each child node, we return the height of the current node in DFS recursion.
When the sub tree of the current node (inclusive) is balanced, the function
dfsHeight() returns a non-negative value as the height. Otherwise -1 is
returned. According to the leftHeight and rightHeight of the two children, the
parent node could check if the sub tree  
is balanced, and decides its return value.

    
    
    class solution {
    public:
    int dfsHeight (TreeNode *root) {
            if (root == NULL) return 0;
            
            int leftHeight = dfsHeight (root -> left);
            if (leftHeight == -1) return -1;
            int rightHeight = dfsHeight (root -> right);
            if (rightHeight == -1) return -1;
            
            if (abs(leftHeight - rightHeight) > 1)  return -1;
            return max (leftHeight, rightHeight) + 1;
        }
        bool isBalanced(TreeNode *root) {
            return dfsHeight (root) != -1;
        }
    };
    

In this bottom up approach, each node in the tree only need to be accessed
once. Thus the time complexity is O(N), better than the first solution.


### Solution 2
    
    
    public boolean isBalanced(TreeNode root) {
        if(root==null){
            return true;
        }
        return height(root)!=-1;
        
    }
    public int height(TreeNode node){
        if(node==null){
            return 0;
        }
        int lH=height(node.left);
        if(lH==-1){
            return -1;
        }
        int rH=height(node.right);
        if(rH==-1){
            return -1;
        }
        if(lH-rH<-1 || lH-rH>1){
            return -1;
        }
        return Math.max(lH,rH)+1;
    }


### Solution 3
# Tree高度延伸的一类题目

### 104\. Maximum Depth of Binary Tree

最基础的递归，先递归到底，当Leaf Node的左右两个Children Node都分别触及Base
Case，也就是None的时候，向上返回。然后之后对应当前node，左右两边的递归都操作结束以后，返回的过程中对左右高度进行对比，取两个中间最大值，然后这里记住要加`1`，也就是当前的层数。

    
    
    class Solution(object):
        def maxDepth_gd(self, root):
            '''bugfree'''
            if not root: return 0
    
            left = self.maxDepth(root.left)
            right = self.maxDepth(root.right)
            return max(left, right) + 1
    

### 110\. Balanced Binary Tree

有了104的基础，我们在延伸下看看110这道题，其实就是基于高度计算，然后判断一下。

但由于嵌套的Recursion调用，整体的时间复杂度是：O(nlogn) ,
在每一层调用get_height的平均时间复杂度是O(N)，然后基于二叉树的性质，调用了的高度是logn，所以n * logn 的时间复杂。

时间复杂度为什么是nlogn搞不清楚的看
[时间复杂度图解](https://raw.githubusercontent.com/yuzhoujr/spazzatura/master/img_box/balanced.jpg)

    
    
    class Solution(object):
        def isBalanced(self, root):
            if not root: return True
            left = self.get_height(root.left)
            right = self.get_height(root.right)
            if abs(left - right) > 1: 
                return False  
            return self.isBalanced(root.left) and self.isBalanced(root.right)
    
            
        def get_height(self, root):
            if not root: return 0
            left = self.get_height(root.left)
            right = self.get_height(root.right)
            return max(left, right) + 1
    

上面这种Brute
Froce的方法，整棵树有很多冗余无意义的遍历，其实我们在处理完`get_height`这个高度的时候，我们完全可以在检查每个节点高度并且返回的同时，记录左右差是否已经超过1，只要有一个节点超过1，那么直接返回False即可，因此我们只需要在外围设立一个全球变量记录True和False，在调用get_height的时候，内置代码里加入对左右高度的判定即可，代码如下

时间复杂度: O(N)

    
    
    # Recursive Rules:
    # 索取：Node的左孩子是不是全部是Balanced，Node的右孩子是不是全部是Balanced的，
    # 返回：如果都是Balanced的，返回True，不然返回False
    
    class Solution(object):
        def isBalanced(self, root):
            self.flag = False
            self.getHeight(root)
            return not self.flag
            
        
        def getHeight(self, root):
            if not root: return 0
            left = self.getHeight(root.left)
            right = self.getHeight(root.right)
            if abs(left - right) > 1: 
                self.flag = True
            return max(left, right) + 1
    

最后Leetcode上有一种`-1`的方法，其实就是上面这种方法的一种延伸。如果左右两边出现了高度差高于1的情况，直接返回`-1`，这个`-1`怎么来的？因为高度不可能为负数，`-1`其实就是一种True/False的表达。

那么在实现上，我们只要对`get_height`每次返回前做一个判定即可，具体实现看下方：

时间复杂度: O(N)

    
    
    class Solution(object):
        def isBalanced(self, root):
            height = self.get_height(root)
            return height != -1
    
            
        def get_height(self, root):
            if not root: return 0
            left = self.get_height(root.left)
            right = self.get_height(root.right)
            if left == -1 or right == -1 : return -1          
            if abs(left - right) > 1:  return -1
            return max(left, right) + 1
    



