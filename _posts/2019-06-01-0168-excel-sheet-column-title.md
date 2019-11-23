---
layout: post
title: 168. Excel Sheet Column Title
---
### Question
Given a positive integer, return its corresponding column title as appear in
an Excel sheet.

For example:

    
    
        1 -> A
        2 -> B
        3 -> C
        ...
        26 -> Z
        27 -> AA
        28 -> AB 
        ...
    

**Example 1:**

    
    
    **Input:** 1
    **Output:** "A"
    

**Example 2:**

    
    
    **Input:** 28
    **Output:** "AB"
    

**Example 3:**

    
    
    **Input:** 701
    **Output:** "ZY"
    

### Solution 1
Java:

    
    
    return n == 0 ? "" : convertToTitle(--n / 26) + (char)('A' + (n % 26));
    

C++:

    
    
    return n == 0 ? "" : convertToTitle(n / 26) + (char) (--n % 26 + 'A');
    

update: because the behavior of different compilers, the safe version should
be:

    
    
    return n == 0 ? "" : convertToTitle((n - 1) / 26) + (char) ((n - 1) % 26 + 'A');
    

Python:

    
    
    return "" if num == 0 else self.convertToTitle((num - 1) / 26) + chr((num - 1) % 26 + ord('A'))


### Solution 2
    
    
    public class Solution {
        public String convertToTitle(int n) {
            StringBuilder result = new StringBuilder();
    
            while(n>0){
                n--;
                result.insert(0, (char)('A' + n % 26));
                n /= 26;
            }
    
            return result.toString();
        }
    }


### Solution 3
Let's see the relationship between the Excel sheet column title and the
number:

    
    
    A   1     AA    26+ 1     BA  2×26+ 1     ...     ZA  26×26+ 1     AAA  1×26²+1×26+ 1
    B   2     AB    26+ 2     BB  2×26+ 2     ...     ZB  26×26+ 2     AAB  1×26²+1×26+ 2
    .   .     ..    .....     ..  .......     ...     ..  ........     ...  .............   
    .   .     ..    .....     ..  .......     ...     ..  ........     ...  .............
    .   .     ..    .....     ..  .......     ...     ..  ........     ...  .............
    Z  26     AZ    26+26     BZ  2×26+26     ...     ZZ  26×26+26     AAZ  1×26²+1×26+26
    

Now we can see that ABCD＝A×26³＋B×26²＋C×26¹＋D＝1×26³＋2×26²＋3×26¹＋4

But how to get the column title from the number? We can't simply use the n%26
method because:

ZZZZ＝Z×26³＋Z×26²＋Z×26¹＋Z＝26×26³＋26×26²＋26×26¹＋26

We can use (n-1)%26 instead, then we get a number range from 0 to 25.

    
    
    class Solution:
        # @return a string
        def convertToTitle(self, num):
            capitals = [chr(x) for x in range(ord('A'), ord('Z')+1)]
            result = []
            while num > 0:
                result.append(capitals[(num-1)%26])
                num = (num-1) // 26
            result.reverse()
            return ''.join(result)



