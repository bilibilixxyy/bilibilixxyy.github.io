---
layout: post
title: 405. Convert a Number to Hexadecimal
---
### Question
Given an integer, write an algorithm to convert it to hexadecimal. For
negative integer, [two’s
complement](https://en.wikipedia.org/wiki/Two%27s_complement) method is used.

 **Note:**

  1. All letters in hexadecimal (`a-f`) must be in lowercase.
  2. The hexadecimal string must not contain extra leading `0`s. If the number is zero, it is represented by a single zero character `'0'`; otherwise, the first character in the hexadecimal string will not be the zero character.
  3. The given number is guaranteed to fit within the range of a 32-bit signed integer.
  4. You **must not use _any_ method provided by the library** which converts/formats the number to hex directly.

 **Example 1:**

    
    
    Input:
    26
    
    Output:
    "1a"
    

**Example 2:**

    
    
    Input:
    -1
    
    Output:
    "ffffffff"
    

### Solution 1
    
    
     /*
    Basic idea: each time we take a look at the last four digits of
                binary verion of the input, and maps that to a hex char
                shift the input to the right by 4 bits, do it again
                until input becomes 0.
    
    */
    
    public class Solution {
        
        char[] map = {'0','1','2','3','4','5','6','7','8','9','a','b','c','d','e','f'};
        
        public String toHex(int num) {
            if(num == 0) return "0";
            String result = "";
            while(num != 0){
                result = map[(num & 15)] + result; 
                num = (num >>> 4);
            }
            return result;
        }
        
        
    }````


### Solution 2
    
    
    const string HEX = "0123456789abcdef";
    class Solution {
    public:
        string toHex(int num) {
            if (num == 0) return "0";
            string result;
            int count = 0;
            while (num && count++ < 8) {
                result = HEX[(num & 0xf)] + result;
                num >>= 4;
            }
            return result;
        }
    };
    


### Solution 3
    
    
        def toHex(self, num):
            if num==0: return '0'
            mp = '0123456789abcdef'  # like a map
            ans = ''
            for i in range(8):
                n = num & 15       # this means num & 1111b
                c = mp[n]          # get the hex char 
                ans = c + ans
                num = num >> 4
            return ans.lstrip('0')  #strip leading zeroes
    



