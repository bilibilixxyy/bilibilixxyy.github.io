---
layout: post
title: 344. Reverse String
---
### Question
Write a function that reverses a string. The input string is given as an array
of characters `char[]`.

Do not allocate extra space for another array, you must do this by **modifying
the input array  [in-place](https://en.wikipedia.org/wiki/In-
place_algorithm)** with O(1) extra memory.

You may assume all the characters consist of [printable ascii
characters](https://en.wikipedia.org/wiki/ASCII#Printable_characters).



 **Example 1:**

    
    
     **Input:** ["h","e","l","l","o"]
    **Output:** ["o","l","l","e","h"]
    

**Example 2:**

    
    
    **Input:** ["H","a","n","n","a","h"]
    **Output:** ["h","a","n","n","a","H"]
    

### Solution 1
<https://www.ratchapong.com/algorithm-practice/leetcode/reverse-string> [Full
solutions]

    
    
    public class Solution {
        public String reverseString(String s) {
            char[] word = s.toCharArray();
            int i = 0;
            int j = s.length() - 1;
            while (i < j) {
                char temp = word[i];
                word[i] = word[j];
                word[j] = temp;
                i++;
                j--;
            }
            return new String(word);
        }
    }
    

#### Complexity Analysis

**Time Complexity:** `O(n)` (Average Case) and `O(n)` (Worst Case) where `n`
is the total number character in the input string. The algorithm need to
reverse the whole string.

**Auxiliary Space:** `O(n)` space is used where `n` is the total number
character in the input string. Space is needed to transform string to
character array.

#### Algorithm

**Approach:** Iterative Swapping Using Two Pointers

One pointer is pointing at the start of the string while the other pointer is
pointing at the end of the string. Both pointers will keep swapping its
element and travel towards each other. The algorithm basically simulating
rotation of a string with respect to its midpoint.

    
    
    public class Solution {
        public String reverseString(String s) {
             byte[] bytes = s.getBytes();
            int i = 0;
            int j = s.length() - 1;
            while (i < j) {
                byte temp = bytes[i];
                bytes[i] = bytes[j];
                bytes[j] = temp;
                i++;
                j--;
            }
            return new String(bytes);
        }
    }
    

#### Complexity Analysis

**Time Complexity:** `O(n)` (Average Case) and `O(n)` (Worst Case) where `n`
is the total number character in the input string. The algorithm need to
reverse the whole string. Each character is `1` byte.

**Auxiliary Space:** `O(n)` space is used where `n` is the total number
character in the input string. Space is needed to transform string to byte
array.

#### Algorithm

**Approach:** Iterative Swapping Using Two Pointers

One pointer is pointing at the start of the byte array while the other pointer
is pointing at the end of the byte array. Both pointers will keep swapping its
element and travel towards each other. The algorithm basically simulating
rotation of a string with respect to its midpoint.

Note that this assume that the input string is encoded using ASCII format.
This will not work with Unicode value where one character may be more than 1
byte.

    
    
     public class Solution {
        public String reverseString(String s) {
            char[] word = s.toCharArray();
            int i = 0;
            int j = s.length() - 1;
            while (i < j) {
                word[i] = (char) (word[i] ^ word[j]);
                word[j] = (char) (word[i] ^ word[j]);
                word[i] = (char) (word[i] ^ word[j]);
                i++;
                j--;
            }
            return new String(bytes);
        }
    }
    

#### Complexity Analysis

**Time Complexity:** `O(n)` (Average Case) and `O(n)` (Worst Case) where `n`
is the total number character in the input string. The algorithm need to
reverse the whole string.

**Auxiliary Space:** `O(n)` space is used where `n` is the total number
character in the input string. Space is needed to transform string to
character array.

#### Algorithm

**Approach:** Iterative Swapping Using Two Pointers

One pointer is pointing at the start of the string while the other pointer is
pointing at the end of the string. Both pointers will keep swapping its
element and travel towards each other. The algorithm basically simulating
rotation of a string with respect to its midpoint. The swapping is done by
using `XOR` swapping algorithm.

Operation | Result  
---|---  
`a = a \oplus b` | `a = a \oplus b`  
`b = a \oplus b` | `b = (a \oplus b) \oplus b = a \oplus b \oplus b = a`  
`a = a \oplus b` | `a = (a \oplus b) \oplus a = a \oplus b \oplus a = b`  
  
Note that this assume that the input string is encoded using ASCII format.
This will not work with Unicode value where one character may be more than 1
byte.

    
    
    public class Solution {
        public String reverseString(String s) {
            byte[] bytes = s.getBytes();
            int i = 0;
            int j = s.length() - 1;
            while (i < j) {
                bytes[i] = (byte) (bytes[i] ^ bytes[j]);
                bytes[j] = (byte) (bytes[i] ^ bytes[j]);
                bytes[i] = (byte) (bytes[i] ^ bytes[j]);
                i++;
                j--;
            }
            return new String(bytes);
        }
    }
    

#### Complexity Analysis

**Time Complexity:** `O(n)` (Average Case) and `O(n)` (Worst Case) where `n`
is the total number character in the input string. The algorithm need to
reverse the whole string. Each character is `1` byte.

**Auxiliary Space:** `O(n)` space is used where `n` is the total number
character in the input string. Space is needed to transform string to byte
array.

#### Algorithm

**Approach:** Iterative Swapping Using Two Pointers

One pointer is pointing at the start of the byte array while the other pointer
is pointing at the end of the byte array. Both pointers will keep swapping its
element and travel towards each other. The algorithm basically simulating
rotation of a string with respect to its midpoint. The swapping is done by
using `XOR` swapping algorithm.

Operation | Result  
---|---  
`a = a \oplus b` | `a = a \oplus b`  
`b = a \oplus b` | `b = (a \oplus b) \oplus b = a \oplus b \oplus b = a`  
`a = a \oplus b` | `a = (a \oplus b) \oplus a = a \oplus b \oplus a = b`  
  
Note that this assume that the input string is encoded using ASCII format.
This will not work with Unicode value where one character may be more than 1
byte.

    
    
    public class Solution {
        public String reverseString(String s) {
            return new StringBuilder(s).reverse().toString();
        }
    }
    

#### Complexity Analysis

**Time Complexity:** `O(n)` (Average Case) and `O(n)` (Worst Case) where `n`
is the total number character in the input string. Depending on the
implementation. However, it is not possible to reverse string in less than
`O(n)`.

**Auxiliary Space:** `O(n)` space is used where `n` is the total number
character in the input string. Space is needed to transform immutable string
into character buffer in `StringBuilder`.

#### Algorithm

**Approach:** Using Java Library

Java's library is probably slower that direct implementation due to extra
overhead in check various edge cases such as surrogate pairs.

    
    
     public class Solution {
        public String reverseString(String s) {
            int length = s.length();
            if (length <= 1) return s;
            String leftStr = s.substring(0, length / 2);
            String rightStr = s.substring(length / 2, length);
            return reverseString(rightStr) + reverseString(leftStr);
        }
    }
    

#### Complexity Analysis

**Time Complexity:** `O(n log(n))` (Average Case) and `O(n * log(n))` (Worst
Case) where `n` is the total number character in the input string. The
recurrence equation is `T(n) = 2 * T(n/2) + O(n)`. `O(n)` is due to the fact
that concatenation function takes linear time. The recurrence equation can be
solved to get `O(n * log(n))`.

**Auxiliary Space:** `O(h)` space is used where `h` is the depth of recursion
tree generated which is `log(n)`. Space is needed for activation stack during
recursion calls.

#### Algorithm

**Approach:** Divide and Conquer (Recursive)

The string is split into half. Each substring will be further divided. This
process continues until the string can no longer be divided (length `


### Solution 2
    
    
     class Solution(object):
        def reverseString(self, s):
            l = len(s)
            if l < 2:
                return s
            return self.reverseString(s[l/2:]) + self.reverseString(s[:l/2])
    
    
    class SolutionClassic(object):
        def reverseString(self, s):
            r = list(s)
            i, j  = 0, len(r) - 1
            while i < j:
                r[i], r[j] = r[j], r[i]
                i += 1
                j -= 1
    
            return "".join(r)
    
    class SolutionPythonic(object):
        def reverseString(self, s):
            return s[::-1]
    


### Solution 3
    
    
    class Solution {
    public:
        string reverseString(string s) {
            int i = 0, j = s.size() - 1;
            while(i < j){
                swap(s[i++], s[j--]); 
            }
            
            return s;
        }
    };



