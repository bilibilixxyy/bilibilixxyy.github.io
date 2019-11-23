---
layout: post
title: 1012. Equal Rational Numbers
---
### Question
Given two strings `S` and `T`, each of which represents a non-negative
rational number, return **True** if and only if they represent the same
number. The strings may use parentheses to denote the repeating part of the
rational number.

In general a rational number can be represented using up to three parts: an
_integer part_ , a  _non-repeating part,_ and a   _repeating part_. The number
will be represented  in one of the following three ways:

  * `<IntegerPart>` (e.g. 0, 12, 123)
  * `<IntegerPart><.><NonRepeatingPart>`  (e.g. 0.5, 1., 2.12, 2.0001)
  * `<IntegerPart><.><NonRepeatingPart><(><RepeatingPart><)>` (e.g. 0.1(6), 0.9(9), 0.00(1212))

The repeating portion of a decimal expansion is conventionally denoted within
a pair of round brackets.  For example:

1 / 6 = 0.16666666... = 0.1(6) = 0.1666(6) = 0.166(66)

Both 0.1(6) or 0.1666(6) or 0.166(66) are correct representations of 1 / 6.



 **Example 1:**

    
    
     **Input:** S = "0.(52)", T = "0.5(25)"
    **Output:** true
    **Explanation:** Because "0.(52)" represents 0.52525252..., and "0.5(25)" represents 0.52525252525..... , the strings represent the same number.
    

**Example 2:**

    
    
    **Input:** S = "0.1666(6)", T = "0.166(66)"
    **Output:** true
    

**Example 3:**

    
    
    **Input:** S = "0.9(9)", T = "1."
    **Output:** true
    **Explanation:**
    "0.9(9)" represents 0.999999999... repeated forever, which equals 1.  [[See this link for an explanation.](https://en.wikipedia.org/wiki/0.999...)]
    "1." represents the number 1, which is formed correctly: (IntegerPart) = "1" and (NonRepeatingPart) = "".



 **Note:**

  1. Each part consists only of digits.
  2. The `<IntegerPart>` will not begin with 2 or more zeros.  (There is no other restriction on the digits of each part.)
  3. `1 <= <IntegerPart>.length <= 4 `
  4. `0 <= <NonRepeatingPart>.length <= 4 `
  5. `1 <= <RepeatingPart>.length <= 4`

### Solution 1
[Video](https://www.youtube.com/watch?v=3s6q20JMLTE)

Transform string to double

 **Java:**

    
    
         public boolean isRationalEqual(String S, String T) {
            return f(S) == f(T);
        }
    
        public double f(String S) {
            int i = S.indexOf('(');
            if (i > 0) {
                String base = S.substring(0, i);
                String rep = S.substring(i + 1, S.length() - 1);
                for (int j = 0; j < 20; ++j) base += rep;
                return Double.valueOf(base);
            }
            return Double.valueOf(S);
        }
    

**C++:**

    
    
         bool isRationalEqual(string S, string T) {
            return f(S) == f(T);
        }
    
        double f(string S) {
            auto i = S.find("(");
            if (i != string::npos) {
                string base = S.substr(0, i);
                string rep = S.substr(i + 1, S.length() - i - 2);
                for (int j = 0; j < 20; ++j) base += rep;
                return stod(base);
            }
            return stod(S);
        }
    

**Python:**

    
    
         def isRationalEqual(self, S, T):
            def f(s):
                i = s.find('(')
                if i >= 0:
                    s = s[:i] + s[i + 1:-1] * 20
                return float(s[:20])
            return f(S) == f(T)
    


### Solution 2
For 0 < q < 1, 1 + q + q^2 + ... = 1 / (1 - q)  
Link:
<https://en.wikipedia.org/wiki/Geometric_progression#Infinite_geometric_series>

0.(52) = 0 + 0.52 * (1 + 0.01 + 0.0001 + ...) = 0 + 0.52 / (1 - 0.01)  
0.5(25) = 0.5 + 0.025 * (1 + 0.01 + 0.0001 + ...) = 0.5 + 0.025 / (1 - 0.01)

    
    
    class Solution {
    
        private List<Double> ratios = Arrays.asList(1.0, 1.0 / 9, 1.0 / 99, 1.0 / 999, 1.0 / 9999);
    
        public boolean isRationalEqual(String S, String T) {
            return Math.abs(computeValue(S) - computeValue(T)) < 1e-9;
        }
    
        private double computeValue(String s) {
            if (!s.contains("(")) {
                return Double.valueOf(s);
            } else {
                double intNonRepeatingValue = Double.valueOf(s.substring(0, s.indexOf('(')));
                int nonRepeatingLength = s.indexOf('(') - s.indexOf('.') - 1;
                int repeatingLength = s.indexOf(')') - s.indexOf('(') - 1;
                int repeatingValue = Integer.parseInt(s.substring(s.indexOf('(') + 1, s.indexOf(')')));
                return intNonRepeatingValue + repeatingValue * Math.pow(0.1, nonRepeatingLength) * ratios.get(repeatingLength);
            }
        }
    }
    


### Solution 3
I'm solve this problem via C++ on contest.  
Why I post ruby here, I just want to say, this problem is so unfair to C++
participant.

Just split number to 3 parts: a.b(c)  
We known: x = a + b / (10^len(b)) + c / (10^len(c) - 1) / (10^len(b))  
Ruby has built in regex support, (lots of languages too), so it's easy to
split string.  
And Ruby has built in rational number support, (python too).  
But as a C++ participant, I need to implement them all by hand.

    
    
    def is_rational_equal(s, t)
        parse_num(s) == parse_num(t)
    end
    
    def parse_num(x)
        x =~ /(\d*)\.?(\d*)\(?(\d*)\)?/
        $1.to_r + $2.to_r / (10 ** $2.length) + ($3.length > 0 ? $3.to_r / (10 ** $3.length - 1) / (10 ** $2.length) : 0)
    end
    



