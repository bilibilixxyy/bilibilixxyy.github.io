---
layout: post
title: 908. Middle of the Linked List
---
### Question
Given a non-empty, singly linked list with head node `head`, return a middle
node of linked list.

If there are two middle nodes, return the second middle node.



 **Example 1:**

    
    
     **Input:** [1,2,3,4,5]
    **Output:** Node 3 from this list (Serialization: [3,4,5])
    The returned node has value 3.  (The judge's serialization of this node is [3,4,5]).
    Note that we returned a ListNode object ans, such that:
    ans.val = 3, ans.next.val = 4, ans.next.next.val = 5, and ans.next.next.next = NULL.
    

**Example 2:**

    
    
    **Input:** [1,2,3,4,5,6]
    **Output:** Node 4 from this list (Serialization: [4,5,6])
    Since the list has two middle nodes with values 3 and 4, we return the second one.
    



 **Note:**

  * The number of nodes in the given list will be between `1` and `100`.

### Solution 1
Each time, `slow` go 1 steps while `fast` go 2 steps.  
When `fast` arrives at the end, `slow` will arrive right in the middle.

 **C++:**

    
    
        ListNode* middleNode(ListNode* head) {
            ListNode *slow = head, *fast = head;
             while (fast && fast->next)
                slow = slow->next, fast = fast->next->next;
            return slow;
        }
    

**Java:**

    
    
         public ListNode middleNode(ListNode head) {
            ListNode slow = head, fast = head;
            while (fast != null && fast.next != null) {
                slow = slow.next;
                fast = fast.next.next;
            }
            return slow;
        }
    

**Python:**

    
    
         def middleNode(self, head):
            slow = fast = head
            while fast and fast.next:
                slow = slow.next
                fast = fast.next.next
            return slow
    


### Solution 2
For those who read it, I wish you good luck and all the best for your
interviews.

    
    
    class Solution {
    public:
        ListNode* middleNode(ListNode* head) {
    
            ListNode* fast = head;
            ListNode* slow = fast;
    
            while (fast && fast->next) {
                slow = slow->next;
                fast = fast->next->next;
            }
    
            return slow;        
        }
    };
    
    
    


### Solution 3
    
    
    def middleNode(self, head):
        tmp = head
        while tmp and tmp.next:
            head = head.next
            tmp = tmp.next.next
        return head
    

We need two pointers, one is head with one step each iteration, and the other
is tmp with two steps each iteration. So when the tmp reaches the end of the
list, the head just reaches the half of it, which is exactly what we want.

And one thing needs to be considered additionaly is that the number of the
node can be odd and even, which may affect the termination condition of the
iteration. To solve this, my idea is to try the algorithm in some small set of
the examples, like the examples provided by the official. And you will find
that if the tmp reaches Null or tmp.next reaches Null, the head is the result.



