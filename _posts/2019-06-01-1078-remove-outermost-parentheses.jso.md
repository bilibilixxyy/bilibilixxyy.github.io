---
layout: post
title: 1078. Remove Outermost Parentheses
---
### Question
A valid parentheses string is either empty `("")`, `"(" + A + ")"`, or `A +
B`, where `A` and `B` are valid parentheses strings, and `+` represents string
concatenation.  For example, `""`, `"()"`, `"(())()"`, and `"(()(()))"` are
all valid parentheses strings.

A valid parentheses string `S` is **primitive** if it is nonempty, and there
does not exist a way to split it into `S = A+B`, with `A` and `B` nonempty
valid parentheses strings.

Given a valid parentheses string `S`, consider its primitive decomposition: `S
= P_1 + P_2 + ... + P_k`, where `P_i` are primitive valid parentheses strings.

Return `S` after removing the outermost parentheses of every primitive string
in the primitive decomposition of `S`.



 **Example 1:**

    
    
     **Input:** "(()())(())"
    **Output:** "()()()"
    **Explanation:**
    The input string is "(()())(())", with primitive decomposition "(()())" + "(())".
    After removing outer parentheses of each part, this is "()()" + "()" = "()()()".
    

**Example 2:**

    
    
    **Input:** "(()())(())(()(()))"
    **Output:** "()()()()(())"
    **Explanation:**
    The input string is "(()())(())(()(()))", with primitive decomposition "(()())" + "(())" + "(()(()))".
    After removing outer parentheses of each part, this is "()()" + "()" + "()(())" = "()()()()(())".
    

**Example 3:**

    
    
    **Input:** "()()"
    **Output:** ""
    **Explanation:**
    The input string is "()()", with primitive decomposition "()" + "()".
    After removing outer parentheses of each part, this is "" + "" = "".
    



 **Note:**

  1. `S.length <= 10000`
  2. `S[i]` is `"("` or `")"`
  3. `S` is a valid parentheses string

### Solution 1
##  **Intuition**

Quote from @shubhama,  
Primitive string will have equal number of opened and closed parenthesis.

##  **Explanation** :

`opened` count the number of opened parenthesis.  
Add every char to the result,  
unless the first left parenthesis,  
and the last right parenthesis.

##  **Time Complexity** :

`O(N)` Time, `O(N)` space

  

 **Java:**

    
    
         public String removeOuterParentheses(String S) {
            StringBuilder s = new StringBuilder();
            int opened = 0;
            for (char c : S.toCharArray()) {
                if (c == '(' && opened++ > 0) s.append(c);
                if (c == ')' && opened-- > 1) s.append(c);
            }
            return s.toString();
        }
    

**C++:**

    
    
         string removeOuterParentheses(string S) {
            string res;
            int opened = 0;
            for (char c : S) {
                if (c == '(' && opened++ > 0) res += c;
                if (c == ')' && opened-- > 1) res += c;
            }
            return res;
        }
    

**Python:**

    
    
        def removeOuterParentheses(self, S):
             res, opened = [], 0
            for c in S:
                if c == '(' and opened > 0: res.append(c)
                if c == ')' and opened > 1: res.append(c)
                opened += 1 if c == '(' else -1
            return "".join(res)
    


### Solution 2
    
    
    class Solution {
        public String removeOuterParentheses(String S) {
            
            StringBuilder sb = new StringBuilder();
            int open=0, close=0, start=0;
            for(int i=0; i<S.length(); i++) {
                if(S.charAt(i) == '(') {
                    open++;
                } else if(S.charAt(i) == ')') {
                    close++;
                }
                if(open==close) {
                    sb.append(S.substring(start+1, i));
                    start=i+1;
                }
            }
            return sb.toString();
        }
    }
    


### Solution 3
    
    
    class Solution:
    	def removeOuterParentheses(self, S: str) -> str:
    		parts = []
    		left_n = 0
    		right_n = 0
    
    		# [j+1:i] will be slice getting parts without beginning and end
    		j = 0  
    
    		for i in range(len(S)):
    			if S[i] == "(":
    				left_n += 1  # add 1 when left parenthesis
    			else:
    				right_n += 1  # add 1 when right
    
    			if left_n == right_n:
    				parts.append(S[j + 1:i])  # add part when left_n = right_n
    				j = i + 1  # because next first item will be cut, i + 1 gives j += 2 total
    

**I hope I helped you in some way. If you like this solution, I'll be very
happy if you click "Star" on Github:**  
<https://github.com/JBielan/leetcode-python>

 **As well feel free to follow my account for daily coding solutions and
machine-learning/data-science projects:**  
<https://github.com/JBielan>

 **May the code be with you!**



