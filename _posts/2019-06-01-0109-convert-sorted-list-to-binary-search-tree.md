---
layout: post
title: 109. Convert Sorted List to Binary Search Tree
---
### Question
Given a singly linked list where elements are sorted in ascending order,
convert it to a height balanced BST.

For this problem, a height-balanced binary tree is defined as a binary tree in
which the depth of the two subtrees of _every_ node never differ by more than
1.

 **Example:**

    
    
    Given the sorted linked list: [-10,-3,0,5,9],
    
    One possible answer is: [0,-3,9,-10,null,5], which represents the following height balanced BST:
    
          0
         / \
       -3   9
       /   /
     -10  5
    

### Solution 1
    
    
    public class Solution {
    public TreeNode sortedListToBST(ListNode head) {
        if(head==null) return null;
        return toBST(head,null);
    }
    public TreeNode toBST(ListNode head, ListNode tail){
        ListNode slow = head;
        ListNode fast = head;
        if(head==tail) return null;
        
        while(fast!=tail&&fast.next!=tail){
            fast = fast.next.next;
            slow = slow.next;
        }
        TreeNode thead = new TreeNode(slow.val);
        thead.left = toBST(head,slow);
        thead.right = toBST(slow.next,tail);
        return thead;
    }
    

}


### Solution 2
    
    
    private ListNode node;
    
    public TreeNode sortedListToBST(ListNode head) {
    	if(head == null){
    		return null;
    	}
    	
    	int size = 0;
    	ListNode runner = head;
    	node = head;
    	
    	while(runner != null){
    		runner = runner.next;
    		size ++;
    	}
    	
    	return inorderHelper(0, size - 1);
    }
    
    public TreeNode inorderHelper(int start, int end){
    	if(start > end){
    		return null;
    	}
    	
    	int mid = start + (end - start) / 2;
    	TreeNode left = inorderHelper(start, mid - 1);
    	
    	TreeNode treenode = new TreeNode(node.val);
    	treenode.left = left;
    	node = node.next;
    
    	TreeNode right = inorderHelper(mid + 1, end);
    	treenode.right = right;
    	
    	return treenode;
    }


### Solution 3
count is a function to calculate the size of list.

Key words: inorder traversal.

    
    
    class Solution {
    public:
        ListNode *list;
        int count(ListNode *node){
            int size = 0;
            while (node) {
                ++size;
                node = node->next;
            }
            return size;
        }
        
        TreeNode *generate(int n){
            if (n == 0)
                return NULL;
            TreeNode *node = new TreeNode(0);
            node->left = generate(n / 2);
            node->val = list->val;
            list = list->next;
            node->right = generate(n - n / 2 - 1);
            return node;
        }
        
        TreeNode *sortedListToBST(ListNode *head) {
            this->list = head;
            return generate(count(head));
        }
    };



