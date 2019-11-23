---
layout: post
title: 141. Linked List Cycle
---
### Question
Given a linked list, determine if it has a cycle in it.

To represent a cycle in the given linked list, we use an integer `pos` which
represents the position (0-indexed) in the linked list where tail connects to.
If `pos` is `-1`, then there is no cycle in the linked list.



 **Example 1:**

    
    
     **Input:** head = [3,2,0,-4], pos = 1
    **Output:** true
    **Explanation:** There is a cycle in the linked list, where tail connects to the second node.
    

![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png)

**Example 2:**

    
    
    **Input:** head = [1,2], pos = 0
    **Output:** true
    **Explanation:** There is a cycle in the linked list, where tail connects to the first node.
    

![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test2.png)

**Example 3:**

    
    
    **Input:** head = [1], pos = -1
    **Output:** false
    **Explanation:** There is no cycle in the linked list.
    

![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test3.png)



 **Follow up:**

Can you solve it using _O(1)_ (i.e. constant) memory?

### Solution 1
    
    
    public boolean hasCycle(ListNode head) {
        if(head==null) return false;
        ListNode walker = head;
        ListNode runner = head;
        while(runner.next!=null && runner.next.next!=null) {
            walker = walker.next;
            runner = runner.next.next;
            if(walker==runner) return true;
        }
        return false;
    }
    

  1. Use two pointers, **walker** and **runner**.
  2.  **walker** moves step by step. **runner** moves two steps at time.
  3. if the Linked List has a cycle **walker** and **runner** will meet at some  
point.


### Solution 2
Took 88 ms and the "Accepted Solutions Runtime Distribution" doesn't show any
faster Python submissions. The "trick" is to not check all the time whether we
have reached the end but to handle it via an exception. ["Easier to ask for
forgiveness than permission."](https://docs.python.org/3/glossary.html#term-
eafp)

The algorithm is of course [Tortoise and
hare](https://en.wikipedia.org/wiki/Cycle_detection#Tortoise_and_hare).

    
    
    def hasCycle(self, head):
        try:
            slow = head
            fast = head.next
            while slow is not fast:
                slow = slow.next
                fast = fast.next.next
            return True
        except:
            return False


### Solution 3
    
    
    /**
     * Definition for singly-linked list.
     * struct ListNode {
     *     int val;
     *     ListNode *next;
     *     ListNode(int x) : val(x), next(NULL) {}
     * };
     */
     /**
     use faster and lower runner solution. (2 pointers)
     the faster one move 2 steps, and slower one move only one step.
     if there's a circle, the faster one will finally "catch" the slower one. 
     (the distance between these 2 pointers will decrease one every time.)
     
     if there's no circle, the faster runner will reach the end of linked list. (NULL)
     */
    class Solution {
    public:
        bool hasCycle(ListNode *head) {
            if(head == NULL || head -> next == NULL)    
                return false;
     
            ListNode *fast = head;
            ListNode *slow = head;
            
            while(fast -> next && fast -> next -> next){
                fast = fast -> next -> next;
                slow = slow -> next;
                if(fast == slow)
                    return true;
            }
     
            return false;
        }
    };



