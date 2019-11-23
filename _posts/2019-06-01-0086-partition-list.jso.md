---
layout: post
title: 86. Partition List
---
### Question
Given a linked list and a value _x_ , partition it such that all nodes less
than _x_ come before nodes greater than or equal to _x_.

You should preserve the original relative order of the nodes in each of the
two partitions.

 **Example:**

    
    
     **Input:** head = 1- >4->3->2->5->2, _x_ = 3
    **Output:** 1- >2->2->4->3->5
    

### Solution 1
    
    
    ListNode *partition(ListNode *head, int x) {
        ListNode node1( 0), node2(0);
        ListNode *p1 = &node1, *p2 = &node2;
        while (head) {
            if (head->val < x)
                p1 = p1->next = head;
            else
                p2 = p2->next = head;
            head = head->next;
        }
        p2->next = NULL;
        p1->next = node2.next;
        return node1.next;
    }


### Solution 2
the basic idea is to maintain two queues, the first one stores all nodes with
val less than x , and the second queue stores all the rest nodes. Then concat
these two queues. Remember to set the tail of second queue a null next, or u
will get TLE.

    
    
    public ListNode partition(ListNode head, int x) {
        ListNode dummy1 = new ListNode(0), dummy2 = new ListNode(0);  //dummy heads of the 1st and 2nd queues
        ListNode curr1 = dummy1, curr2 = dummy2;      //current tails of the two queues;
        while (head!=null){
            if (head.val<x) {
                curr1.next = head;
                curr1 = head;
            }else {
                curr2.next = head;
                curr2 = head;
            }
            head = head.next;
        }
        curr2.next = null;          //important! avoid cycle in linked list. otherwise u will get TLE.
        curr1.next = dummy2.next;
        return dummy1.next;
    }


### Solution 3
    
    
    def partition(self, head, x):
        h1 = l1 = ListNode(0)
        h2 = l2 = ListNode(0)
        while head:
            if head.val < x:
                l1.next = head
                l1 = l1.next
            else:
                l2.next = head
                l2 = l2.next
            head = head.next
        l2.next = None
        l1.next = h2.next
        return h1.next



