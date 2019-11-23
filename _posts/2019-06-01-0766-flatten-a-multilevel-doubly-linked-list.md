---
layout: post
title: 766. Flatten a Multilevel Doubly Linked List
---
### Question
You are given a doubly linked list which in addition to the next and previous
pointers, it could have a child pointer, which may or may not point to a
separate doubly linked list. These child lists may have one or more children
of their own, and so on, to produce a multilevel data structure, as shown in
the example below.

Flatten the list so that all the nodes appear in a single-level, doubly linked
list. You are given the head of the first level of the list.



 **Example:**

    
    
     **Input:**
     1---2---3---4---5---6--NULL
             |
             7---8---9---10--NULL
                 |
                 11--12--NULL
    
    **Output:**
    1-2-3-7-8-11-12-9-10-4-5-6-NULL
    



**Explanation for the above example:**

Given the following multilevel doubly linked list:

    
    
    ![](https://assets.leetcode.com/uploads/2018/10/12/multilevellinkedlist.png)



We should return the following flattened doubly linked list:

    
    
    ![](https://assets.leetcode.com/uploads/2018/10/12/multilevellinkedlistflattened.png)

### Solution 1
Basic idea is straight forward:

  1. Start form the `head` , move one step each time to the next node
  2. When meet with a node with child, say node `p`, follow its `child chain` to the end and connect the tail node with `p.next`, by doing this we merged the `child chain` back to the `main thread`
  3. Return to `p` and proceed until find next node with child.
  4. Repeat until reach `null`

    
    
    class Solution {
        public Node flatten(Node head) {
            if( head == null) return head;
    	// Pointer
            Node p = head; 
            while( p!= null) {
                /* CASE 1: if no child, proceed */
                if( p.child == null ) {
                    p = p.next;
                    continue;
                }
                /* CASE 2: got child, find the tail of the child and link it to p.next */
                Node temp = p.child;
                // Find the tail of the child
                while( temp.next != null ) 
                    temp = temp.next;
                // Connect tail with p.next, if it is not null
                temp.next = p.next;  
                if( p.next != null )  p.next.prev = temp;
                // Connect p with p.child, and remove p.child
                p.next = p.child; 
                p.child.prev = p;
                p.child = null;
            }
            return head;
        }
    }
    


### Solution 2
    
    
        public Node flatten(Node head) {
        	flattentail(head);
        	return head;
        }
    
        // flattentail: flatten the node "head" and return the tail in its child (if exists)
        // the tail means the last node after flattening "head"
    
        // Five situations:
        // 1. null - no need to flatten, just return it
        // 2. no child, no next - no need to flatten, it is the last element, just return it
        // 3. no child, next - no need to flatten, go next
        // 4. child, no next - flatten the child and done
        // 5. child, next - flatten the child, connect it with the next, go next
    
        private Node flattentail(Node head) {
        	if (head == null) return head; // CASE 1
        	if (head.child == null) {
        		if (head.next == null) return head; // CASE 2
        		return flattentail(head.next); // CASE 3
        	}
        	else {
        		Node child = head.child;  
        		head.child = null;
        		Node next = head.next;
        		Node childtail = flattentail(child);
        		head.next = child;
        		child.prev = head;  
    			if (next != null) { // CASE 5
    				childtail.next = next;
    				next.prev = childtail;
    				return flattentail(next);
    			}
                return childtail; // CASE 4
        	}	   	
        }
    


### Solution 3
    
    
    class Solution(object):
        def flatten(self, head):
            if not head:
                return
            
            dummy = Node(0,None,head,None)     
            stack = []
            stack.append(head)
            prev = dummy
            
            while stack:
                root = stack.pop()
    
                root.prev = prev
                prev.next = root
                
                if root.next:
                    stack.append(root.next)
                    root.next = None
                if root.child:
                    stack.append(root.child)
                    root.child = None
                prev = root        
                
            
            dummy.next.prev = None
            return dummy.next
    



