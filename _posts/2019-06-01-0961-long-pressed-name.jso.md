---
layout: post
title: 961. Long Pressed Name
---
### Question
Your friend is typing his `name` into a keyboard.  Sometimes, when typing a
character `c`, the key might get _long pressed_ , and the character will be
typed 1 or more times.

You examine the `typed` characters of the keyboard.  Return `True` if it is
possible that it was your friends name, with some characters (possibly none)
being long pressed.



 **Example 1:**

    
    
     **Input:** name = "alex", typed = "aaleex"
    **Output:** true
    **Explanation:** 'a' and 'e' in 'alex' were long pressed.
    

**Example 2:**

    
    
    **Input:** name = "saeed", typed = "ssaaedd"
    **Output:** false
    **Explanation:** 'e' must have been pressed twice, but it wasn't in the typed output.
    

**Example 3:**

    
    
    **Input:** name = "leelee", typed = "lleeelee"
    **Output:** true
    

**Example 4:**

    
    
    **Input:** name = "laiden", typed = "laiden"
    **Output:** true
    **Explanation:** It's not necessary to long press any character.
    



 **Note:**

  1. `name.length <= 1000`
  2. `typed.length <= 1000`
  3. The characters of `name` and `typed` are lowercase letters.

### Solution 1
 **C++:**

    
    
        bool isLongPressedName(string name, string typed) {
            int  i = 0, m = name.length(), n = typed.length();
            for (int j = 0; j < n; ++j)
                if (i < m && name[i] == typed[j])
                    ++i;
                else if (!j || typed[j] != typed[j - 1])
                    return false;
            return i == m;
        }
    

**Java:**

    
    
        public boolean is LongPressedName(String name, String typed) {
            int i = 0, m = name.length(), n = typed.length();
            for (int j = 0; j < n; ++j)
                if (i < m && name.charAt(i) == typed.charAt(j))
                    ++i;
                else if (j == 0 || typed.charAt(j) != typed.charAt(j - 1))
                    return false;
            return i == m;
        }
    

**Python:**

    
    
        def isLongPressedName(self, name, typed):
             i = 0
            for j in range(len(typed)):
                if i < len(name) and name[i] == typed[j]:
                    i += 1
                elif j == 0 or typed[j] != typed[j - 1]:
                    return False
            return i == len(name)
    


### Solution 2
The idea evert letter in **typed** should be the same as **name** , but
"shifted" (or spreaded to be more exact) to some number of positions.  
That's why, we go through **typed** and trying to identify if we have a
corrponding letter in **name**. At the same time, we calculate the difference
in positions of these corresponding letters in **typed** and **name**. In
other words, difference identicates how many "additional" letters contains
**typed**. For example:  
name: **AABCD**  
typed: **AAAABBCDDDDDD**

At the beginning difference is 0.  
We go through **AAAABBCDDDDDD** :  
Compare first letter of **typed** and **name** :  
![image](https://assets.leetcode.com/users/olsh/image_1540209344.png)

The are equal -> all is ok, go further, difference remains unchanged  
![image](https://assets.leetcode.com/users/olsh/image_1540209466.png)

Again equal -> go next:  
![image](https://assets.leetcode.com/users/olsh/image_1540210445.png)

Not equal, but we can link **A** ( **typed** ) to **A** ( **name** ) from the
previous step. As a result, our difference was incremented:  
![image](https://assets.leetcode.com/users/olsh/image_1540210721.png)

The same will do next: **A** ( **typed** ) != **B** ( **name** ), but we can
link **A** ( **typed** ) to **A** ( **name** ) from the previous step -> the
difference was incremented again:  
![image](https://assets.leetcode.com/users/olsh/image_1540210926.png)

At the end of all these manipulations, we see the following:  
![image](https://assets.leetcode.com/users/olsh/image_1540208693.png)  
The whole implementation:

    
    
    class Solution {
     public boolean isLongPressedName(String name, String typed) {
      int difference = 0;
      for (int i = 0; i < typed.length();) {
    	//letters are equal -> go next
       if (difference <= i && i - difference < name.length() && typed.charAt(i) == name.charAt(i - difference)) {
        i++;
       } 
    	 // letters are not equal,  but we can link typed letter to name letter from the previous iteration
    	 else if (difference < i && i - difference - 1 < name.length() && typed.charAt(i) == name.charAt(i - difference - 1)) {
        difference++;
       } else return false;
      }
    	// check that at the end of name there's no odd symbols
        return typed.length() - difference == name.length();
     }
    }
    


### Solution 3
This very naive solution got accepted. It fails of course for any test case
where `typed` contains extra letters.

    
    
    bool isLongPressedName(string name, string typed, int i = 0) {
        for (auto c : typed) i += name[i] == c;
        return i == name.size();
    }
    

Here is a bit more lengthy but accurate solution:

    
    
    bool isLongPressedName(string name, string typed, int i = 0, int j = 0) {
        while (j < typed.size()) {
            if (i < name.size() && name[i] == typed[j]) ++i, ++j;
            else if (i > 0 && name[i - 1] == typed[j]) ++j;
            else return false;
        }
        return i == name.size();
    }
    



