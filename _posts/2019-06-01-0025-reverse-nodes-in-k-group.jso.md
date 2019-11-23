---
layout: post
title: 25. Reverse Nodes in k-Group
---
### Question
Given a linked list, reverse the nodes of a linked list _k_ at a time and
return its modified list.

_k_ is a positive integer and is less than or equal to the length of the
linked list. If the number of nodes is not a multiple of _k_ then left-out
nodes in the end should remain as it is.

**Example:**

Given this linked list: `1->2->3->4->5`

For _k_ = 2, you should return: `2->1->4->3->5`

For _k_ = 3, you should return: `3->2->1->4->5`

**Note:**

  * Only constant extra memory is allowed.
  * You may not alter the values in the list's nodes, only nodes itself may be changed.

### Solution 1
Hi, guys!  
Despite the fact that the approach is recursive, the code is less than 20
lines. :)

    
    
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode curr = head;
        int count = 0;
        while (curr != null && count != k) { // find the k+1 node
            curr = curr.next;
            count++;
        }
        if (count == k) { // if k+1 node is found
            curr = reverseKGroup(curr, k); // reverse list with k+1 node as head
            // head - head-pointer to direct part, 
            // curr - head-pointer to reversed part;
            while (count-- > 0) { // reverse current k-group: 
                ListNode tmp = head.next; // tmp - next head in direct part
                head.next = curr; // preappending "direct" head to the reversed list 
                curr = head; // move head of reversed part to a new node
                head = tmp; // move "direct" head to the next node in direct part
            }
            head = curr;
        }
        return head;
    }
    

Hope it helps!


### Solution 2
Reference:  
<http://www.cnblogs.com/lichen782/p/leetcode_Reverse_Nodes_in_kGroup.html>

First, build a function reverse() to reverse the ListNode between begin and
end. See the explanation below:

    
    
       /**
         * Reverse a link list between begin and end exclusively
         * an example:
         * a linked list:
         * 0->1->2->3->4->5->6
         * |           |   
         * begin       end
         * after call begin = reverse(begin, end)
         * 
         * 0->3->2->1->4->5->6
         *          |  |
         *      begin end
         * @return the reversed list's 'begin' node, which is the precedence of node end
         */
    

Then walk thru the linked list and apply reverse() iteratively. See the code
below.

    
    
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode begin;
        if (head==null || head.next ==null || k==1)
        	return head;
        ListNode dummyhead = new ListNode(-1);
        dummyhead.next = head;
        begin = dummyhead;
        int i=0;
        while (head != null){
        	i++;
        	if (i%k == 0){
        		begin = reverse(begin, head.next);
        		head = begin.next;
        	} else {
        		head = head.next;
        	}
        }
        return dummyhead.next;
        
    }
    
    public ListNode reverse(ListNode begin, ListNode end){
    	ListNode curr = begin.next;
    	ListNode next, first;
    	ListNode prev = begin;
    	first = curr;
    	while (curr!=end){
    		next = curr.next;
    		curr.next = prev;
    		prev = curr;
    		curr = next;
    	}
    	begin.next = prev;
    	first.next = curr;
    	return first;
    }


### Solution 3
    
    
      public class Solution {
            public ListNode reverseKGroup(ListNode head, int k) {
                if (head==null||head.next==null||k<2) return head;
        
                ListNode dummy = new ListNode(0);
                dummy.next = head;
                
                ListNode tail = dummy, prev = dummy,temp;
                int count;
                while(true){
                    count =k;
                    while(count>0&&tail!=null){
                        count--;
                        tail=tail.next;
                    } 
                    if (tail==null) break;//Has reached the end
                    
        
                    head=prev.next;//for next cycle
                // prev-->temp-->...--->....--->tail-->....
                // Delete @temp and insert to the next position of @tail
                // prev-->...-->...-->tail-->head-->...
                // Assign @temp to the next node of @prev
                // prev-->temp-->...-->tail-->...-->...
                // Keep doing until @tail is the next node of @prev
                    while(prev.next!=tail){
                        temp=prev.next;//Assign
                        prev.next=temp.next;//Delete
                        
                        temp.next=tail.next;
                        tail.next=temp;//Insert
                        
                    }
                    
                    tail=head;
                    prev=head;
                    
                }
                return dummy.next;
                
            }
        }



