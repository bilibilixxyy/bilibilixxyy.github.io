---
layout: post
title: 725. Split Linked List in Parts
---
### Question
Given a (singly) linked list with head node `root`, write a function to split
the linked list into `k` consecutive linked list "parts".

The length of each part should be as equal as possible: no two parts should
have a size differing by more than 1. This may lead to some parts being null.

The parts should be in order of occurrence in the input list, and parts
occurring earlier should always have a size greater than or equal parts
occurring later.

Return a List of ListNode's representing the linked list parts that are
formed.

Examples1->2->3->4, k = 5 // 5 equal parts[ [1], [2],[3],[4],null ]

 **Example 1:**  

    
    
     **Input:** root = [1, 2, 3], k = 5 **Output:** [[1],[2],[3],[],[]] **Explanation:** The input and each element of the output are ListNodes, not arrays.For example, the input root has root.val = 1, root.next.val = 2, \root.next.next.val = 3, and root.next.next.next = null.The first element output[0] has output[0].val = 1, output[0].next = null.The last element output[4] is null, but it's string representation as a ListNode is [].

 **Example 2:**  

    
    
     **Input:** 
    root = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10], k = 3
    **Output:** [[1, 2, 3, 4], [5, 6, 7], [8, 9, 10]]
    **Explanation:**
    The input has been split into consecutive parts with size difference at most 1, and earlier parts are a larger size than the later parts.
    

**Note:**

* The length of `root` will be in the range `[0, 1000]`.
* Each value of a node in the input will be an integer in the range `[0, 999]`.
* `k` will be an integer in the range `[1, 50]`.

### Solution 1
 **Java**

    
    
    class Solution {
        public ListNode[] splitListToParts(ListNode root, int k) {
            ListNode[] parts = new ListNode[k];
            int len =  0;
            for (ListNode node = root; node != null; node = node.next)
                len++;
            int n = len / k, r = len % k; // n : minimum guaranteed part size; r : extra nodes spread to the first r parts;
            ListNode node = root, prev = null;
            for (int i = 0; node != null && i < k; i++, r--) {
                parts[i] = node;
                for (int j = 0; j < n + (r > 0 ? 1 : 0); j++) {
                    prev = node;
                    node = node.next;
                }
                prev.next = null;
            }
            return parts;        
        }
    }
    

**C++**

    
    
    class Solution {
    public:
        vector <ListNode*> splitListToParts(ListNode* root, int k) {
            vector<ListNode*> parts(k, nullptr);
            int len = 0;
            for (ListNode* node = root; node; node = node->next)
                len++;
            int n = len / k, r = len % k; // n : minimum guaranteed part size; r : extra nodes spread to the first r parts;
            ListNode* node = root, *prev = nullptr;
            for (int i = 0; node && i < k; i++, r--) {
                parts[i] = node;
                for (int j = 0; j < n + (r > 0); j++) {
                    prev = node;
                    node = node->next;
                }
                prev->next = nullptr;
            }
            return parts;
        }
    };
    


### Solution 2
This question can be split into two parts:

  1. Count the length of the linked list
  2. Determine the length of nodes in each chunk
  3. Splitting the linked list up

At the end of step 2, `res` will look something like this, for a list of
length 10 and k of 3: `[4, 4, 3]`.

Step 3 iterates through `res` using the values in `res` and replaces the value
at each index with each chunk's head node. We have to keep a reference to
`prev` in order to slice up the chunks nicely by setting `prev.next = None`.

 _\- Yangshun_

    
    
    class Solution(object):
        def splitListToParts(self, root,  k):
            # Count the length of the linked list
            curr, length = root, 0
            while curr:
                curr, length = curr.next, length + 1
            # Determine the length of each chunk
            chunk_size, longer_chunks = length // k, length % k
            res = [chunk_size + 1] * longer_chunks + [chunk_size] * (k - longer_chunks)
            # Split up the list
            prev, curr = None, root
            for index, num in enumerate(res):
                if prev:
                    prev.next = None
                res[index] = curr
                for i in range(num):
                    prev, curr = curr, curr.next
            return res
    


### Solution 3
    
    
    class Solution {
    public:
        vector<ListNode*> splitListToParts(ListNode* root, int k) {
            int len = 0;
            for (ListNode *x = root; x; ++len, x = x->next);
    
            int n = len / k, r = len % k;
            vector<ListNode*> ret(k);
            for (int i = 0; i < k && root; ++i) {
                ret[i] = root;
                for (int j = 1; j < n + (i < r); ++j)
                    root = root->next;
                ListNode *next = root->next;
                root->next = nullptr;
                root = next;
            }
            return ret;
        }
    };
    



