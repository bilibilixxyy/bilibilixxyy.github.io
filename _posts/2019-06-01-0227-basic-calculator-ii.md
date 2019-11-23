---
layout: post
title: 227. Basic Calculator II
---
### Question
Implement a basic calculator to evaluate a simple expression string.

The expression string contains only **non-negative** integers, `+`, `-`, `*`,
`/` operators and empty spaces ` `. The integer division should truncate
toward zero.

 **Example 1:**

    
    
     **Input:** "3+2*2"
    **Output:** 7
    

**Example 2:**

    
    
    **Input:** " 3/2 "
    **Output:** 1

**Example 3:**

    
    
    **Input:** " 3+5 / 2 "
    **Output:** 5
    

**Note:**

  * You may assume that the given expression is always valid.
  * **Do not** use the `eval` built-in library function.

### Solution 1
    
    
    public class Solution {
    public int calculate(String s) {
        int len;
        if(s==null || (len = s.length())==0) return 0;
        Stack<Integer> stack = new Stack<Integer>();
        int num = 0;
        char sign = '+';
        for(int i=0;i<len;i++){
            if(Character.isDigit(s.charAt(i))){
                num = num*10+s.charAt(i)-'0';
            }
            if((!Character.isDigit(s.charAt(i)) &&' '!=s.charAt(i)) || i==len-1){
                if(sign=='-'){
                    stack.push(-num);
                }
                if(sign=='+'){
                    stack.push(num);
                }
                if(sign=='*'){
                    stack.push(stack.pop()*num);
                }
                if(sign=='/'){
                    stack.push(stack.pop()/num);
                }
                sign = s.charAt(i);
                num = 0;
            }
        }
    
        int re = 0;
        for(int i:stack){
            re += i;
        }
        return re;
    }
    

}


### Solution 2
If you don't like the `44 - op` ASCII trick, you can use `op == '+' ? 1 : -1`
instead. And wow, I didn't know C++ has `or`. I'm a Python guy and wrote that
out of habit and only realized it after getting this accepted :-)

    
    
    int calculate(string s) {
        istringstream in('+' + s + '+');
        long long total = 0, term = 0, n;
        char op;
        while (in >> op) {
            if (op == '+' or op == '-') {
                total += term;
                in >> term;
                term *= 44 - op;
            } else {
                in >> n;
                if (op == '*')
                    term *= n;
                else
                    term /= n;
            }
        }
        return total;
    }


### Solution 3
    
    
    public int calculate(String s) {
        if (s == null) return 0;
        s = s.trim().replaceAll(" +", "");
        int length = s.length();
        
        int res = 0;
        long preVal = 0; // initial preVal is 0
        char sign = '+'; // initial sign is +
        int i = 0;
        while (i < length) {
            long curVal = 0;
            while (i < length && (int)s.charAt(i) <= 57 && (int)s.charAt(i) >= 48) { // int
                curVal = curVal*10 + (s.charAt(i) - '0');
                i++;
            }
            if (sign == '+') {
                res += preVal;  // update res
                preVal = curVal;
            } else if (sign == '-') {
                res += preVal;  // update res
                preVal = -curVal;
            } else if (sign == '*') {
                preVal = preVal * curVal; // not update res, combine preVal & curVal and keep loop
            } else if (sign == '/') {
                preVal = preVal / curVal; // not update res, combine preVal & curVal and keep loop
            }
            if (i < length) { // getting new sign
                sign = s.charAt(i);
                i++;
            }
        }
        res += preVal;
        return res;
    }



