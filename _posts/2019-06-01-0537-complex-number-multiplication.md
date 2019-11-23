---
layout: post
title: 537. Complex Number Multiplication
---
### Question
Given two strings representing two [complex
numbers](https://en.wikipedia.org/wiki/Complex_number).

You need to return a string representing their multiplication. Note i2 = -1
according to the definition.

 **Example 1:**  

    
    
     **Input:** "1+1i", "1+1i"
    **Output:** "0+2i"
    **Explanation:** (1 + i) * (1 + i) = 1 + i 2 + 2 * i = 2i, and you need convert it to the form of 0+2i.
    

**Example 2:**  

    
    
    **Input:** "1+-1i", "1+-1i"
    **Output:** "0+-2i"
    **Explanation:** (1 - i) * (1 - i) = 1 + i 2 - 2 * i = -2i, and you need convert it to the form of 0+-2i.
    

**Note:**

  1. The input strings will not have extra blank.
  2. The input strings will be given in the form of **a+bi** , where the integer **a** and **b** will both belong to the range of [-100, 100]. And **the output should be also in this form**.

### Solution 1
stringstream is very useful to extract num from a string

    
    
    class Solution {
    public:
        string complexNumberMultiply(string a, string b) {
            int ra, ia, rb, ib;
            char buff;
            stringstream aa(a), bb(b), ans;
            aa >> ra >> buff >> ia >> buff;
            bb >> rb >> buff >> ib >> buff;
            ans << ra*rb - ia*ib << "+" << ra*ib + rb*ia << "i";
            return ans.str();
        }
    };


### Solution 2
This solution relies on the fact that (a+bi)(c+di) = (ac - bd) + (ad+bc)i.

    
    
    public String complexNumberMultiply(String a, String b) {
        int[] coefs1 = Stream.of(a.split("\+|i")).mapToInt(Integer::parseInt).toArray(), 
              coefs2 = Stream.of(b.split("\+|i")).mapToInt(Integer::parseInt).toArray();
        return (coefs1[0]*coefs2[0] - coefs1[1]*coefs2[1]) + "+" + (coefs1[0]*coefs2[1] + coefs1[1]*coefs2[0]) + "i";
    }
    


### Solution 3
    
    
    public String complexNumberMultiply(String a, String b) {
        String result = "";
        String[] A = a.split("\+");
        String[] B = b.split("\+");
        int a1 = Integer.parseInt(A[0]);
        int b1 = Integer.parseInt(A[1].replace("i",""));
    
        int a2 = Integer.parseInt(B[0]);
        int b2 = Integer.parseInt(B[1].replace("i",""));
    
        int a1a2 = a1 * a2;
        int b1b2 = b1 * b2;
        int a1b2a2b1 = (a1 * b2) + (b1 * a2);
    
        String afinal = (a1a2 + (-1 * b1b2)) + "";
        String bfinal = a1b2a2b1 + "i";
        result = afinal+"+"+bfinal;
        return result;
    }



