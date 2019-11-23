---
layout: post
title: 1072. Next Greater Node In Linked List
---
### Question
We are given a linked list with `head` as the first node.  Let's number the
nodes in the list: `node_1, node_2, node_3, ...` etc.

Each node may have a _next larger_ **value** : for `node_i`,
`next_larger(node_i)` is the `node_j.val` such that `j > i`, `node_j.val >
node_i.val`, and `j` is the smallest possible choice.  If such a `j` does not
exist, the next larger value is `0`.

Return an array of integers `answer`, where `answer[i] =
next_larger(node_{i+1})`.

Note that in the example **inputs**  (not outputs) below, arrays such as
`[2,1,5]` represent the serialization of a linked list with a head node value
of 2, second node value of 1, and third node value of 5.



 **Example 1:**

    
    
     **Input:** [2,1,5]
    **Output:** [5,5,0]
    

**Example 2:**

    
    
    **Input:** [2,7,4,3,5]
    **Output:** [7,0,5,5,0]
    

**Example 3:**

    
    
    **Input:** [1,7,5,1,9,2,5,1]
    **Output:** [7,9,9,9,0,5,0,0]
    



 **Note:**

  1. ` 1 <= node.val <= 10^9` for each node in the linked list.
  2. The given list has length in the range `[0, 10000]`.

### Solution 1
##  **Intuition** :

Very similar to this problem [503\. Next Greater Element
II](https://leetcode.com/problems/next-greater-element-ii/discuss/98270/)  
  

##  **Time Complexity** :

O(N) Time, O(N) Space  
  
  
 **Java:**  
Transform the linked list to an arraylist,  
then it's a normal "next larger element" problem,  
solved by stack.

    
    
        public int[] nextLargerNodes(ListNode head) {
            ArrayList<Integer> A = new ArrayList<>();
            for (ListNode node = head; node != null; node = node.next)
                A.add(node.val);
            int[] res = new int[A.size()];
            Stack<Integer> stack = new Stack<>();
            for (int i = 0; i < A.size(); ++i) {
                while (!stack.isEmpty() && A.get(stack.peek()) < A.get(i))
                    res[stack.pop()] = A.get(i);
                stack.push(i);
            }
            return res;
        }
    

**C++:**  
Push nodes' values to `vector<int> res`.  
`vector<int> stack` will save the indices of elements that need to find next
greater element.  
In the end, we reset 0 to all elements that have no next greater elements.

    
    
        vector <int> nextLargerNodes(ListNode* head) {
            vector<int> res, stack;
            for (ListNode* node = head; node; node = node->next) {
                while (stack.size() && res[stack.back()] < node->val) {
                    res[stack.back()] = node->val;
                    stack.pop_back();
                }
                stack.push_back(res.size());
                res.push_back(node->val);
            }
            for (int i: stack) res[i] = 0;
            return res;
        }
    

**Python:**  
Save  <index, value> pair to the stack.

    
    
        def nextLargerNodes(self, head):
            res, stack = [], []
            while head:
                while stack and stack[-1][1] < head.val:
                    res[stack.pop()[0]] = head.val
                stack.append([len(res), head.val])
                res.append(0)
                head = head.next
            return res
    


### Solution 2
# Intuition

We can convert list into the array (we need to return an array anyways). So,
this problem becomes "Next greater element". Search on YouTube for this title,
there are tons of videos there.

# Solution

We go right-to-left, and maintain the monotonically decreasing stack:

  * We remove elements from the stack until the top of the stack is larger than the current element.
    * We do not need those elements - the current value will be used instead for the remaining elements.
  * The top of the stack is now our next greater element.
  * We push the current element to the stack.

    
    
    vector<int> nextLargerNodes(ListNode* h) {
      vector<int> res, stack;
      for (auto p = h; p != nullptr; p = p->next) res.push_back(p->val);
      for (int i = res.size() - 1; i >= 0; --i) {
        auto val = res[i];
        while (!stack.empty() && stack.back() <= res[i]) stack.pop_back();
        res[i] = stack.empty() ? 0 : stack.back();
        stack.push_back(val);
      }
      return res;
    }
    

## Complexity Analysis

Runtime: _O(n)_. We process each input element no more than 2 times.  
Memory: _O(n)_. We use up to _n_ memory for the stack.


### Solution 3
    
    
     int[] res;
    
    public int[] nextLargerNodes(ListNode head) {
        calNode(head, 0, new Stack<>());
        return res;
    }
    
    public void calNode(ListNode node, int index, Stack<Integer> stack) {
        if(node == null) {
            res = new int[index];
            return;
        }
        calNode(node.next, index + 1, stack);
        while(!stack.empty() && stack.peek() <= node.val)
            stack.pop();
        res[index] = stack.empty() ? 0 : stack.peek();
        stack.push(node.val);
    }
    



