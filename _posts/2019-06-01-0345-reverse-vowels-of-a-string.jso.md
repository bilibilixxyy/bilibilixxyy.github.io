---
layout: post
title: 345. Reverse Vowels of a String
---
### Question
Write a function that takes a string as input and reverse only the vowels of a
string.

 **Example 1:**

    
    
     **Input:** "hello"
    **Output:** "holle"
    

**Example 2:**

    
    
    **Input:** "leetcode"
    **Output:** "leotcede"

 **Note:**  
The vowels does not include the letter "y".

### Solution 1
In the inner while loop, don't forget the condition "start less than end"
while incrementing start and decrementing end. This is my friend's google
phone interview question. Cheers!  
// update! May use a HashSet to reduce the look up time to O(1)

    
    
    public class Solution {
    public String reverseVowels(String s) {
        if(s == null || s.length()==0) return s;
        String vowels = "aeiouAEIOU";
        char[] chars = s.toCharArray();
        int start = 0;
        int end = s.length()-1;
        while(start<end){
            
            while(start<end && !vowels.contains(chars[start]+"")){
                start++;
            }
            
            while(start<end && !vowels.contains(chars[end]+"")){
                end--;
            }
            
            char temp = chars[start];
            chars[start] = chars[end];
            chars[end] = temp;
            
            start++;
            end--;
        }
        return new String(chars);
    }
    

}


### Solution 2
 **Ruby**

    
    
     def reverse_vowels(s)
      vowels = s.scan(/[aeiou]/i)
      s.gsub(/[aeiou]/i) { vowels.pop }
    end
    

* * *

**Python**

    
    
     def reverseVowels(self, s):
        vowels = re.findall('(?i)[aeiou]', s)
        return re.sub('(?i)[aeiou]', lambda m: vowels.pop(), s)
    

* * *

It's possible in one line, but I don't really like it:

    
    
    def reverseVowels(self, s):
        return re.sub('(?i)[aeiou]', lambda m, v=re.findall('(?i)[aeiou]', s): v.pop(), s)
    

* * *

Another version, finding replacement vowels on the fly instead of collecting
all in advance:

    
    
    def reverseVowels(self, s):
        vowels = (c for c in reversed(s) if c in 'aeiouAEIOU')
        return re.sub('(?i)[aeiou]', lambda m: next(vowels), s)


### Solution 3
    
    
    class Solution {
    public:
        string reverseVowels(string s) {
            int i = 0, j = s.size() - 1;
            while (i < j) {
                i = s.find_first_of("aeiouAEIOU", i);
                j = s.find_last_of("aeiouAEIOU", j);
                if (i < j) {
                    swap(s[i++], s[j--]);
                }
            }
            return s;
        }
    };



