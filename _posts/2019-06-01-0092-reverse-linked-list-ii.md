---
layout: post
title: 92. Reverse Linked List II
---
### Question
Reverse a linked list from position _m_ to _n_. Do it in one-pass.

 **Note:  **1 ≤ _m_ ≤ _n_ ≤ length of list.

 **Example:**

    
    
     **Input:** 1- >2->3->4->5->NULL, _m_ = 2, _n_ = 4
    **Output:** 1- >4->3->2->5->NULL
    

### Solution 1
Simply just reverse the list along the way using 4 pointers: dummy, pre,
start, then

    
    
    public ListNode reverseBetween(ListNode head, int m, int n) {
        if(head == null) return null;
        ListNode dummy = new ListNode(0); // create a dummy node to mark the head of this list
        dummy.next = head;
        ListNode pre = dummy; // make a pointer pre as a marker for the node before reversing
        for(int i = 0; i<m-1; i++) pre = pre.next;
        
        ListNode start = pre.next; // a pointer to the beginning of a sub-list that will be reversed
        ListNode then = start.next; // a pointer to a node that will be reversed
        
        // 1 - 2 -3 - 4 - 5 ; m=2; n =4 ---> pre = 1, start = 2, then = 3
        // dummy-> 1 -> 2 -> 3 -> 4 -> 5
        
        for(int i=0; i<n-m; i++)
        {
            start.next = then.next;
            then.next = pre.next;
            pre.next = then;
            then = start.next;
        }
        
        // first reversing : dummy->1 - 3 - 2 - 4 - 5; pre = 1, start = 2, then = 4
        // second reversing: dummy->1 - 4 - 3 - 2 - 5; pre = 1, start = 2, then = 5 (finish)
        
        return dummy.next;
        
    }


### Solution 2
On my way of self-learning, `linked list` was always a nightmare to me. Hopes
the code and this graph (ugh, kind of ugly maybe) can help someone who was
just like me.

    
    
    class Solution(object):
        def reverseBetween(self, head, m, n):
            """
            :type head: ListNode
            :type m: int
            :type n: int
            :rtype: ListNode
            """
            if not head or m == n: return head
            p = dummy = ListNode(None)
            dummy.next = head
            for i in range(m-1): p = p.next
            tail = p.next
    
            for i in range(n-m):
                tmp = p.next                  # a)
                p.next = tail.next            # b)
                tail.next = tail.next.next    # c)
                p.next.next = tmp             # d)
            return dummy.next
    

![0_1490008790876_reversed_linked_list.jpeg](/uploads/files/1490008792563-reversed_linked_list.jpeg)


### Solution 3
First locate the node before the `m`-th node (`pre`) and the `m`-th node
(`cur`). Then move`cur -> next` to be after `pre` for `n - m` times.

    
    
    class Solution {
    public:
       ListNode* reverseBetween(ListNode* head, int m, int n) {
           ListNode *dummy = new ListNode(0), *pre = dummy, *cur;
           dummy -> next = head;
           for (int i = 0; i < m - 1; i++) {
               pre = pre -> next;
           }
           cur = pre -> next;
           for (int i = 0; i < n - m; i++) {
               ListNode* temp = pre -> next;
               pre -> next = cur -> next;
               cur -> next = cur -> next -> next;
               pre -> next -> next = temp;
           }
           return dummy -> next;
       }
    };
    



