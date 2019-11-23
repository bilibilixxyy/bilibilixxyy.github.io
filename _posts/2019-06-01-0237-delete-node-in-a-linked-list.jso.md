---
layout: post
title: 237. Delete Node in a Linked List
---
### Question
Write a function to delete a node (except the tail) in a singly linked list,
given only access to that node.

Given linked list -- head = [4,5,1,9], which looks like following:

![](https://assets.leetcode.com/uploads/2018/12/28/237_example.png)



 **Example 1:**

    
    
     **Input:** head = [4,5,1,9], node = 5
    **Output:** [4,1,9]
    **Explanation:** You are given the second node with value 5, the linked list should become 4 -> 1 -> 9 after calling your function.
    

**Example 2:**

    
    
    **Input:** head = [4,5,1,9], node = 1
    **Output:** [4,5,9]
    **Explanation:** You are given the third node with value 1, the linked list should become 4 -> 5 -> 9 after calling your function.
    



 **Note:**

  * The linked list will have at least two elements.
  * All of the nodes' values will be unique.
  * The given node will not be the tail and it will always be a valid node of the linked list.
  * Do not return anything from your function.

### Solution 1
This question is stupid and should be deleted intermediately.


### Solution 2
We can't really delete the node, but we can kinda achieve the same effect by
instead removing the **next** node after copying its data into the node that
we were asked to delete.

 **C++**

    
    
    void deleteNode(ListNode*  node) {
        *node = *node->next;
    }
    

But better properly delete the next node:

    
    
    void deleteNode(ListNode* node) {
        auto next = node->next;
        *node = *next;
        delete next;
    }
    

**Java and C#**

    
    
    public void deleteNode(ListNode  node) {
        node.val = node.next.val;
        node.next = node.next.next;
    }
    

**Python**

    
    
    def deleteNode(self,  node):
        node.val = node.next.val
        node.next = node.next.next
    

**C**

    
    
    void deleteNode(struct ListNode*  node) {
        *node = *node->next;
    }
    

But better properly free the next node's memory:

    
    
    void deleteNode(struct ListNode* node) {
        struct ListNode* next = node->next;
        *node = *next;
        free(next);
    }
    

**JavaScript**

    
    
    var deleteNode = function( node) {
        node.val = node.next.val;
        node.next = node.next.next;
    };
    

**Ruby**

    
    
    def delete_node( node)
        node.val = node.next.val
        node.next = node.next.next
        nil
    end


### Solution 3
    
    
    public void deleteNode(ListNode node) {
        node.val=node.next.val;
        node.next=node.next.next;
    }
    

Since we couldn't enter the preceding node, we can not delete the given node.
We can just copy the next node to the given node and delete the next one.



