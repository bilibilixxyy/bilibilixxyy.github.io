---
layout: post
title: 20. Valid Parentheses
---
### Question
Given a string containing just the characters `'('`, `')'`, `'{'`, `'}'`,
`'['` and `']'`, determine if the input string is valid.

An input string is valid if:

  1. Open brackets must be closed by the same type of brackets.
  2. Open brackets must be closed in the correct order.

Note that an empty string is also considered valid.

 **Example 1:**

    
    
     **Input:** "()"
    **Output:** true
    

**Example 2:**

    
    
    **Input:** "()[]{}"
    **Output:** true
    

**Example 3:**

    
    
    **Input:** "(]"
    **Output:** false
    

**Example 4:**

    
    
    **Input:** "([)]"
    **Output:** false
    

**Example 5:**

    
    
    **Input:** "{[]}"
    **Output:** true
    

### Solution 1
    
    
    public boolean isValid(String s) {
    	Stack<Character> stack = new Stack<Character>();
    	for (char c : s.toCharArray()) {
    		if (c == '(')
    			stack.push(')');
    		else if (c == '{')
    			stack.push('}');
    		else if (c == '[')
    			stack.push(']');
    		else if (stack.isEmpty() || stack.pop() != c)
    			return false;
    	}
    	return stack.isEmpty();
    }


### Solution 2
    
    
    class Solution:
        # @return a boolean
        def isValid(self, s):
            stack = []
            dict = {"]":"[", "}":"{", ")":"("}
            for char in s:
                if char in dict.values():
                    stack.append(char)
                elif char in dict.keys():
                    if stack == [] or dict[char] != stack.pop():
                        return False
                else:
                    return False
            return stack == []
    

It's quite obvious.


### Solution 3
    
    
    public class Solution {
        public boolean isValid(String s) {
            Stack<Character> stack = new Stack<Character>();
            // Iterate through string until empty
            for(int i = 0; i<s.length(); i++) {
                // Push any open parentheses onto stack
                if(s.charAt(i) == '(' || s.charAt(i) == '[' || s.charAt(i) == '{')
                    stack.push(s.charAt(i));
                // Check stack for corresponding closing parentheses, false if not valid
                else if(s.charAt(i) == ')' && !stack.empty() && stack.peek() == '(')
                    stack.pop();
                else if(s.charAt(i) == ']' && !stack.empty() && stack.peek() == '[')
                    stack.pop();
                else if(s.charAt(i) == '}' && !stack.empty() && stack.peek() == '{')
                    stack.pop();
                else
                    return false;
            }
            // return true if no open parentheses left in stack
            return stack.empty();
        }
    }



