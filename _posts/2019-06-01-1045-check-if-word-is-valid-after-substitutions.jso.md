---
layout: post
title: 1045. Check If Word Is Valid After Substitutions
---
### Question
We are given that the string `"abc"` is valid.

From any valid string `V`, we may split `V` into two pieces `X` and `Y` such
that `X + Y` (`X` concatenated with `Y`) is equal to `V`.  (`X` or `Y` may be
empty.)  Then, `X + "abc" + Y` is also valid.

If for example `S = "abc"`, then examples of valid strings are: `"abc",
"aabcbc", "abcabc", "abcabcababcc"`.  Examples of **invalid**  strings are:
`"abccba"`, `"ab"`, `"cababc"`, `"bac"`.

Return `true` if and only if the given string `S` is valid.



 **Example 1:**

    
    
     **Input:** "aabcbc"
    **Output:** true
    **Explanation:**
    We start with the valid string "abc".
    Then we can insert another "abc" between "a" and "bc", resulting in "a" + "abc" + "bc" which is "aabcbc".
    

**Example 2:**

    
    
    **Input:** "abcabcababcc"
    **Output:** true
    **Explanation:**
    "abcabcabc" is valid after consecutive insertings of "abc".
    Then we can insert "abc" before the last letter, resulting in "abcabcab" + "abc" + "c" which is "abcabcababcc".
    

**Example 3:**

    
    
    **Input:** "abccba"
    **Output:** false
    

**Example 4:**

    
    
    **Input:** "cababc"
    **Output:** false



 **Note:**

  1. `1 <= S.length <= 20000`
  2. `S[i]` is `'a'`, `'b'`, or `'c'`

### Solution 1
## Solution 1, Brute Force

Brute force using replace will get accepted. Though it's not expected.

Time complexity `O(N^2)`, space `O(N^2)` (depending on implementation).  
 **Python**

    
    
         def isValid(self, S):
            S2 = ""
            while S != S2:
                S, S2 = S.replace("abc", ""), S
            return S == ""
    

  

## Solution 2

Keep a stack, whenever meet `'c'`,  
pop `a` and `b` at the end of stack.  
Otherwise return `false`.

**Java**

    
    
         public boolean isValid(String s) {
            Stack<Character> stack = new Stack<>();
            for (char c: s.toCharArray()) {
                if (c == 'c') {
                    if (stack.isEmpty() || stack.pop() != 'b') return false;
                    if (stack.isEmpty() || stack.pop() != 'a') return false;
                } else {
                    stack.push(c);
                }
            }
            return stack.isEmpty();
        }
    

**C++**

    
    
         bool isValid(string S) {
            vector<char> stack;
            for (char c : S) {
                if (c == 'c') {
                    int n = stack.size();
                    if (n < 2 || stack[n - 1] != 'b' || stack[n - 2] != 'a') return false;
                    stack.pop_back(), stack.pop_back();
                } else {
                    stack.push_back(c);
                }
            }
            return stack.size() == 0;
        }
    

**Python**

    
    
         def isValid(self, S):
            stack = []
            for i in S:
                if i == 'c':
                    if stack[-2:] != ['a', 'b']:
                        return False
                    stack.pop()
                    stack.pop()
                else:
                    stack.append(i)
            return not stack
    

  

## Solution 3, Accepted Wrong Solution...**

One wrong solution is that check that if `count[a] >= count[b] >= count[c]`
always valid.  
It's hard for Leetcode to construct all kinds of false positive test cases.  
One easy counterexample can be `"aabbcc"`, expecting Leetcode to add it later.

    
    
        def isValid(self, S):
            count = [0, 0, 0]
            for i in S:
                count[ord(i) - ord('a')] += 1
                if not count[0] >= count[1] >= count[2]:
                    return False
            return count[0] == count[1] == count[2]
    


### Solution 2
We search for "abc" substring and remove it from the string. If, in the end,
the string is empty - return true.

    
    
    bool isValid(string S) {
      for (auto i = S.find("abc"); i != string::npos; i = S.find("abc"))
        S.erase(i, 3);
      return S.empty();
    }
    

## Complexity Analysis

Runtime: _O(n * n)_ , where n is the number of characters. `find` is O(n + 3)
and `erase` is O(n), and we repeat it n / 3 times.  
Memory: _O(n)_.


### Solution 3
    
    
        	String abc =  "abc";
        	
        	while(S.contains(abc)) {
        		S = S.replace(abc, "");
        	}
        	
            return S.isEmpty();
    
    
    



