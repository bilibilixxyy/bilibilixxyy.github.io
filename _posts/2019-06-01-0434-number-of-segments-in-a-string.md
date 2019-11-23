---
layout: post
title: 434. Number of Segments in a String
---
### Question
Count the number of segments in a string, where a segment is defined to be a
contiguous sequence of non-space characters.

Please note that the string does not contain any **non-printable** characters.

 **Example:**

    
    
     **Input:** "Hello, my name is John"
    **Output:** 5
    

### Solution 1
    
    
    public int countSegments(String s) {
        int res=0;
        for(int i=0; i<s.length(); i++)
            if(s.charAt(i)!=' ' && (i==0 || s.charAt(i-1)==' '))
                res++;        
        return res;
    }
    
    Time complexity:  O(n)
    Space complexity: O(1)


### Solution 2
    
    
    class Solution {
    public:
        int countSegments(string s) {
            int res = 0;
            s.push_back(' ');
            for(int i = 1; i < s.size(); ++i)
              if(s[i] == ' ' && s[i-1] != ' ') ++res;
            return res;
        }
    };
    


### Solution 3
Ruby:

    
    
    def count_segments(s)
      s.split.size
    end
    

Python:

    
    
    def countSegments(self, s):
        return len(s.split())
    

Java:

    
    
    public int countSegments(String s) {
        return ("x " + s).split(" +").length - 1;
    }
    

C++:

    
    
    int countSegments(string s) {
        return regex_replace(regex_replace(s, regex("\S+"), "x"), regex(" "), "").size();
    }



