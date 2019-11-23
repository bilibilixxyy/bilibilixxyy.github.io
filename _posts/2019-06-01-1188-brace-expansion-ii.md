---
layout: post
title: 1188. Brace Expansion II
---
### Question
Under a grammar given below, strings can represent a set of lowercase words.
Let's use `R(expr)` to denote the **set** of words the expression represents.

Grammar can best be understood through simple examples:

  * Single letters represent a singleton set containing that word. 
    * `R("a") = {"a"}`
    * `R("w") = {"w"}`
  * When we take a comma delimited list of 2 or more expressions, we take the union of possibilities. 
    * `R("{a,b,c}") = {"a","b","c"}`
    * `R("{{a,b},{b,c}}") = {"a","b","c"}` (notice the final set only contains each word at most once)
  * When we concatenate two expressions, we take the set of possible concatenations between two words where the first word comes from the first expression and the second word comes from the second expression. 
    * `R("{a,b}{c,d}") = {"ac","ad","bc","bd"}`
    * `R("a{b,c}{d,e}f{g,h}") = {"abdfg", "abdfh", "abefg", "abefh", "acdfg", "acdfh", "acefg", "acefh"}`

Formally, the 3 rules for our grammar:

  * For every lowercase letter `x`, we have `R(x) = {x}`
  * For expressions `e_1, e_2, ... , e_k` with `k >= 2`, we have `R({e_1,e_2,...}) = R(e_1) ∪ R(e_2) ∪ ...`
  * For expressions `e_1` and `e_2`, we have `R(e_1 + e_2) = {a + b for (a, b) in R(e_1) × R(e_2)}`, where + denotes concatenation, and × denotes the cartesian product.

Given an `expression` representing a set of words under the given grammar,
return the sorted list of words that the expression represents.



 **Example 1:**

    
    
     **Input:** "{a,b}{c,{d,e}}"
    **Output:** ["ac","ad","ae","bc","bd","be"]
    

**Example 2:**

    
    
    **Input:** "{{a,z},a{b,c},{ab,z}}"
    **Output:** ["a","ab","ac","z"]
    **Explanation:** Each distinct word is written only once in the final answer.
    



 **Constraints:**

  1. `1 <= expression.length <= 50`
  2. `expression[i]` consists of `'{'`, `'}'`, `','`or lowercase English letters.
  3. The given `expression` represents a set of words based on the grammar given in the description.

### Solution 1
# The general idea

 **Split expressions into groups separated by top level`','`; for each top-
level sub expression (substrings with braces), process it and add it to the
corresponding group; finally combine the groups and sort.**

## Thought process

  * in each call of the function, try to remove one level of braces
  * maintain a list of groups separated by top level `','`
    * when meets `','`: create a new empty group as the current group
    * when meets `'{'`: check whether current `level` is 0, if so, record the starting index of the sub expression;
      * always increase `level` by 1, no matter whether current level is 0
    * when meets `'}'`: decrease `level` by 1; then check whether current `level` is 0, if so, recursively call `braceExpansionII` to get the set of words from `expresion[start: end]`, where `end` is the current index (exclusive).
      * add the expanded word set to the current group
    * when meets a letter: check whether the current `level` is 0, if so, add `[letter]` to the current group
    *  **base case: when there is no brace in the expression.**
  * finally, after processing all sub expressions and collect all groups (seperated by `','`), we initialize an empty word_set, and then iterate through all groups
    * for each group, find the product of the elements inside this group
    * compute the union of all groups
  * sort and return
  * note that `itertools.product(*group)` returns an iterator of **tuples** of characters, so we use`''.join()` to convert them to strings

    
    
     class Solution:
        def braceExpansionII(self, expression: str) -> List[str]:
            groups = [[]]
            level = 0
            for i, c in enumerate(expression):
                if c == '{':
                    if level == 0:
                        start = i+1
                    level += 1
                elif c == '}':
                    level -= 1
                    if level == 0:
                        groups[-1].append(self.braceExpansionII(expression[start:i]))
                elif c == ',' and level == 0:
                    groups.append([])
                elif level == 0:
                    groups[-1].append([c])
            word_set = set()
            for group in groups:
                word_set |= set(map(''.join, itertools.product(*group)))
            return sorted(word_set)
    


### Solution 2
题意：告诉我们三个规则。

规则一：单个字符串集合，可以表示为`a`，代表一个字符串`"a"`。  
规则二、字符串集合的并集，表示为`{a,b,c,d}`，代表`"a","b","c"`四个字符串。  
规则三、字符串集合的叉集，表示为`{a,b}{c,d}e`，代表`"ace","ade","bce","bde"`四个字符串。

问题：告诉我们一个表达式，求最终的字符串集合。

思路： 这个规则描述其实不太好实现，我们稍微转化一下就简单了。

定义：`a,b,c,d,e,f`都是符合规则的表达式。  
规则零、字符串，使用`[a-z]+`表示，代表一个集合，只有一个字符串。  
规则一、字符串集合，使用`{a}`表示，代表`a`是一个合法的表达式，对应的结果是一个字符串集合。  
规则二、字符串集合的并集，使用`a,b,c,d`表示，代表`a,b,c,d`四个集合求并集。  
规则三、字符串集合的叉集，使用`abcd`表示，代表`a,b,c,d`四个集合求叉集。  
叉集定义（两个集合为例）：第一个集合里的任何一个元素与第二个集合里的任何一个元素拼接在一起所形成的的字符串集合。

按照这个定义，问题给的表达式其实属于规则二。  
规则一由花括号与规则二组成。  
规则二由若干个逗号分隔的规则三组成。  
规则三由若干相连的规则零或规则一组成。

由此，我们可以写出对应的递归方程来。

    
    
    class Solution {
        set<string> merge(set<string>&a, set<string>&b){
            if(a.size() == 0){
                return b;
            }
            if(b.size() == 0){
                return a;
            }
            set<string> ans;
            for(auto& v1: a){
                for(auto& v2: b){
                    ans.insert(v1+v2);
                }
            }
            return ans;
        }
        
        //{a,b,c}
        set<string> parseRule1(const string& str, int& i){
            set<string> ans;
            i++;
            ans = parseRule2(str, i);
            i++;
            return ans;
        }
        //{a,b},{c,d}
        set<string> parseRule2(const string& str, int& i){
            set<string> ans;
            ans = parseRule3(str, i);
            while(i<str.length()){
                if(str[i] != ','){
                    break;
                }
                i++;
                set<string> tmp = parseRule3(str, i);
                ans.insert(tmp.begin(), tmp.end());
            }
            return ans;
        }
        //a{c,d}b{e,f}
        set<string> parseRule3(const string& str, int& i){
            set<string> ans;
            while(i < str.length()){
                if(str[i] == '}' || str[i] == ','){
                    break;
                }
                if(str[i] == '{'){
                    set<string> tmp = parseRule1(str, i);
                    ans = merge(ans, tmp);
                }else{
                    set<string> tmp;
                    string tmpStr;
                    while(i < str.length() && str[i] <= 'z' && str[i] >= 'a'){
                        tmpStr.push_back(str[i++]);
                    }
                    tmp.insert(tmpStr);
                    ans = merge(ans, tmp);
                }
            }
            return ans;
        }
    public:
        vector<string> braceExpansionII(string str) {
            int pos = 0;
            set<string> ans = parseRule2(str, pos);
            return vector<string>(ans.begin(), ans.end());
        }
    };
    

或者简化规则，只有两个规则。  
规则1：表达式是一个集合的交集，即 `ab`  
规则2：表达式是一个集合的并集，即`a,b,c,d`

    
    
    class Solution {
        set<string> merge(set<string>&a, set<string>&b){
            if(a.size() == 0){
                return b;
            }
            if(b.size() == 0){
                return a;
            }
            set<string> ans;
            for(auto& v1: a){
                for(auto& v2: b){
                    ans.insert(v1+v2);
                }
            }
            return ans;
        }
        //{a,b},{c,d}
        set<string> parseRule2(const string& str, int& i){
            set<string> ans;
            ans = parseRule3(str, i);
            while(i<str.length()){
                if(str[i] != ','){
                    break;
                }
                i++;
                set<string> tmp = parseRule3(str, i);
                ans.insert(tmp.begin(), tmp.end());
            }
            return ans;
        }
        //a{c,d}b{e,f}
        set<string> parseRule3(const string& str, int& i){
            set<string> ans;
            while(i < str.length()){
                if(str[i] == '}' || str[i] == ','){
                    break;
                }
                if(str[i] == '{'){
    				i++;
                    set<string> tmp = parseRule2(str, i);
    				i++;
                    ans = merge(ans, tmp);
                }else{
                    set<string> tmp;
                    string tmpStr;
                    while(i < str.length() && str[i] <= 'z' && str[i] >= 'a'){
                        tmpStr.push_back(str[i++]);
                    }
                    tmp.insert(tmpStr);
                    ans = merge(ans, tmp);
                }
            }
            return ans;
        }
    public:
        vector<string> braceExpansionII(string str) {
            int pos = 0;
            set<string> ans = parseRule2(str, pos);
            return vector<string>(ans.begin(), ans.end());
        }
    };
    


### Solution 3
<https://www.youtube.com/watch?v=BbqQ-JaWD3g>



