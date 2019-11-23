---
layout: post
title: 19. Remove Nth Node From End of List
---
### Question
Given a linked list, remove the _n_ -th node from the end of list and return
its head.

 **Example:**

    
    
    Given linked list: **1- >2->3->4->5**, and **_n_ = 2**.
    
    After removing the second node from the end, the linked list becomes **1- >2->3->5**.
    

**Note:**

Given _n_ will always be valid.

 **Follow up:**

Could you do this in one pass?

### Solution 1
A one pass solution can be done using pointers. Move one pointer **fast** \--
> **n+1** places forward, to maintain a gap of n between the two pointers and
then move both at the same speed. Finally, when the fast pointer reaches the
end, the slow pointer will be **n+1** places behind - just the right spot for
it to be able to skip the next node.

Since the question gives that **n** is valid, not too many checks have to be
put in place. Otherwise, this would be necessary.

    
    
     public ListNode removeNthFromEnd(ListNode head, int n) {
        
        ListNode start = new ListNode(0);
        ListNode slow = start, fast = start;
        slow.next = head;
        
        //Move fast in front so that the gap between slow and fast becomes n
        for(int i=1; i<=n+1; i++)   {
            fast = fast.next;
        }
        //Move fast to the end, maintaining the gap
        while(fast != null) {
            slow = slow.next;
            fast = fast.next;
        }
        //Skip the desired node
        slow.next = slow.next.next;
        return start.next;
    }


### Solution 2
 **Value-Shifting - AC in 64 ms**

My first solution is "cheating" a little. Instead of really removing the nth
_node_ , I remove the nth _value_. I recursively determine the indexes
(counting from back), then shift the values for all indexes larger than n, and
then always drop the head.

    
    
    class Solution:
        def removeNthFromEnd(self, head, n):
            def index( node):
                if not node:
                    return 0
                i = index(node.next) + 1
                if i > n:
                    node.next.val = node.val
                return i
            index(head)
            return head.next
    

* * *

**Index and Remove - AC in 56 ms**

In this solution I recursively determine the indexes again, but this time my
helper function removes the nth node. It returns two values. The index, as in
my first solution, and the possibly changed head of the remaining list.

    
    
    class Solution:
        def removeNthFromEnd(self, head, n):
            def remove(head):
                if not head:
                    return 0, head
                i, head.next = remove(head.next)
                return i+1, (head, head.next)[i+1 == n]
            return remove(head)[1]
    

* * *

**n ahead - AC in 48 ms**

The standard solution, but without a dummy extra node. Instead, I simply
handle the special case of removing the head right after the fast cursor got
its head start.

    
    
    class Solution:
        def removeNthFromEnd(self, head, n):
            fast = slow = head
            for _ in range(n):
                fast = fast.next
            if not fast:
                return head.next
            while fast.next:
                fast = fast.next
                slow = slow.next
            slow.next = slow.next.next
            return head


### Solution 3
    
    
    class Solution
    {
    public:
        ListNode* removeNthFromEnd(ListNode* head, int n)
        {
            ListNode** t1 = &head, *t2 = head;
            for(int i = 1; i < n; ++i)
            {
                t2 = t2->next;
            }
            while(t2->next != NULL)
            {
                t1 = &((*t1)->next);
                t2 = t2->next;
            }
            *t1 = (*t1)->next;
            return head;
        }
    };



