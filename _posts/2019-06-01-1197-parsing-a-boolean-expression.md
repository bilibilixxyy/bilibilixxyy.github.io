---
layout: post
title: 1197. Parsing A Boolean Expression
---
### Question
Return the result of evaluating a given boolean `expression`, represented as a
string.

An expression can either be:

  * `"t"`, evaluating to `True`;
  * `"f"`, evaluating to `False`;
  * `"!(expr)"`, evaluating to the logical NOT of the inner expression `expr`;
  * `"&(expr1,expr2,...)"`, evaluating to the logical AND of 2 or more inner expressions `expr1, expr2, ...`;
  * `"|(expr1,expr2,...)"`, evaluating to the logical OR of 2 or more inner expressions `expr1, expr2, ...`



 **Example 1:**

    
    
     **Input:** expression = "!(f)"
    **Output:** true
    

**Example 2:**

    
    
    **Input:** expression = "|(f,t)"
    **Output:** true
    

**Example 3:**

    
    
    **Input:** expression = " &(t,f)"
    **Output:** false
    

**Example 4:**

    
    
    **Input:** expression = "|( &(t,f,t),!(t))"
    **Output:** false
    



 **Constraints:**

  * `1 <= expression.length <= 20000`
  * `expression[i]` consists of characters in `{'(', ')', '&', '|', '!', 't', 'f', ','}`.
  * `expression` is a valid expression representing a boolean, as given in the description.

### Solution 1
##  **Intuition**

Well, we can see that `&`, `|` and `!` are just three functions.  
And in python, they are function `all`, `any` and keyword `not`.

##  **Explanation**

Following the description,  
it demands us to evaluate the expression.  
So no recursion and no stack, I just `eval` the expression.  
  

##  **Complexity**

Time `O(N)`  
Sapce `O(N)`  
I guess it's not fast compared with string parse, but wow it's `O(N)`.  
  

 **Python:**

    
    
        def parseBoolExpr(self, S, t= True, f=False):
            return eval(S.replace('!', 'not |').replace('&(', 'all([').replace('|(', 'any([').replace(')', '])'))
    


### Solution 2
**Method 1:** Iterative version - Use Stack and Set.

Loop through the input String:

  1. Use a stack to store chars except `','` and `')'`;
  2. If we find a `')'`, keep popping out the chars from the stack till find a `'('`; add the popped-out into a Set.
  3. Pop out the `operator` after popping `')'` out, push into stack the corresponding result according to the `operator`.
  4. repeat the above till the end, and the remaining is the result.

 **[Java]**

    
    
         public boolean parseBoolExpr(String expression) {
            Deque<Character> stk = new ArrayDeque<>();
            for (int i = 0; i < expression.length(); ++i) {
                char c = expression.charAt(i);
                if (c == ')') {
                    Set<Character> seen = new HashSet<>();
                    while (stk.peek() != '(')
                        seen.add(stk.pop());
                    stk.pop();// pop out '('.
                    char operator = stk.pop(); // get operator for current expression.
                    if (operator == '&') {
                        stk.push(seen.contains('f') ? 'f' : 't'); // if there is any 'f', & expression results to 'f'
                    }else if (operator == '|') {
                        stk.push(seen.contains('t') ? 't' : 'f'); // if there is any 't', | expression results to 't'
                    }else { // ! expression.
                        stk.push(seen.contains('t') ? 'f' : 't'); // Logical NOT flips the expression.
                    }
                }else if (c != ',') {
                    stk.push(c);
                }
            }
            return stk.pop() == 't';
        }
    

**[Python 3]** credit to: **@zengfei216 and @cenkay**

    
    
         def parseBoolExpr(self, expression: str) -> bool:
            stack = []
            for c in expression:
                if c == ')':
                    seen = set()
                    while stack[-1] != '(':
                        seen.add(stack.pop())
                    stack.pop()
                    operator = stack.pop()
                    stack.append(all(seen) if operator == '&' else any(seen) if operator == '|' else not seen.pop())
                elif c != ',':
                    stack.append(True if c == 't' else False if c == 'f' else c)
            return stack.pop()
    

* * *

**Method 2:** Recursive version.

Use `level` to count the non-matched `(` and `)`, together with `,`, we can
delimit valid sub-expression and hence recurse to sub-problem.

**[Java]**

    
    
         public boolean parseBoolExpr(String expression) {
            return parse(expression, 0, expression.length());
        }
        private boolean parse(String s, int lo, int hi) {
            char c = s.charAt(lo);
            if (hi - lo == 1) return c == 't'; // base case.
            boolean ans = c == '&'; // only when c is &, set ans to true; otherwise false.
            for (int i = lo + 2, start = i, level = 0; i < hi; ++i) {
                char d = s.charAt(i);
                if (level == 0 && (d == ',' || d == ')')) { // locate a valid sub-expression. 
                    boolean cur = parse(s, start, i); // recurse to sub-problem.
                    start = i + 1; // next sub-expression start index.
                    if (c == '&') ans &= cur;
                    else if (c == '|') ans |= cur;
                    else ans = !cur; // c == '!'.
                }
                if (d == '(') ++level;
                if (d == ')') --level;
            }
            return ans;
        }
    

**[Python 3]**

    
    
        def parseBoolExpr(self, expression: str) ->  bool:
            
            def parse(e: str, lo: int, hi: int) -> bool:
                if hi - lo == 1: # base case
                    return e[lo] == 't'               
                ans = e[lo] == '&' # only when the first char is '&', ans assigned True.
                level, start = 0, lo + 2 # e[lo + 1] must be '(', so start from lo + 2 to delimit sub-expression.
                for i in range(lo + 2, hi):
                    if level == 0  and e[i] in [',', ')']: # found a sub-expression.
                        cur = parse(e, start, i) # recurse to sub-problem.
                        start = i + 1 # start point of next sub-expression.
                        if e[lo] == '&':
                            ans &= cur
                        elif e[lo] == '|':
                            ans |= cur
                        else: # e[lo] is '!'.
                            ans = not cur
                    if e[i] == '(':
                        level = level + 1
                    elif e[i] == ')':
                        level = level - 1
                return ans;        
            
            return parse(expression, 0, len(expression))
    

**Analysis:**  
Time & space: O(n), n = expression.length().


### Solution 3
    
    
    class Solution {
    public:
        bool f_not(const string& s,int& i){
            i+=2;
            auto ret = f(s,i);
            i++;
            return !ret;
        }
        
        bool f_and(const string& s,int& i){
            i+=2;
            bool ret = true;
            ret &= f(s,i);
            while(s[i]!=')'){
                i++;
                ret &= f(s,i);
            }
            i++;
            return ret;
        }
        
        bool f_or(const string& s,int& i){
            i+=2;
            bool ret = false;
            ret |= f(s,i);
            while(s[i]!=')'){
                i++;
                ret |= f(s,i);
            }
            i++;
            return ret;
        }
        
        bool f(const string& s, int& i){
            if(s[i] == 't'){
                i++;
                return true;
            } else if(s[i] == 'f'){
                i++;
                return false;
            } else if(s[i] == '!'){
                return f_not(s,i);
            } else if(s[i] == '&'){
                return f_and(s,i);
            } return f_or(s,i);
        }
        bool parseBoolExpr(string expression) {
            int i = 0;
            return f(expression,i);
        }
    };
    



