---
layout: post
title: 2
.
Add Two Numbers
---
### Question
You are given two **non-empty** linked lists representing two non-negative
integers. The digits are stored in **reverse order** and each of their nodes
contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the
number 0 itself.

 **Example:**

    
    
     **Input:** (2 - > 4 -> 3) + (5 -> 6 -> 4)
    **Output:** 7 - > 0 -> 8
    **Explanation:** 342 + 465 = 807.
    
### Solution 1
    
    
     public class Solution {
        public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
            ListNode c1 = l1;
            ListNode c2 = l2;
            ListNode sentinel = new ListNode(0);
            ListNode d = sentinel;
            int sum = 0;
            while (c1 != null || c2 != null) {
                sum /= 10;
                if (c1 != null) {
                    sum += c1.val;
                    c1 = c1.next;
                }
                if (c2 != null) {
                    sum += c2.val;
                    c2 = c2.next;
                }
                d.next = new ListNode(sum % 10);
                d = d.next;
            }
            if (sum / 10 == 1)
                d.next = new ListNode(1);
            return sentinel.next;
        }
    }
### Solution 2
    
    
    ListNode *addTwoNumbers(ListNode *l1, ListNode *l2) {
        ListNode preHead(0), *p = &preHead;
        int extra = 0;
        while (l1 || l2 || extra) {
            int sum = (l1 ? l1->val : 0) + (l2 ? l2->val : 0) + extra;
            extra = sum / 10;
            p->next = new ListNode(sum % 10);
            p = p->next;
            l1 = l1 ? l1->next : l1;
            l2 = l2 ? l2->next : l2;
        }
        return preHead.next;
    }
### Solution 3
    
    
    class Solution:
    # @return a ListNode
    def addTwoNumbers(self, l1, l2):
        carry = 0
        root = n = ListNode(0)
        while l1 or l2 or carry:
            v1 = v2 = 0
            if l1:
                v1 = l1.val
                l1 = l1.next
            if l2:
                v2 = l2.val
                l2 = l2.next
            carry, val = divmod(v1+v2+carry, 10)
            n.next = ListNode(val)
            n = n.next
        return root.next

