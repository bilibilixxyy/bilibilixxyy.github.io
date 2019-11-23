---
layout: post
title: 171. Excel Sheet Column Number
---
### Question
Given a column title as appear in an Excel sheet, return its corresponding
column number.

For example:

    
    
        A -> 1
        B -> 2
        C -> 3
        ...
        Z -> 26
        AA -> 27
        AB -> 28 
        ...
    

**Example 1:**

    
    
    **Input:** "A"
    **Output:** 1
    

**Example 2:**

    
    
    **Input:** "AB"
    **Output:** 28
    

**Example 3:**

    
    
    **Input:** "ZY"
    **Output:** 701
    

### Solution 1
Java:

    
    
    int result = 0;
    for (int i = 0; i < s.length(); result = result * 26 + (s.charAt(i) - 'A' + 1), i++);
    return result;
    

C++:

    
    
    int result = 0;
    for (int i = 0; i < s.size(); result = result * 26 + (s.at(i) - 'A' + 1), i++);
    return result;
    

Python:

    
    
    return reduce(lambda x, y : x * 26 + y, [ord(c) - 64 for c in list(s)])
    

Python version is beautiful because reduce function and list comprehensive.

I don't know whether exist similar approach to achieve one line solution in
Java or C++.  
One possible way is defining another method like this:

    
    
    public int titleToNumber(int num, String s)
    

to store previous result and make recursive call.  
But this add much more lines.


### Solution 2
I was asked of this question during an interview with microsoft. The
interviewer asked whether I want a coding question or a brain teaser, I asked
for the latter and here comes the question. I did not do it very well at that
time, though.


### Solution 3
Here is my Java solution. Similar to the number to title.

    
    
    public int titleToNumber(String s) {
        int result = 0;
        for(int i = 0 ; i < s.length(); i++) {
          result = result * 26 + (s.charAt(i) - 'A' + 1);
        }
        return result;
      }



