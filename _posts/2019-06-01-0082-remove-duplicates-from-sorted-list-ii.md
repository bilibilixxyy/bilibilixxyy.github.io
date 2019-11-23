---
layout: post
title: 82. Remove Duplicates from Sorted List II
---
### Question
Given a sorted linked list, delete all nodes that have duplicate numbers,
leaving only _distinct_ numbers from the original list.

 **Example 1:**

    
    
     **Input:** 1- >2->3->3->4->4->5
    **Output:** 1- >2->5
    

**Example 2:**

    
    
    **Input:** 1- >1->1->2->3
    **Output:** 2- >3
    

### Solution 1
    
    
    public ListNode deleteDuplicates(ListNode head) {
            if(head==null) return null;
            ListNode FakeHead=new ListNode(0);
            FakeHead.next=head;
            ListNode pre=FakeHead;
            ListNode cur=head;
            while(cur!=null){
                while(cur.next!=null&&cur.val==cur.next.val){
                    cur=cur.next;
                }
                if(pre.next==cur){
                    pre=pre.next;
                }
                else{
                    pre.next=cur.next;
                }
                cur=cur.next;
            }
            return FakeHead.next;
        }


### Solution 2
    
    
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) return null;
        
        if (head.next != null && head.val == head.next.val) {
            while (head.next != null && head.val == head.next.val) {
                head = head.next;
            }
            return deleteDuplicates(head.next);
        } else {
            head.next = deleteDuplicates(head.next);
        }
        return head;
    }
    

if current node is not unique, return deleteDuplicates with head.next.  
If current node is unique, link it to the result of next list made by
recursive call. Any improvement?


### Solution 3
    
    
    def deleteDuplicates(self, head):
        dummy = pre = ListNode(0)
        dummy.next = head
        while head and head.next:
            if head.val == head.next.val:
                while head and head.next and head.val == head.next.val:
                    head = head.next
                head = head.next
                pre.next = head
            else:
                pre = pre.next
                head = head.next
        return dummy.next



