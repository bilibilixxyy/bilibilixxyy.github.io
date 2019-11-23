---
layout: post
title: 147. Insertion Sort List
---
### Question
Sort a linked list using insertion sort.

![](https://upload.wikimedia.org/wikipedia/commons/0/0f/Insertion-sort-
example-300px.gif)  
A graphical example of insertion sort. The partial sorted list (black)
initially contains only the first element in the list.  
With each iteration one element (red) is removed from the input data and
inserted in-place into the sorted list  


 **Algorithm of Insertion Sort:**

  1. Insertion sort iterates, consuming one input element each repetition, and growing a sorted output list.
  2. At each iteration, insertion sort removes one element from the input data, finds the location it belongs within the sorted list, and inserts it there.
  3. It repeats until no input elements remain.

  
 **Example 1:**

    
    
     **Input:** 4- >2->1->3
    **Output:** 1- >2->3->4
    

**Example 2:**

    
    
    **Input:** -1- >5->3->4->0
    **Output:** -1- >0->3->4->5
    

### Solution 1
    
    
    public ListNode insertionSortList(ListNode head) {
    		if( head == null ){
    			return head;
    		}
    		
    		ListNode helper = new ListNode(0); //new starter of the sorted list
    		ListNode cur = head; //the node will be inserted
    		ListNode pre = helper; //insert node between pre and pre.next
    		ListNode next = null; //the next node will be inserted
    		//not the end of input list
    		while( cur != null ){
    			next = cur.next;
    			//find the right place to insert
    			while( pre.next != null && pre.next.val < cur.val ){
    				pre = pre.next;
    			}
    			//insert between pre and pre.next
    			cur.next = pre.next;
    			pre.next = cur;
    			pre = helper;
    			cur = next;
    		}
    		
    		return helper.next;
    	}


### Solution 2
One of the quotes is

> For God's sake, don't try sorting a linked list during the interview

<http://steve-yegge.blogspot.nl/2008/03/get-that-job-at-google.html>

So it might be better to actually copy the values into an array and sort them
there.


### Solution 3
Keep a sorted partial list (`head`) and start from the second node (`head ->
next`), each time when we see a node with `val` smaller than its previous
node, we scan from the `head` and find the position that the node should be
inserted. Since a node may be inserted before `head`, we create a `dummy` head
that points to `head`.

    
    
    /**
     * Definition for singly-linked list.
     * struct ListNode {
     *     int val;
     *     ListNode *next;
     *     ListNode(int x) : val(x), next(NULL) {}
     * };
     */
    class Solution {
    public:
        ListNode* insertionSortList(ListNode* head) {
            ListNode* dummy = new ListNode(0);
            dummy -> next = head;
            ListNode *pre = dummy, *cur = head;
            while (cur) {
                if ((cur -> next) && (cur -> next -> val < cur -> val)) {
                    while ((pre -> next) && (pre -> next -> val < cur -> next -> val)) {
                        pre = pre -> next;
                    }
                    ListNode* temp = pre -> next;
                    pre -> next = cur -> next;
                    cur -> next = cur -> next -> next;
                    pre -> next -> next = temp;
                    pre = dummy;
                }
                else {
                    cur = cur -> next;
                }
            }
            return dummy -> next;
        }
    };
    



