---
layout: post
title: 957. Minimum Add to Make Parentheses Valid
---
### Question
Given a string `S` of `'('` and `')'` parentheses, we add the minimum number
of parentheses ( `'('` or `')'`, and in any positions ) so that the resulting
parentheses string is valid.

Formally, a parentheses string is valid if and only if:

  * It is the empty string, or
  * It can be written as `AB` (`A` concatenated with `B`), where `A` and `B` are valid strings, or
  * It can be written as `(A)`, where `A` is a valid string.

Given a parentheses string, return the minimum number of parentheses we must
add to make the resulting string valid.



 **Example 1:**

    
    
     **Input:** "())"
    **Output:** 1
    

**Example 2:**

    
    
    **Input:** "((("
    **Output:** 3
    

**Example 3:**

    
    
    **Input:** "()"
    **Output:** 0
    

**Example 4:**

    
    
    **Input:** "()))(("
    **Output:** 4



 **Note:**

  1. `S.length <= 1000`
  2. `S` only consists of `'('` and `')'` characters.

### Solution 1
 **Intuition** :  
To make a string valid,  
we can add some `(` on the left,  
and add some `)` on the right.  
We need to find the number of each.  
  

 **Explanation** :  
`left` records the number of `(` we need to add on the left of `S`.  
`right` records the number of `)` we need to add on the left of `S`,  
which equals to the number of current opened parentheses.  
  

Loop char `c` in the string `S`:  
`if (c == '(')`, we increment `right`,  
`if (c == ')')`, we decrement `right`.  
When `right` is already 0, we increment `left`  
Return `left + right` in the end  
  

 **Time Complexity** :  
Time `O(N)`  
Space `O(1)`  
  

 **Java:**

    
    
         public int minAddToMakeValid(String S) {
            int left = 0, right = 0;
            for (int i = 0; i < S.length; ++i) {
                if (S.charAt(i) == '(') {
                    right++;
                } else if (right > 0) {
                    right--;
                } else {
                    left++;
                }
            }
            return left + right;
        }
    

**C++:**

    
    
         int minAddToMakeValid(string S) {
            int left = 0, right = 0;
            for (char c : S)
                if (c == '(')
                    right++;
                else if (right > 0)
                    right--;
                else
                    left++;
            return left + right;
        }
    

**Python:**

    
    
         def minAddToMakeValid(self, S):
            left = right = 0
            for i in S:
                if right == 0 and i == ')':
                    left += 1
                else:
                    right += 1 if i == '(' else -1
            return left + right
    


### Solution 2
Loop through the input array.

  1. if encounter '(', push '(' into stack;
  2. otherwise, ')', check if there is a matching '(' on top of stack; if no, increase the counter by 1; if yes, pop it out;
  3. after the loop, count in the un-matched characters remaining in the stack.

Method 1:  
Time & space: O(n).

    
    
        public int minAddToMakeValid(String S) {
            Deque<Character> stk = new ArrayDeque<>();
            int count = 0;
            for (char c : S.toCharArray()) {
                if (c == '(') { stk.push(c); }
                else if (stk.isEmpty()) { ++count; }
                else { stk.pop(); }
            }
            return count + stk.size();
        }
    

or, make the above the code shorter:

    
    
        public int minAddToMakeValid(String S) {
            Deque<Character> stk = new ArrayDeque<>();
            for (char c : S.toCharArray()) {
                if (c == ')' && !stk.isEmpty() && stk.peek() == '(') { stk.pop(); } // pop out matched pairs.
                else { stk.push(c); } // push in unmatched chars.
            }
            return stk.size();
        }
    

Method 2:  
Since there is only one kind of char, '(', in stack,only a counter will also
work.  
Time: O(n), space: O(1).

    
    
        public int minAddToMakeValid(String S) {
            int count = 0, stk = 0;
            for (int i = 0; i < S.length(); ++i) {
                if (S.charAt(i) == '(') { ++stk; }
                else if (stk <= 0) { ++count; }
                else { --stk; }
            }
            return count + stk;
        }
    


### Solution 3
    
    
    class Solution(object):
        def minAddToMakeValid(self, S):
            """
            :type S: str
            :rtype: int
            """
            
            while "()" in S:
                S = S.replace("()", "")
            
            return len(S)
    



