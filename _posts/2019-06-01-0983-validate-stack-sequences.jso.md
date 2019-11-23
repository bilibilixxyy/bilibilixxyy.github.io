---
layout: post
title: 983. Validate Stack Sequences
---
### Question
Given two sequences `pushed` and `popped`  **with distinct values** , return
`true` if and only if this could have been the result of a sequence of push
and pop operations on an initially empty stack.



 **Example 1:**

    
    
     **Input:** pushed = [1,2,3,4,5], popped = [4,5,3,2,1]
    **Output:** true
    **Explanation:** We might do the following sequence:
    push(1), push(2), push(3), push(4), pop() -> 4,
    push(5), pop() -> 5, pop() -> 3, pop() -> 2, pop() -> 1
    

**Example 2:**

    
    
    **Input:** pushed = [1,2,3,4,5], popped = [4,3,5,1,2]
    **Output:** false
    **Explanation:** 1 cannot be popped before 2.
    



 **Note:**

  1. `0 <= pushed.length == popped.length <= 1000`
  2. `0 <= pushed[i], popped[i] < 1000`
  3. `pushed` is a permutation of `popped`.
  4. `pushed` and `popped` have distinct values.

### Solution 1
Simulate stack operations:  
Loop through the `pushed` array,

  1. Keep pushing `pushed` elements into stack if the top element on the stack is different from the current one of `popped`;
  2. Keep poping out of the top element if it is same as the current one of `popped`;
  3. Check if the stack is empty after loop.

 **Analysis:**  
Let `n` be `pushed.length`  
the while loop at most run `n` times since the stack at most pop out `n`
times.  
 **Time & space: O(n).**

    
    
        public boolean validateStackSequences(int[] pushed, int[] popped) {
            Stack<Integer> stk = new Stack<>();
            int i = 0;
            for (int p : pushed) {
                stk.push(p);
                while (!stk.isEmpty() && stk.peek() == popped[i]) {
                    stk.pop();
                    ++i;
                }
            }
            return stk.empty();
        }
    


### Solution 2
**C++:**

    
    
         bool validateStackSequences(vector<int>& pushed, vector<int>& popped) {
            stack<int> s;
            int i = 0;
            for (int x : pushed) {
                s.push(x);
                while (s.size() && s.top() == popped[i]) {
                    s.pop();
                    i++;
                }
            }
            return i == popped.size();
        }
    

**Java:**

    
    
         public boolean validateStackSequences(int[] pushed, int[] popped) {
            Stack<Integer> s = new Stack<>();
            int i = 0;
            for (int x : pushed) {
                s.push(x);
                while (!s.empty() && s.peek() == popped[i]) {
                    s.pop();
                    i++;
                }
            }
            return i == popped.length;
        }
    

**Python:**

    
    
        def validateStackSequences( self, pushed, popped):
            cur = []
            i = 0
            for x in pushed:
                cur.append(x)
                while cur and cur[-1] == popped[i]:
                    i += 1
                    cur.pop()
            return i == len(popped)
    


### Solution 3
    
    
    class Solution {
        public boolean validateStackSequences(int[] pushed, int[] popped) {
            Deque<Integer> stack = new ArrayDeque<>();
            int j = 0;
            for (int x : pushed) {
                stack.push(x);
                while (stack.size() > 0 && stack.peek() == popped[j]) {
                    stack.pop();
                    j++;
                }
            }
            return stack.size() == 0;
        }
    }
    



