---
layout: post
title: 886. Score of Parentheses
---
### Question
Given a balanced parentheses string `S`, compute the score of the string based
on the following rule:

  * `()` has score 1
  * `AB` has score `A + B`, where A and B are balanced parentheses strings.
  * `(A)` has score `2 * A`, where A is a balanced parentheses string.



 **Example 1:**

    
    
     **Input:** "()"
    **Output:** 1
    

**Example 2:**

    
    
    **Input:** "(())"
    **Output:** 2
    

**Example 3:**

    
    
    **Input:** "()()"
    **Output:** 2
    

**Example 4:**

    
    
    **Input:** "(()(()))"
    **Output:** 6
    



 **Note:**

  1. `S` is a balanced parentheses string, containing only `(` and `)`.
  2. `2 <= S.length <= 50`

### Solution 1
##  **Approach 0: Stack**

`cur` record the score at the current layer level.

If we meet `'('`,  
we push the current score to `stack`,  
enter the next inner layer level,  
and reset `cur = 0`.

If we meet `')'`,  
the `cur` score will be doubled and will be at least 1.  
We exit the current layer level,  
and set `cur = stack.pop() + cur`

Complexity: `O(N)` time and `O(N)` space

 **Java**

    
    
         public int scoreOfParentheses(String S) {
            Stack<Integer> stack = new Stack<>();
            int cur = 0;
            for (char c : S.toCharArray()) {
                if (c == '(') {
                    stack.push(cur);
                    cur = 0;
                } else {
                    cur = stack.pop() + Math.max(cur * 2, 1);
                }
            }
            return cur;
        }
    

**C++**

    
    
         int scoreOfParentheses(string S) {
            stack<int> stack;
            int cur = 0;
            for (char i : S)
                if (i == '(') {
                    stack.push(cur);
                    cur = 0;
                }
                else {
                    cur += stack.top() + max(cur, 1);
                    stack.pop();
                }
            return cur;
        }
    

**Python**

    
    
        def scoreOfParentheses(self, S):
             stack, cur = [], 0
            for i in S:
                if i == '(':
                    stack.append(cur)
                    cur = 0
                else:
                    cur += stack.pop() + max(cur, 1)
            return cur
    

  

## **Approach 1: Array**

Same as stack, I do it with an array.  
We change a pointer instead of pushing/popping repeatedly.

Complexity: `O(N)` time and `O(N)` space

**Java:**

    
    
         public int scoreOfParentheses(String S) {
            int res[] = new int[30], i = 0;
            for (char c : S.toCharArray())
                if (c == '(') res[++i] = 0;
                else res[i - 1] += Math.max(res[i--] * 2, 1);
            return res[0];
        }
    

**C++:**

    
    
         int scoreOfParentheses(string S) {
            int res[30] = {0}, i = 0;
            for (char c : S)
                if (c == '(') res[++i] = 0;
                else res[i - 1] += max(res[i] * 2, 1), i--;
            return res[0];
        }
    

**Python:**

    
    
        def scoreOfParentheses(self, S):
            res, i = [ 0] * 30, 0
            for c in S:
                i += 1 if c == '(' else -1
                res[i] = res[i] + max(res[i + 1] * 2, 1) if c == ')' else 0
            return res[0]
    

  

## **Follow-Up**

Can you solve it in `O(1)` space?

  

## **Approach 2: O(1) Space**

We count the number of layers.  
If we meet `'('` layers number `l++`  
else we meet `')'` layers number `l--`

If we meet `"()"`, we know the number of layer outside,  
so we can calculate the score `res += 1 << l`.

**C++:**

    
    
        int scoreOfParentheses(string S) {
            int res =  0, l = 0;
            for (int i = 0; i < S.length(); ++i) {
                if (S[i] == '(') l++; else l--;
                if (S[i] == ')' && S[i - 1] == '(') res += 1 << l;
            }
            return res;
        }
    

**Java:**

    
    
        public int scoreOfParentheses(String S) {
            int res =  0, l = 0;
            for (int i = 0; i < S.length(); ++i) {
                if (S.charAt(i) == '(') l++; else l--;
                if (S.charAt(i) == ')' && S.charAt(i - 1) == '(') res += 1 << l;
            }
            return res;
        }
    

**Python:**

    
    
         def scoreOfParentheses(self, S):
            res = l = 0
            for a, b in itertools.izip(S, S[1:]):
                if a + b == '()': res += 2 ** l
                l += 1 if a == '(' else -1
            return res
    


### Solution 2
    
    
    class Solution {
        public int scoreOfParentheses(String S) {
            Stack<Integer> stack = new Stack<>();
            for (char c : S.toCharArray()) {
                if (c == '(') {
                    stack.push(-1);
                } else {
                    int cur = 0;
                    while (stack.peek() != -1) {
                        cur += stack.pop();
                    }
                    stack.pop();
                    stack.push(cur == 0 ? 1 : cur * 2);
                }
            }
            int sum = 0;
            while (!stack.isEmpty()) {
                sum += stack.pop();
            }
            return sum;
        }
    }
    


### Solution 3
    
    
        def scoreOfParentheses(self, S):
            return eval(S.replace(')(', ')+(').replace('()', '1').replace(')', ')*2'))
    



