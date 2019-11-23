---
layout: post
title: 834. Ambiguous Coordinates
---
### Question
We had some 2-dimensional coordinates, like `"(1, 3)"` or `"(2, 0.5)"`.  Then,
we removed all commas, decimal points, and spaces, and ended up with the
string `S`.  Return a list of strings representing all possibilities for what
our original coordinates could have been.

Our original representation never had extraneous zeroes, so we never started
with numbers like "00", "0.0", "0.00", "1.0", "001", "00.01", or any other
number that can be represented with less digits.  Also, a decimal point within
a number never occurs without at least one digit occuring before it, so we
never started with numbers like ".1".

The final answer list can be returned in any order.  Also note that all
coordinates in the final answer have exactly one space between them (occurring
after the comma.)

    
    
     **Example 1:**
    **Input:** "(123)"
    **Output:** ["(1, 23)", "(12, 3)", "(1.2, 3)", "(1, 2.3)"]
    
    
    
    **Example 2:**
    **Input:** "(00011)"
    **Output:**   ["(0.001, 1)", "(0, 0.011)"]
    **Explanation:** 
    0.0, 00, 0001 or 00.01 are not allowed.
    
    
    
    **Example 3:**
    **Input:** "(0123)"
    **Output:** ["(0, 123)", "(0, 12.3)", "(0, 1.23)", "(0.1, 23)", "(0.1, 2.3)", "(0.12, 3)"]
    
    
    
    **Example 4:**
    **Input:** "(100)"
    **Output:** [(10, 0)]
    **Explanation:** 
    1.0 is not allowed.
    



 **Note:**

  * `4 <= S.length <= 12`.
  * `S[0]` = "(", `S[S.length - 1]` = ")", and the other elements in `S` are digits.

### Solution 1
We can split S to two parts for two coordinates.  
Then we use sub function `f` to find all possible strings for each coordinate.

 **In sub functon f(S)**  
if S == "": return []  
if S == "0": return [S]  
if S == "0XXX0": return []  
if S == "0XXX": return ["[0.XXX](http://0.XXX)"]  
if S == "XXX0": return [S]  
return [S, "[X.XXX](http://X.XXX)", "XX.XX", "XXX.X"...]

Then we add the product of two lists to result.

 **Time complexity**  
O(N^3) with N <= 10

Provement:  
![image](https://s3-lc-
upload.s3.amazonaws.com/users/lee215/image_1523920967.png)

C++:

    
    
        vector<string> ambiguousCoordinates(string S) {
            int n = S.size();
            vector<string> res;
            for (int i = 1; i < n - 2; ++i) {
                vector<string> A = f(S.substr(1, i)), B = f(S.substr(i + 1, n - 2 - i));
                for (auto & a : A) for (auto & b : B) res.push_back("(" + a + ", " + b + ")");
            }
            return res;
        }
        vector<string> f(string S) {
            int n = S.size();
            if (n == 0 || (n > 1 && S[0] == '0' && S[n - 1] == '0')) return {};
            if (n > 1 && S[0] == '0') return {"0." + S.substr(1)};
            if (n == 1 || S[n - 1] == '0') return {S};
            vector<string> res = {S};
            for (int i = 1; i < n; ++i) res.push_back(S.substr(0, i) + '.' + S.substr(i));
            return res;
        }
    

**Java:**

    
    
        public List<String> ambiguous Coordinates(String S) {
            int n = S.length();
            List<String> res = new ArrayList();
            for (int i = 1; i < n - 2; ++i) {
                List<String> A = f(S.substring(1, i + 1)), B = f(S.substring(i + 1, n - 1));
                for (String a : A) for (String b : B) res.add("(" + a + ", " + b + ")");
            }
            return res;
        }
        public List<String> f(String S) {
            int n = S.length();
            List<String> res = new ArrayList();
            if (n == 0 || (n > 1 && S.charAt(0) == '0' && S.charAt(n - 1) == '0')) return res;
            if (n > 1 && S.charAt(0) == '0') {
                res.add("0." + S.substring(1));
                return res;
            }
            res.add(S);
            if (n == 1 || S.charAt(n - 1) == '0') return res;
            for (int i = 1; i < n; ++i) res.add(S.substring(0, i) + '.' + S.substring(i));
            return res;
        }
    

**Python:**

    
    
         def ambiguousCoordinates(self, S):
            S = S[1:-1]
            def f(S):
                if not S or len(S) > 1 and S[0] == S[-1] == '0': return []
                if S[-1] == '0': return [S]
                if S[0] == '0': return [S[0] + '.' + S[1:]]
                return [S] + [S[:i] + '.' + S[i:] for i in range(1, len(S))]
            return ['(%s, %s)' % (a, b) for i in range(len(S)) for a, b in itertools.product(f(S[:i]), f(S[i:]))]
    


### Solution 2
    
    
    class Solution {
        public List<String> ambiguousCoordinates(String S) {
            S = S.substring(1, S.length() - 1);
            List<String> result = new LinkedList<>();
            for (int i = 1; i < S.length(); i++) {
                List<String> left = allowed(S.substring(0, i));
                List<String> right = allowed(S.substring(i));
                for (String ls : left) {
                    for (String rs : right) {
                        result.add("(" + ls + ", " + rs + ")");
                    }
                }
            }
            return result;
        }
        private List<String> allowed(String s) {
            int l = s.length();
            char[] cs = s.toCharArray();
            List<String> result = new LinkedList<>();
            if (cs[0] == '0' && cs[l - 1] == '0') { // "0xxxx0" Invalid unless a single "0"
                if (l == 1) {
                    result.add("0");
                }
                return result;
            }
            if (cs[0] == '0') { // "0xxxxx" The only valid result is "0.xxxxx"
                result.add("0." + s.substring(1));
                return result;
            }
            if (cs[l - 1] == '0') { // "xxxxx0" The only valid result is itself
                result.add(s);
                return result;
            }
            result.add(s); // Add itself
            for (int i = 1; i < l; i++) { // "xxxx" -> "x.xxx", "xx.xx", "xxx.x"
                result.add(s.substring(0, i) + '.' + s.substring(i));
            }
            return result;
        }
    }
    


### Solution 3
    
    
    vector<string> cases(string &&s) {
        if (s.size() == 1) // single digit
            return {s};
        if (s.front() == '0') { // 0xxx
            if (s.back() == '0') // 0xxx0
                return {};
            return {"0." + s.substr(1)}; // 0xxx9
        }
        if (s.back() == '0') // 9xxx0
            return {s};
        vector<string> res{s}; // 9xxx9
        for (int i = 1; i < s.size(); i++)
            res.emplace_back(s.substr(0, i) + "." + s.substr(i));
        return res;
    }
    
    vector<string> ambiguousCoordinates(string S) {
        vector<string> res;
        for (int i = 2; i < S.size() - 1; i++) // position of comma
        for (auto &x : cases(S.substr(1, i - 1)))
        for (auto &y : cases(S.substr(i, S.size() - i - 1)))
            res.emplace_back("(" + x + ", " + y + ")");
        return res;
    }
    



