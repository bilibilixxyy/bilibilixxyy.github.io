---
layout: post
title: 328. Odd Even Linked List
---
### Question
Given a singly linked list, group all odd nodes together followed by the even
nodes. Please note here we are talking about the node number and not the value
in the nodes.

You should try to do it in place. The program should run in O(1) space
complexity and O(nodes) time complexity.

 **Example 1:**

    
    
     **Input:**1->2->3->4->5->NULL
    **Output:**1->3->5->2->4->NULL
    

**Example 2:**

    
    
    **Input:** 2->1->3->5->6->4->7->NULL
    **Output:**2->3->6->7->1->5->4->NULL
    

**Note:**

  * The relative order inside both the even and odd groups should remain as it was in the input.
  * The first node is considered odd, the second node even and so on ...

### Solution 1
    
    
    public class Solution {
    public ListNode oddEvenList(ListNode head) {
        if (head != null) {
        
            ListNode odd = head, even = head.next, evenHead = even; 
        
            while (even != null && even.next != null) {
                odd.next = odd.next.next; 
                even.next = even.next.next; 
                odd = odd.next;
                even = even.next;
            }
            odd.next = evenHead; 
        }
        return head;
    }}


### Solution 2
    
    
    public ListNode oddEvenList(ListNode head) {
        if(head==null||head.next==null) return head;
        ListNode odd=head,ehead=head.next,even=ehead;
        while(even!=null&&even.next!=null){
            odd.next=even.next;
            odd=odd.next;
            even.next=odd.next;
            even=even.next;
        }
        odd.next=ehead;
        return head;
    }


### Solution 3
    
    
        ListNode* oddEvenList(ListNode* head) 
        {
            if(!head) return head;
            ListNode *odd=head, *evenhead=head->next, *even = evenhead;
            while(even && even->next)
            {
                odd->next = odd->next->next;
                even->next = even->next->next;
                odd = odd->next;
                even = even->next;
            }
            odd->next = evenhead;
            return head;
        }



