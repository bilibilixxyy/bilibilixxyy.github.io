---
layout: post
title: 116. Populating Next Right Pointers in Each Node
---
### Question
You are given a **perfect binary tree**  where all leaves are on the same
level, and every parent has two children. The binary tree has the following
definition:

    
    
    struct Node {
      int val;
      Node *left;
      Node *right;
      Node *next;
    }
    

Populate each next pointer to point to its next right node. If there is no
next right node, the next pointer should be set to `NULL`.

Initially, all next pointers are set to `NULL`.



**Example:**

![](https://assets.leetcode.com/uploads/2019/02/14/116_sample.png)

    
    
    **Input:** {"$id":"1","left":{"$id":"2","left":{"$id":"3","left":null,"next":null,"right":null,"val":4},"next":null,"right":{"$id":"4","left":null,"next":null,"right":null,"val":5},"val":2},"next":null,"right":{"$id":"5","left":{"$id":"6","left":null,"next":null,"right":null,"val":6},"next":null,"right":{"$id":"7","left":null,"next":null,"right":null,"val":7},"val":3},"val":1}
    
    **Output:** {"$id":"1","left":{"$id":"2","left":{"$id":"3","left":null,"next":{"$id":"4","left":null,"next":{"$id":"5","left":null,"next":{"$id":"6","left":null,"next":null,"right":null,"val":7},"right":null,"val":6},"right":null,"val":5},"right":null,"val":4},"next":{"$id":"7","left":{"$ref":"5"},"next":null,"right":{"$ref":"6"},"val":3},"right":{"$ref":"4"},"val":2},"next":null,"right":{"$ref":"7"},"val":1}
    
    **Explanation:** Given the above perfect binary tree (Figure A), your function should populate each next pointer to point to its next right node, just like in Figure B.
    



 **Note:**

  * You may only use constant extra space.
  * Recursive approach is fine, implicit stack space does not count as extra space for this problem.

### Solution 1
    
    
    void connect(TreeLinkNode *root) {
        if (root == NULL) return;
        TreeLinkNode *pre = root;
        TreeLinkNode *cur = NULL;
        while(pre->left) {
            cur = pre;
            while(cur) {
                cur->left->next = cur->right;
                if(cur->next) cur->right->next = cur->next->left;
                cur = cur->next;
            }
            pre = pre->left;
        }
    }
    

you need two additional pointer.


### Solution 2
    
    
    public class Solution {
        public void connect(TreeLinkNode root) {
            TreeLinkNode level_start=root;
            while(level_start!=null){
                TreeLinkNode cur=level_start;
                while(cur!=null){
                    if(cur.left!=null) cur.left.next=cur.right;
                    if(cur.right!=null && cur.next!=null) cur.right.next=cur.next.left;
                    
                    cur=cur.next;
                }
                level_start=level_start.left;
            }
        }
    }


### Solution 3
    
    
    public void connect(TreeLinkNode root) {
        if(root == null)
            return;
            
        if(root.left != null){
            root.left.next = root.right;
            if(root.next != null)
                root.right.next = root.next.left;
        }
        
        connect(root.left);
        connect(root.right);
    }



