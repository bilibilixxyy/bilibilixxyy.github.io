---
layout: post
title: 206. Reverse Linked List
---
### Question
Reverse a singly linked list.

 **Example:**

    
    
     **Input:** 1- >2->3->4->5->NULL
    **Output:** 5- >4->3->2->1->NULL
    

**Follow up:**

A linked list can be reversed either iteratively or recursively. Could you
implement both?

### Solution 1
    
    
    public ListNode reverseList(ListNode head) {
        /* iterative solution */
        ListNode newHead = null;
        while (head != null) {
            ListNode next = head.next;
            head.next = newHead;
            newHead = head;
            head = next;
        }
        return newHead;
    }
    
    public ListNode reverseList(ListNode head) {
        /* recursive solution */
        return reverseListInt(head, null);
    }
    
    private ListNode reverseListInt(ListNode head, ListNode newHead) {
        if (head == null)
            return newHead;
        ListNode next = head.next;
        head.next = newHead;
        return reverseListInt(next, head);
    }


### Solution 2
[此题收录在Github](https://github.com/yuzhoujr/leetcode/issues/33)

## Iterative:

设置一个Prev的参数方便之后的操作。

###  **Step 1: 保存`head` reference**

在While循环中，cur指针用来保留当前Head的位置，因为如果我们操作 `head = head.next`这一步并且没有对head的位置进行保留，
我们会失去对`head`的reference，导致我们之后不能进行反转操作。

###  **Step 2: 保存`head.next`的reference**

`head`的reference存好以后，我们还需要保存`head.next`的reference，原因是我们如果对第一个node进行了反转操作，node就指向我们之前定义好的`prev`上，而失去了对原先`head.next`这个位置的拥有权。

`head.next`这个reference，我们直接用`head`来保存即可，所以有了`head =
head.next`这么一个操作。当然你要是想写的更加易懂，你也可以直接新创建一个函数，取名`next`，然后指向`next = head.next`。

###  **Step 3: 反转**

万事俱备，可以对`cur`指针进行反转了，指向之前定义的prev。

###  **Step 4: Reference转移**

最后不要忘记移动prev到cur的位置，不然prev的位置永远不变

    
    
    class Solution:
        def reverseList(self, head):
            prev = None
            while head:
                cur = head
                head = head.next
                cur.next = prev
                prev = cur
            return prev
                
    

  

## Iterative(保持Head位置)

底下这种写法，保证了Head的位置不被移动，这种操作对于该题的定义毫无意义，因为最终不需要head的reference，但如果API定义需要head的话，是个好的practice.  
还有就是对prev和next两个指针的命名，提高了code的可读性。

    
    
    class Solution(object):
        def reverseList(self, head):
            if not head: return None
            prev, cur = None, head
            
            while cur:
                next = cur.next
                cur.next = prev
                prev = cur
                cur = next
            return prev   
    

  

## Recursive

    
    
    class Solution:
        def reverseList(self, head):
            if not head or not head.next:
                return head
            
            new_head = self.reverseList(head.next)
            next_node = head.next    #        head -> next_node 
            next_node.next = head    #        head <- next_node 
            head.next = None         # [x] <- head <- next_node 
            return new_head
    

  

### Step 1: Base Case:

    
    
    if not head or not head.next:
        return head
    

Base Case的返回条件是当head或者head.next为空，则开始返回

  

### Step 2: Recurse到底

`new_head = self.reverseList(head.next)`  
在做任何处理之前，我们需要不断的递归，直到触及到Base Case,。当我们移动到最后一个Node以后, 将这个`Node`定义为我们新的`head`,
取名`new_head`. 我们从`new_head`开始重复性的往前更改指针的方向

  

### Step 3: 返回并且更改指针方向

    
    
    next_node = head.next    #        head -> next_node 
    next_node.next = head    #        head <- next_node 
    head.next = None         # [x] <- head <- next_node 
    

![](https://raw.githubusercontent.com/yuzhoujr/spazzatura/master/img_box/reversell.jpg)

这里一定要注意了，我们每次往上返回的是`new_head`, 这个`new_head`指针指向的是最后的Node.
而我们再返回过程中实际操作的`head`，是在`step2: Recurse到底`这一步向下传递的`head`的指针, 不要搞混了 ,
实在不懂，就把上面这个图画一下，走一遍

  
  
  
  

## Follow up: Reverse Nodes in K-Group

利用上面这种保持Head位置的写法，可以直接将方程带入到这道题常问的Follow Up  
也就是Leetcode 25的原题，代码也放在这

含义就是我们找到每次新的head的位置，然后通过我们这题写好的逻辑翻转即可。

    
    
    class Solution(object):
        def reverseKGroup(self, head, k):
            count, node = 0, head
            while node and count < k:
                node = node.next
                count += 1
            if count < k: return head
            new_head, prev = self.reverse(head, count)
            head.next = self.reverseKGroup(new_head, k)
            return prev
        
        def reverse(self, head, count):
            prev, cur, nxt = None, head, head
            while count > 0:
                nxt = cur.next
                cur.next = prev
                prev = cur
                cur = nxt
                count -= 1
            return (cur, prev)
    


### Solution 3
Well, since the `head` pointer may also be modified, we create a `pre` that
points to it to facilitate the reverse process.

For the example list `1 -> 2 -> 3 -> 4 -> 5` in the problem statement, it will
become `0 -> 1 -> 2 -> 3 -> 4 -> 5` (we init `pre -> val` to be `0`). We also
set a pointer `cur` to `head`. Then we keep inserting `cur -> next` after
`pre` until `cur` becomes the last node. This idea uses three pointers (`pre`,
`cur` and `temp`). You may implement it as follows.

    
    
    class Solution {
    public:
        ListNode* reverseList(ListNode* head) {
            ListNode *pre = new ListNode(0), *cur = head;
            pre -> next = head;
            while (cur && cur -> next) {
                ListNode* temp = pre -> next;
                pre -> next = cur -> next;
                cur -> next = cur -> next -> next;
                pre -> next -> next = temp;
            }
            return pre -> next;
        }
    };
    

Or

    
    
    class Solution {
    public:
        ListNode* reverseList(ListNode* head) {
            ListNode *pre = new ListNode(0), *cur = head;
            pre -> next = head;
            while (cur && cur -> next) {
                ListNode* temp = cur -> next;
                cur -> next = temp -> next;
                temp -> next = pre -> next;
                pre -> next = temp;
            }
            return pre -> next;
        }
    };
    

We can even use fewer pointers. The idea is to reverse one node at a time from
the beginning of the list.

    
    
    class Solution {
    public:
        ListNode* reverseList(ListNode* head) {
            ListNode* cur = NULL;
            while (head) {
                ListNode* next = head -> next;
                head -> next = cur;
                cur = head;
                head = next;
            }
            return cur;
        }
    };
    

All the above solutions are iterative. A recursive one is as follows. First
reverse all the nodes after `head`. Then we need to set `head` to be the final
node in the reversed list. We simply set its next node in the original list
(`head -> next`) to point to it and sets its `next` to `NULL`.

    
    
    class Solution {
    public:
        ListNode* reverseList(ListNode* head) {
            if (!head || !(head -> next)) {
                return head;
            }
            ListNode* node = reverseList(head -> next);
            head -> next -> next = head;
            head -> next = NULL;
            return node;
        }
    };
    



