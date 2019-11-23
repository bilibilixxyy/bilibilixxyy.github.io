---
layout: post
title: 445. Add Two Numbers II
---
### Question
You are given two **non-empty** linked lists representing two non-negative
integers. The most significant digit comes first and each of their nodes
contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the
number 0 itself.

 **Follow up:**  
What if you cannot modify the input lists? In other words, reversing the lists
is not allowed.

 **Example:**

    
    
     **Input:** (7 - > 2 -> 4 -> 3) + (5 -> 6 -> 4)
    **Output:** 7 - > 8 -> 0 -> 7
    

### Solution 1
    
    
     public class Solution {
        public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
            Stack<Integer> s1 = new Stack<Integer>();
            Stack<Integer> s2 = new Stack<Integer>();
            
            while(l1 != null) {
                s1.push(l1.val);
                l1 = l1.next;
            };
            while(l2 != null) {
                s2.push(l2.val);
                l2 = l2.next;
            }
            
            int sum = 0;
            ListNode list = new ListNode(0);
            while (!s1.empty() || !s2.empty()) {
                if (!s1.empty()) sum += s1.pop();
                if (!s2.empty()) sum += s2.pop();
                list.val = sum % 10;
                ListNode head = new ListNode(sum / 10);
                head.next = list;
                list = head;
                sum /= 10;
            }
            
            return list.val == 0 ? list.next : list;
        }
    }
    


### Solution 2
Idea is to reverse output instead of input. Not sure if this is cheating.

    
    
        ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
            int n1 = 0, n2 = 0, carry = 0;
            ListNode *curr1 = l1, *curr2 = l2, *res = NULL;
            while( curr1 ){ curr1=curr1->next; n1++; }
            while( curr2 ){ curr2=curr2->next; n2++; } 
            curr1 = l1; curr2 = l2;
            while( n1 > 0 && n2 > 0){
                int sum = 0;
                if( n1 >= n2 ){ sum += curr1->val; curr1=curr1->next; n1--;}
                if( n2 > n1 ){ sum += curr2->val; curr2=curr2->next; n2--;}
                res = addToFront( sum, res );
            }
            curr1 = res; res = NULL;
            while( curr1 ){
                curr1->val += carry; carry = curr1->val/10;
                res = addToFront( curr1->val%10, res );
                curr2 = curr1; 
                curr1 = curr1->next;
                delete curr2;
            }
            if( carry ) res = addToFront( 1, res );
            return res;
        }
        ListNode* addToFront( int val, ListNode* head ){
            ListNode* temp = new ListNode(val);
            temp->next = head;
            return temp;
        }
    


### Solution 3
Since there is no maximum of int in python, we can computer the sum and then
construct the result link list. Does this count as cheating?

    
    
    def addTwoNumbers(self, l1, l2):
    
            x1, x2 = 0, 0
            while l1:
                x1 = x1*10+l1.val
                l1 = l1.next
            while l2:
                x2 = x2*10+l2.val
                l2 = l2.next
            x = x1 + x2
            
            head = ListNode(0)
            if x == 0: return head
            while x:
                v, x = x%10, x//10
                head.next, head.next.next = ListNode(v), head.next
                
            return head.next
    



