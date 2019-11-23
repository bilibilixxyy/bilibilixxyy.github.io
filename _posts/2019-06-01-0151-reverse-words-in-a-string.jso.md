---
layout: post
title: 151. Reverse Words in a String
---
### Question
Given an input string, reverse the string word by word.



 **Example 1:**

    
    
     **Input:** "the sky is blue"
    **Output:  **"blue is sky the"
    

**Example 2:**

    
    
    **Input:** "   hello world!  "
    **Output:  **"world! hello"
    **Explanation:** Your reversed string should not contain leading or trailing spaces.
    

**Example 3:**

    
    
    **Input:** "a good    example"
    **Output:  **"example good a"
    **Explanation:** You need to reduce multiple spaces between two words to a single space in the reversed string.
    



 **Note:**

  * A word is defined as a sequence of non-space characters.
  * Input string may contain leading or trailing spaces. However, your reversed string should not contain leading or trailing spaces.
  * You need to reduce multiple spaces between two words to a single space in the reversed string.



 **Follow up:**

For C programmers, try to solve it _in-place_ in _O_ (1) extra space.

### Solution 1
    
    
    public class Solution {
      
      public String reverseWords(String s) {
        if (s == null) return null;
        
        char[] a = s.toCharArray();
        int n = a.length;
        
        // step 1. reverse the whole string
        reverse(a, 0, n - 1);
        // step 2. reverse each word
        reverseWords(a, n);
        // step 3. clean up spaces
        return cleanSpaces(a, n);
      }
      
      void reverseWords(char[] a, int n) {
        int i = 0, j = 0;
          
        while (i < n) {
          while (i < j || i < n && a[i] == ' ') i++; // skip spaces
          while (j < i || j < n && a[j] != ' ') j++; // skip non spaces
          reverse(a, i, j - 1);                      // reverse the word
        }
      }
      
      // trim leading, trailing and multiple spaces
      String cleanSpaces(char[] a, int n) {
        int i = 0, j = 0;
          
        while (j < n) {
          while (j < n && a[j] == ' ') j++;             // skip spaces
          while (j < n && a[j] != ' ') a[i++] = a[j++]; // keep non spaces
          while (j < n && a[j] == ' ') j++;             // skip spaces
          if (j < n) a[i++] = ' ';                      // keep only one space
        }
      
        return new String(a).substring(0, i);
      }
      
      // reverse a[] from a[i] to a[j]
      private void reverse(char[] a, int i, int j) {
        while (i < j) {
          char t = a[i];
          a[i++] = a[j];
          a[j--] = t;
        }
      }
      
    }


### Solution 2
First, reverse the whole string, then reverse each word.

    
    
    void reverseWords(string &s) {
        reverse(s.begin(), s.end());
        int storeIndex = 0;
        for (int i = 0; i < s.size(); i++) {
            if (s[i] != ' ') {
                if (storeIndex != 0) s[storeIndex++] = ' ';
                int j = i;
                while (j < s.size() && s[j] != ' ') { s[storeIndex++] = s[j++]; }
                reverse(s.begin() + storeIndex - (j - i), s.begin() + storeIndex);
                i = j;
            }
        }
        s.erase(s.begin() + storeIndex, s.end());
    }


### Solution 3
    
    
    String[] parts = s.trim().split("\s+");
    String out = "";
    for (int i = parts.length - 1; i > 0; i--) {
        out += parts[i] + " ";
    }
    return out + parts[0];
    

I'm splitting on the regex for one-or-more whitespace, this takes care of
multiple spaces/tabs/newlines/etc in the input. Since the input could have
leading/trailing whitespace, which would result in empty matches, I first trim
the input string.

Now there could be three possibilities:

  1. The input is empty: "", parts will contain [""]. The for loop is skipped and "" + "" is returned.
  2. The input contains only one part: "a", parts will contain ["a"]. The for loop is skipped and "" + "a" is returned.
  3. The input contains multiple parts: "a b c", reverse the order of all but the first part: "c b " in the for loop and return "c b " + "a".

Obviously this is not the fastest or most memory efficient way to solve the
problem, but optimizations should _only_ be done when they are needed.
Readable code is usually more important than efficient code.

How to make it efficient?

  1. Use a StringBuilder to concatenate the string parts, instead of concatenating strings directly. This will (I assume) build something like a linked-list of string parts, and only allocate the new string when you need it, instead of on each concatenation.
  2. Iterate over the string, instead of using trim/split. Store the index of the last character in the word, when you find the first character, copy the substring to the output string.
  3. Instead of using substring, insert the word-characters directly in the StringBuilder. Assuming they're using a linked-list or tree, this could be a whole last faster.



