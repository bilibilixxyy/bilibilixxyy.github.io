---
layout: post
title: 640. Solve the Equation
---
### Question
Solve a given equation and return the value of `x` in the form of string
"x=#value". The equation contains only '+', '-' operation, the variable `x`
and its coefficient.

If there is no solution for the equation, return "No solution".

If there are infinite solutions for the equation, return "Infinite solutions".

If there is exactly one solution for the equation, we ensure that the value of
`x` is an integer.

 **Example 1:**  

    
    
     **Input:** "x+5-3+x=6+x-2"
    **Output:** "x=2"
    

**Example 2:**  

    
    
    **Input:** "x=x"
    **Output:** "Infinite solutions"
    

**Example 3:**  

    
    
    **Input:** "2x=x"
    **Output:** "x=0"
    

**Example 4:**  

    
    
    **Input:** "2x+3x-6x=x+2"
    **Output:** "x=-1"
    

**Example 5:**  

    
    
    **Input:** "x=x+2"
    **Output:** "No solution"
    

### Solution 1
    
    
    public String solveEquation(String equation) {
        int[] res = evaluateExpression(equation.split("=")[0]),  
      	  res2 = evaluateExpression(equation.split("=")[1]);
        res[0] -= res2[0];
        res[1] = res2[1] - res[1];
        if (res[0] == 0 && res[1] == 0) return "Infinite solutions";
        if (res[0] == 0) return "No solution";
        return "x=" + res[1]/res[0];
    }  
    
    public int[] evaluateExpression(String exp) {
        String[] tokens = exp.split("(?=[-+])"); 
        int[] res =  new int[2];
        for (String token : tokens) {
            if (token.equals("+x") || token.equals("x")) res[0] += 1;
    	else if (token.equals("-x")) res[0] -= 1;
    	else if (token.contains("x")) res[0] += Integer.parseInt(token.substring(0, token.indexOf("x")));
    	else res[1] += Integer.parseInt(token);
        }
        return res;
    }
    


### Solution 2
    
    
    def solve_equation(equation)
      a, x = eval('i = 1i;' + equation.gsub('x', 'i').sub('=', '-(') + ')').rect
      "x=#{-a/x}" rescue a != 0 ? 'No solution' : 'Infinite solutions'
    end
    

It's easy to change an equation like `2+3x=5x-7` to an expression for a
complex number like `2+3i-(5i-7)`. Then I let Ruby evaluate that and it gives
me the total of "x numbers" and the total of "non-x numbers" as the imaginary
and real part of the complex number.

Same in Python:

    
    
    def solveEquation(self, equation):
        z = eval(equation.replace('x', 'j').replace('=', '-(') + ')', {'j': 1j})
        a, x = z.real, -z.imag
        return 'x=%d' % (a / x) if x else 'No solution' if a else 'Infinite solutions'
    

And here's a completely different one using a regular expression to parse the
tokens:

    
    
    def solveEquation(self, equation):
        x = a = 0
        side = 1
        for eq, sign, num, isx in re.findall('(=)|([-+]?)(\d*)(x?)', equation):
            if eq:
                side = -1
            elif isx:
                x += side * int(sign + '1') * int(num or 1)
            elif num:
                a -= side * int(sign + num)
        return 'x=%d' % (a / x) if x else 'No solution' if a else 'Infinite solutions'


### Solution 3
The idea is using two pointers to update two parameters: the coefficient of x
and the total sum. On the left and right side of '=', we have to use opposite
signs for each numbers, so I define a sign variable, which will flip if '=' is
seen.

    
    
    class Solution {
    public:
        string solveEquation(string equation) {
            int n = equation.size(), sign = 1, coeff = 0, tot = 0, i = 0;
            for (int j = 0; j < n; j++) {
                if (equation[j] == '+' || equation[j] == '-') {
                    if (j > i) tot += sign*stoi(equation.substr(i, j-i));
                    i = j;
                }
                // corner case: x, -x, +x
                else if (equation[j] == 'x') {
                    if ((i == j) || equation[j-1] == '+')
                        coeff += sign;
                    else if (equation[j-1] == '-')
                        coeff -= sign;
                    else 
                        coeff += sign*stoi(equation.substr(i, j-i));
                    i = j+1;
                }
               // flip sign
                else if (equation[j] == '=') {
                    if (j > i) tot += sign*stoi(equation.substr(i, j-i));
                    sign = -1;
                    i = j+1;
                }
            }
            // there may be a number in the end
            if (i < n) tot += sign*stoi(equation.substr(i));
            if (coeff == 0 && tot == 0) return "Infinite solutions";
            if (coeff == 0 && tot) return "No solution";
            int ans = -tot/coeff;
            return "x="+to_string(ans);
        }
    };
    



