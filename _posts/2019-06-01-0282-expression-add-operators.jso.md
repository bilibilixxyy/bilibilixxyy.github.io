---
layout: post
title: 282. Expression Add Operators
---
### Question
Given a string that contains only digits `0-9` and a target value, return all
possibilities to add **binary** operators (not unary) `+`, `-`, or `*` between
the digits so they evaluate to the target value.

 **Example 1:**

    
    
     **Input:** _num_ = "123", _target_ = 6
    **Output:** ["1+2+3", "1*2*3"] 
    

**Example 2:**

    
    
    **Input:** _num_ = "232", _target_ = 8
    **Output:** ["2*3+2", "2+3*2"]

**Example 3:**

    
    
    **Input:** _num_ = "105", _target_ = 5
    **Output:** ["1*0+5","10-5"]

**Example 4:**

    
    
    **Input:** _num_ = "00", _target_ = 0
    **Output:** ["0+0", "0-0", "0*0"]
    

**Example 5:**

    
    
    **Input:** _num_ = "3456237490", _target_ = 9191
    **Output:** []
    

### Solution 1
This problem has a lot of edge cases to be considered:

  1. overflow: we use a long type once it is larger than Integer.MAX_VALUE or minimum, we get over it.
  2. 0 sequence: because we can't have numbers with multiple digits started with zero, we have to deal with it too.
  3. a little trick is that we should save the value that is to be multiplied in the next recursion.

* * *
    
    
    public class Solution {
        public List<String> addOperators(String num, int target) {
            List<String> rst = new ArrayList<String>();
            if(num == null || num.length() == 0) return rst;
            helper(rst, "", num, target, 0, 0, 0);
            return rst;
        }
        public void helper(List<String> rst, String path, String num, int target, int pos, long eval, long multed){
            if(pos == num.length()){
                if(target == eval)
                    rst.add(path);
                return;
            }
            for(int i = pos; i < num.length(); i++){
                if(i != pos && num.charAt(pos) == '0') break;
                long cur = Long.parseLong(num.substring(pos, i + 1));
                if(pos == 0){
                    helper(rst, path + cur, num, target, i + 1, cur, cur);
                }
                else{
                    helper(rst, path + "+" + cur, num, target, i + 1, eval + cur , cur);
                    
                    helper(rst, path + "-" + cur, num, target, i + 1, eval -cur, -cur);
                    
                    helper(rst, path + "*" + cur, num, target, i + 1, eval - multed + multed * cur, multed * cur );
                }
            }
        }
    }


### Solution 2
    
    
    class Solution {
    private:
        // cur: {string} expression generated so far.
        // pos: {int}    current visiting position of num.
        // cv:  {long}   cumulative value so far.
        // pv:  {long}   previous operand value.
        // op:  {char}   previous operator used.
        void dfs(std::vector<string>& res, const string& num, const int target, string cur, int pos, const long cv, const long pv, const char op) {
            if (pos == num.size() && cv == target) {
                res.push_back(cur);
            } else {
                for (int i=pos+1; i<=num.size(); i++) {
                    string t = num.substr(pos, i-pos);
                    long now = stol(t);
                    if (to_string(now).size() != t.size()) continue;
                    dfs(res, num, target, cur+'+'+t, i, cv+now, now, '+');
                    dfs(res, num, target, cur+'-'+t, i, cv-now, now, '-');
                    dfs(res, num, target, cur+'*'+t, i, (op == '-') ? cv+pv - pv*now : ((op == '+') ? cv-pv + pv*now : pv*now), pv*now, op);
                }
            }
        }
    
    public:
        vector<string> addOperators(string num, int target) {
            vector<string> res;
            if (num.empty()) return res;
            for (int i=1; i<=num.size(); i++) {
                string s = num.substr(0, i);
                long cur = stol(s);
                if (to_string(cur).size() != s.size()) continue;
                dfs(res, num, target, s, i, cur, cur, '#');         // no operator defined.
            }
    
            return res;
        }
    };


### Solution 3
I am surprised that it beats 100.00% other solutions, so i have to share this.

    
    
    void dfs(List<String> ret, char[] path, int len, long left, long cur, char[] digits, int pos, int target) {
        if (pos == digits.length) {
            if (left + cur == target) ret.add(new String(path, 0, len));
            return;
        }
        long n = 0;
        int j = len + 1;
        for (int i = pos; i < digits.length; i++) {
            n = n * 10 + digits[i] - '0';
            path[j++] = digits[i];
            path[len] = '+';
            dfs(ret, path, j, left + cur, n, digits, i + 1, target);
            path[len] = '-';
            dfs(ret, path, j, left + cur, -n, digits, i + 1, target);
            path[len] = '*';
            dfs(ret, path, j, left, cur * n, digits, i + 1, target);
            if (digits[pos] == '0') break; 
        }
    }
    public List<String> addOperators(String num, int target) {
        List<String> ret = new LinkedList<>();
        if (num.length() == 0) return ret;
        char[] path = new char[num.length() * 2 - 1];
        char[] digits = num.toCharArray();
        long n = 0;
        for (int i = 0; i < digits.length; i++) {
            n = n * 10 + digits[i] - '0';
            path[i] = digits[i];
            dfs(ret, path, i + 1, 0, n, digits, i + 1, target);
            if (n == 0) break;
        }
        return ret;
    }



