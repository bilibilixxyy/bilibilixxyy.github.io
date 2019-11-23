---
layout: post
title: 160. Intersection of Two Linked Lists
---
### Question
Write a program to find the node at which the intersection of two singly
linked lists begins.

For example, the following two linked lists:

[![](https://assets.leetcode.com/uploads/2018/12/13/160_statement.png)](https://assets.leetcode.com/uploads/2018/12/13/160_statement.png)

begin to intersect at node c1.



 **Example 1:**

[![](https://assets.leetcode.com/uploads/2018/12/13/160_example_1.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_1.png)

    
    
     **Input:** intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
    **Output:** Reference of the node with value = 8
    **Input Explanation:** The intersected node's value is 8 (note that this must not be 0 if the two lists intersect). From the head of A, it reads as [4,1,8,4,5]. From the head of B, it reads as [5,0,1,8,4,5]. There are 2 nodes before the intersected node in A; There are 3 nodes before the intersected node in B.



**Example 2:**

[![](https://assets.leetcode.com/uploads/2018/12/13/160_example_2.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_2.png)

    
    
    **Input:** intersectVal = 2, listA = [0,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
    **Output:** Reference of the node with value = 2
    **Input Explanation:**  The intersected node's value is 2 (note that this must not be 0 if the two lists intersect). From the head of A, it reads as [0,9,1,2,4]. From the head of B, it reads as [3,2,4]. There are 3 nodes before the intersected node in A; There are 1 node before the intersected node in B.
    



**Example 3:**

[![](https://assets.leetcode.com/uploads/2018/12/13/160_example_3.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_3.png)

    
    
    **Input:** intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
    **Output:** null
    **Input Explanation:** From the head of A, it reads as [2,6,4]. From the head of B, it reads as [1,5]. Since the two lists do not intersect, intersectVal must be 0, while skipA and skipB can be arbitrary values.
    **Explanation:** The two lists do not intersect, so return null.
    



 **Notes:**

  * If the two linked lists have no intersection at all, return `null`.
  * The linked lists must retain their original structure after the function returns.
  * You may assume there are no cycles anywhere in the entire linked structure.
  * Your code should preferably run in O(n) time and use only O(1) memory.

### Solution 1
I found most solutions here preprocess linkedlists to get the difference in
len.  
Actually we don't care about the "value" of difference, we just want to make
sure two pointers reach the intersection node at the same time.

We can use two iterations to do that. In the first iteration, we will reset
the pointer of one linkedlist to the head of another linkedlist after it
reaches the tail node. In the second iteration, we will move two pointers
until they points to the same node. Our operations in first iteration will
help us counteract the difference. So if two linkedlist intersects, the
meeting point in second iteration must be the intersection point. If the two
linked lists have no intersection at all, then the meeting pointer in second
iteration must be the tail node of both lists, which is null

Below is my commented Java code:

    
    
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        //boundary check
        if(headA == null || headB == null) return null;
        
        ListNode a = headA;
        ListNode b = headB;
        
        //if a & b have different len, then we will stop the loop after second iteration
        while( a != b){
        	//for the end of first iteration, we just reset the pointer to the head of another linkedlist
            a = a == null? headB : a.next;
            b = b == null? headA : b.next;    
        }
        
        return a;
    }


### Solution 2
    
    
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) 
    {
        ListNode *p1 = headA;
        ListNode *p2 = headB;
            
        if (p1 == NULL || p2 == NULL) return NULL;
    
        while (p1 != NULL && p2 != NULL && p1 != p2) {
            p1 = p1->next;
            p2 = p2->next;
    
            //
            // Any time they collide or reach end together without colliding 
            // then return any one of the pointers.
            //
            if (p1 == p2) return p1;
    
            //
            // If one of them reaches the end earlier then reuse it 
            // by moving it to the beginning of other list.
            // Once both of them go through reassigning, 
            // they will be equidistant from the collision point.
            //
            if (p1 == NULL) p1 = headB;
            if (p2 == NULL) p2 = headA;
        }
            
        return p1;
    }


### Solution 3
    
    
    class Solution:
        # @param two ListNodes
        # @return the intersected ListNode
        def getIntersectionNode(self, headA, headB):
            if headA is None or headB is None:
                return None
    
            pa = headA # 2 pointers
            pb = headB
    
            while pa is not pb:
                # if either pointer hits the end, switch head and continue the second traversal, 
                # if not hit the end, just move on to next
                pa = headB if pa is None else pa.next
                pb = headA if pb is None else pb.next
    
            return pa # only 2 ways to get out of the loop, they meet or the both hit the end=None
    
    # the idea is if you switch head, the possible difference between length would be countered. 
    # On the second traversal, they either hit or miss. 
    # if they meet, pa or pb would be the node we are looking for, 
    # if they didn't meet, they will hit the end at the same iteration, pa == pb == None, return either one of them is the same,None



