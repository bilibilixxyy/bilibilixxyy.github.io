---
layout: post
title: 83. Remove Duplicates from Sorted List
---
### Question
Given a sorted linked list, delete all duplicates such that each element
appear only _once_.

 **Example 1:**

    
    
     **Input:** 1- >1->2
    **Output:** 1- >2
    

**Example 2:**

    
    
    **Input:** 1- >1->2->3->3
    **Output:** 1- >2->3
    

### Solution 1
This solution is inspired by renzid
<https://leetcode.com/discuss/33043/3-line-recursive-solution>

    
    
    public ListNode deleteDuplicates(ListNode head) {
            if(head == null || head.next == null)return head;
            head.next = deleteDuplicates(head.next);
            return head.val == head.next.val ? head.next : head;
    }
    

Enjoy!


### Solution 2
    
    
    public class Solution {
        public ListNode deleteDuplicates(ListNode head) {
            ListNode list = head;
             
             while(list != null) {
            	 if (list.next == null) {
            		 break;
            	 }
            	 if (list.val == list.next.val) {
            		 list.next = list.next.next;
            	 } else {
            		 list = list.next;
            	 }
             }
             
             return head;
        }
    }


### Solution 3
    
    
    def deleteDuplicates(self, head):
        cur = head
        while cur:
            while cur.next and cur.next.val == cur.val:
                cur.next = cur.next.next     # skip duplicated node
            cur = cur.next     # not duplicate of current node, move to next node
        return head



