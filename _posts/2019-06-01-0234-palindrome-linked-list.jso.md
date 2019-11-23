---
layout: post
title: 234. Palindrome Linked List
---
### Question
Given a singly linked list, determine if it is a palindrome.

 **Example 1:**

    
    
     **Input:** 1- >2
    **Output:** false

**Example 2:**

    
    
    **Input:** 1- >2->2->1
    **Output:** true

 **Follow up:**  
Could you do it in O(n) time and O(1) space?

### Solution 1
It is a common misunderstanding that the space complexity of a program is just
how much the size of additional memory space being used besides input. An
important prerequisite is neglected the above definition: [the input has to be
read-only](https://en.wikipedia.org/wiki/DSPACE#Machine_models). By
definition, changing the input and change it back is not allowed (or the input
size should be counted when doing so). Another way of determining the space
complexity of a program is to simply look at how much space it has written to.
Reversing a singly linked list requires writing to O(n) memory space, thus the
space complexities for all "reverse-the-list"-based approaches are O(n), not
O(1).

Solving this problem in O(1) space is theoretically impossible due to two
simple facts: (1) a program using O(1) space is computationally equivalent to
a finite automata, or a regular expression checker; (2) [the pumping
lemma](https://en.wikipedia.org/wiki/Pumping_lemma_for_regular_languages)
states that the set of palindrome strings does not form a regular set.

Please change the incorrect problem statement.


### Solution 2
This can be solved by reversing the 2nd half and compare the two halves. Let's
start with an example `[1, 1, 2, 1]`.

In the beginning, set two pointers `fast` and `slow` starting at the head.

    
    
    1 -> 1 -> 2 -> 1 -> null 
    sf
    

(1) **Move:** `fast` pointer goes to the end, and `slow` goes to the middle.

    
    
     1 -> 1 -> 2 -> 1 -> null 
              s          f
    

(2) **Reverse:** the right half is reversed, and `slow` pointer becomes the
2nd head.

    
    
     1 -> 1    null <- 2 <- 1           
    h                      s
    

(3) **Compare:** run the two pointers `head` and `slow` together and compare.

    
    
     1 -> 1    null <- 2 <- 1             
         h            s
    

* * *
    
    
    public boolean isPalindrome(ListNode head) {
        ListNode fast = head, slow = head;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }
        if (fast != null) { // odd nodes: let right half smaller
            slow = slow.next;
        }
        slow = reverse(slow);
        fast = head;
        
        while (slow != null) {
            if (fast.val != slow.val) {
                return false;
            }
            fast = fast.next;
            slow = slow.next;
        }
        return true;
    }
    
    public ListNode reverse(ListNode head) {
        ListNode prev = null;
        while (head != null) {
            ListNode next = head.next;
            head.next = prev;
            prev = head;
            head = next;
        }
        return prev;
    }


### Solution 3
O(n) time, O(1) space. The second solution restores the list after changing
it.

* * *

 **Solution 1: _Reversed first half == Second half?_**

Phase 1: Reverse the first half while finding the middle.  
Phase 2: Compare the reversed first half with the second half.

    
    
    def isPalindrome(self, head):
        rev = None
        slow = fast = head
        while fast and fast.next:
            fast = fast.next.next
            rev, rev.next, slow = slow, rev, slow.next
        if fast:
            slow = slow.next
        while rev and rev.val == slow.val:
            slow = slow.next
            rev = rev.next
        return not rev
    

* * *

**Solution 2: _Play Nice_**

Same as the above, but while comparing the two halves, restore the list to its
original state by reversing the first half back. Not that the OJ or anyone
else cares.

    
    
    def isPalindrome(self, head):
        rev = None
        fast = head
        while fast and fast.next:
            fast = fast.next.next
            rev, rev.next, head = head, rev, head.next
        tail = head.next if fast else head
        isPali = True
        while rev:
            isPali = isPali and rev.val == tail.val
            head, head.next, rev = rev, head, rev.next
            tail = tail.next
        return isPali



