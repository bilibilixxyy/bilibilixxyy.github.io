---
layout: post
title: 13. Roman to Integer
---
### Question
Roman numerals are represented by seven different symbols: `I`, `V`, `X`, `L`,
`C`, `D` and `M`.

    
    
     **Symbol**       **Value**
    I             1
    V             5
    X             10
    L             50
    C             100
    D             500
    M             1000

For example, two is written as `II` in Roman numeral, just two one's added
together. Twelve is written as, `XII`, which is simply `X` \+ `II`. The number
twenty seven is written as `XXVII`, which is `XX` \+ `V` \+ `II`.

Roman numerals are usually written largest to smallest from left to right.
However, the numeral for four is not `IIII`. Instead, the number four is
written as `IV`. Because the one is before the five we subtract it making
four. The same principle applies to the number nine, which is written as `IX`.
There are six instances where subtraction is used:

  * `I` can be placed before `V` (5) and `X` (10) to make 4 and 9. 
  * `X` can be placed before `L` (50) and `C` (100) to make 40 and 90. 
  * `C` can be placed before `D` (500) and `M` (1000) to make 400 and 900.

Given a roman numeral, convert it to an integer. Input is guaranteed to be
within the range from 1 to 3999.

**Example 1:**

    
    
    **Input:**  "III"
    **Output:** 3

**Example 2:**

    
    
    **Input:**  "IV"
    **Output:** 4

**Example 3:**

    
    
    **Input:**  "IX"
    **Output:** 9

**Example 4:**

    
    
    **Input:**  "LVIII"
    **Output:** 58
    **Explanation:** L = 50, V= 5, III = 3.
    

**Example 5:**

    
    
    **Input:**  "MCMXCIV"
    **Output:** 1994
    **Explanation:** M = 1000, CM = 900, XC = 90 and IV = 4.

### Solution 1
count every Symbol and add its value to the sum, and minus the extra part of
special cases.

    
    
    public int romanToInt(String s) {
         int sum=0;
        if(s.indexOf("IV")!=-1){sum-=2;}
        if(s.indexOf("IX")!=-1){sum-=2;}
        if(s.indexOf("XL")!=-1){sum-=20;}
        if(s.indexOf("XC")!=-1){sum-=20;}
        if(s.indexOf("CD")!=-1){sum-=200;}
        if(s.indexOf("CM")!=-1){sum-=200;}
        
        char c[]=s.toCharArray();
        int count=0;
        
       for(;count<=s.length()-1;count++){
           if(c[count]=='M') sum+=1000;
           if(c[count]=='D') sum+=500;
           if(c[count]=='C') sum+=100;
           if(c[count]=='L') sum+=50;
           if(c[count]=='X') sum+=10;
           if(c[count]=='V') sum+=5;
           if(c[count]=='I') sum+=1;
           
       }
       
       return sum;
        
    }


### Solution 2
Problem is simpler to solve by working the string from back to front and using
a map. Runtime speed is 88 ms.

    
    
    int romanToInt(string s) 
    {
        unordered_map<char, int> T = { { 'I' , 1 },
                                       { 'V' , 5 },
                                       { 'X' , 10 },
                                       { 'L' , 50 },
                                       { 'C' , 100 },
                                       { 'D' , 500 },
                                       { 'M' , 1000 } };
                                       
       int sum = T[s.back()];
       for (int i = s.length() - 2; i >= 0; --i) 
       {
           if (T[s[i]] < T[s[i + 1]])
           {
               sum -= T[s[i]];
           }
           else
           {
               sum += T[s[i]];
           }
       }
       
       return sum;
    }


### Solution 3
    
    
    class Solution:
    # @param {string} s
    # @return {integer}
    def romanToInt(self, s):
        roman = {'M': 1000,'D': 500 ,'C': 100,'L': 50,'X': 10,'V': 5,'I': 1}
        z = 0
        for i in range(0, len(s) - 1):
            if roman[s[i]] < roman[s[i+1]]:
                z -= roman[s[i]]
            else:
                z += roman[s[i]]
        return z + roman[s[-1]]
    

*Note: The trick is that the last letter is always added. Except the last one, if one letter is less than its latter one, this letter is subtracted.



