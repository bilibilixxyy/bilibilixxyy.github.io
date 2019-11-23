---
layout: post
title: 230. Kth Smallest Element in a BST
---
### Question
Given a binary search tree, write a function `kthSmallest` to find the **k**
th smallest element in it.

 **Note:**  
You may assume k is always valid, 1 ≤ k ≤ BST's total elements.

 **Example 1:**

    
    
     **Input:** root = [3,1,4,null,2], k = 1
       3
      / \
     1   4
      \
        2
    **Output:** 1

**Example 2:**

    
    
    **Input:** root = [5,3,6,2,4,null,null,1], k = 3
           5
          / \
         3   6
        / \
       2   4
      /
     1
    **Output:** 3
    

**Follow up:**  
What if the BST is modified (insert/delete operations) often and you need to
find the kth smallest frequently? How would you optimize the kthSmallest
routine?

### Solution 1
Binary Search (dfs): (edited 1/2019) this is NOT preferrable as in performance
but since the quesiton is categorized with Binary Search tag, I was trying to
solve it in that way.

  * time complexity: O(N) best, O(N^2) worst
    
        public int kthSmallest(TreeNode root, int k) {
          int count = countNodes(root.left);
          if (k <= count) {
              return kthSmallest(root.left, k);
          } else if (k > count + 1) {
              return kthSmallest(root.right, k-1-count); // 1 is counted as current node
          }
          
          return root.val;
      }
      
      public int countNodes(TreeNode n) {
          if (n == null) return 0;
          
          return 1 + countNodes(n.left) + countNodes(n.right);
      }
    

DFS in-order recursive:

  * time complexity: O(N)
    
          // better keep these two variables in a wrapper class
      private static int number = 0;
      private static int count = 0;
    
      public int kthSmallest(TreeNode root, int k) {
          count = k;
          helper(root);
          return number;
      }
      
      public void helper(TreeNode n) {
          if (n.left != null) helper(n.left);
          count--;
          if (count == 0) {
              number = n.val;
              return;
          }
          if (n.right != null) helper(n.right);
      }
    

DFS in-order iterative:

  * time complexity: O(N) best
    
        public int kthSmallest(TreeNode root, int k) {
          Stack<TreeNode> st = new Stack<>();
          
          while (root != null) {
              st.push(root);
              root = root.left;
          }
              
          while (k != 0) {
              TreeNode n = st.pop();
              k--;
              if (k == 0) return n.val;
              TreeNode right = n.right;
              while (right != null) {
                  st.push(right);
                  right = right.left;
              }
          }
          
          return -1; // never hit if k is valid
    }
    

(edited: 7/2017)  
**note: requirement has been changed a bit since last time I visited that the
counting could be looked up frequently and BST itself could be altered
(inserted/deleted) by multiple times, so that's the main reason that I stored
them in an array.**

    
    
     class Solution(object):
        def kthSmallest(self, root, k):
            """
            :type root: TreeNode
            :type k: int
            :rtype: int
            """
            count = []
            self.helper(root, count)
            return count[k-1]
            
        def helper(self, node, count):
            if not node:
                return
            
            self.helper(node.left, count)
            count.append(node.val)
            self.helper(node.right, count)
    

DFS recursive, stop early when meet kth

    
    
    def findNode(node, res):
                if len(res) > 1:
                    return
    
                if node.left:
                    findNode(node.left, res)
    
                res[0] -= 1
                if res[0] == 0:
                    res.append(node.val)
                    return
                
                if node.right:
                    findNode(node.right, res)
                    
            res = [k]
            findNode(root, res)
            return res[1]
    

Thanks again!


### Solution 2
If we could add a count field in the BST node class, it will take O(n) time
when we calculate the count value for the whole tree, but after that, it will
take O(logn) time when insert/delete a node or calculate the kth smallest
element.

    
    
       public class Solution {
            public int kthSmallest(TreeNode root, int k) {
                TreeNodeWithCount rootWithCount = buildTreeWithCount(root);
                return kthSmallest(rootWithCount, k);
            }
            
            private TreeNodeWithCount buildTreeWithCount(TreeNode root) {
                if (root == null) return null;
                TreeNodeWithCount rootWithCount = new TreeNodeWithCount(root.val);
                rootWithCount.left = buildTreeWithCount(root.left);
                rootWithCount.right = buildTreeWithCount(root.right);
                if (rootWithCount.left != null) rootWithCount.count += rootWithCount.left.count;
                if (rootWithCount.right != null) rootWithCount.count += rootWithCount.right.count;
                return rootWithCount;
            }
            
            private int kthSmallest(TreeNodeWithCount rootWithCount, int k) {
                if (k <= 0 || k > rootWithCount.count) return -1;
                if (rootWithCount.left != null) {
                    if (rootWithCount.left.count >= k) return kthSmallest(rootWithCount.left, k);
                    if (rootWithCount.left.count == k-1) return rootWithCount.val;
                    return kthSmallest(rootWithCount.right, k-1-rootWithCount.left.count);
                } else {
                    if (k == 1) return rootWithCount.val;
                    return kthSmallest(rootWithCount.right, k-1);
                }
            }
            
            class TreeNodeWithCount {
                int val;
                int count;
                TreeNodeWithCount left;
                TreeNodeWithCount right;
                TreeNodeWithCount(int x) {val = x; count = 1;};
            }
        }


### Solution 3
In order traverse for BST gives the natural order of numbers. No need to use
array.

Recursive:

    
    
    int count = 0;
    int result = Integer.MIN_VALUE;
    
    public int kthSmallest(TreeNode root, int k) {
        traverse(root, k);
        return result;
    }
    
    public void traverse(TreeNode root, int k) {
        if(root == null) return;
        traverse(root.left, k);
        count ++;
        if(count == k) result = root.val;
        traverse(root.right, k);       
    }
    

Iterative:

    
    
     public int kthSmallest(TreeNode root, int k) {
         Stack<TreeNode> stack = new Stack<TreeNode>();
         TreeNode p = root;
         int count = 0;
         
         while(!stack.isEmpty() || p != null) {
             if(p != null) {
                 stack.push(p);  // Just like recursion
                 p = p.left;   
                 
             } else {
                TreeNode node = stack.pop();
                if(++count == k) return node.val; 
                p = node.right;
             }
         }
         
         return Integer.MIN_VALUE;
     }



