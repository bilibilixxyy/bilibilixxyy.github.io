---
layout: post
title: 402. Remove K Digits
---
### Question
Given a non-negative integer _num_ represented as a string, remove _k_ digits
from the number so that the new number is the smallest possible.

 **Note:**  

  * The length of _num_ is less than 10002 and will be ≥ _k_.
  * The given _num_ does not contain any leading zero.

 **Example 1:**

    
    
    Input: num = "1432219", k = 3
    Output: "1219"
    Explanation: Remove the three digits 4, 3, and 2 to form the new number 1219 which is the smallest.
    

**Example 2:**

    
    
    Input: num = "10200", k = 1
    Output: "200"
    Explanation: Remove the leading 1 and the number is 200. Note that the output must not contain leading zeroes.
    

**Example 3:**

    
    
    Input: num = "10", k = 2
    Output: "0"
    Explanation: Remove all the digits from the number and it is left with nothing which is 0.
    

### Solution 1
    
    
    public  class Solution {
        public String removeKdigits(String num, int k) {
            int len = num.length();
            //corner case
            if(k==len)        
                return "0";
                
            Stack<Character> stack = new Stack<>();
            int i =0;
            while(i<num.length()){
                //whenever meet a digit which is less than the previous digit, discard the previous one
                while(k>0 && !stack.isEmpty() && stack.peek()>num.charAt(i)){
                    stack.pop();
                    k--;
                }
                stack.push(num.charAt(i));
                i++;
            }
            
            // corner case like "1111"
            while(k>0){
                stack.pop();
                k--;            
            }
            
            //construct the number from the stack
            StringBuilder sb = new StringBuilder();
            while(!stack.isEmpty())
                sb.append(stack.pop());
            sb.reverse();
            
            //remove all the 0 at the head
            while(sb.length()>1 && sb.charAt(0)=='0')
                sb.deleteCharAt(0);
            return sb.toString();
        }
    }
    


### Solution 2
    
    
    public class Solution {
        public String removeKdigits(String num, int k) {
            int digits = num.length() - k;
            char[] stk = new char[num.length()];
            int top = 0;
            // k keeps track of how many characters we can remove
            // if the previous character in stk is larger than the current one
            // then removing it will get a smaller number
            // but we can only do so when k is larger than 0
            for (int i = 0; i < num.length(); ++i) {
                char c = num.charAt(i);
                while (top > 0 && stk[top-1] > c && k > 0) {
                    top -= 1;
                    k -= 1;
                }
                stk[top++] = c;
            }
            // find the index of first non-zero digit
            int idx = 0;
            while (idx < digits && stk[idx] == '0') idx++;
            return idx == digits? "0": new String(stk, idx, digits - idx);
        }
    }
    


### Solution 3
The first algorithm is straight-forward. Let's think about the simplest case:
how to remove 1 digit from the number so that the new number is the smallest
possible？ Well, one can simply scan from left to right, and remove the first
"peak" digit; the peak digit is larger than its right neighbor. One can repeat
this procedure k times, and obtain the first algorithm:

    
    
    string removeKdigits(string num, int k) {
            while (k > 0) {
                int n = num.size();
                int i = 0;
                while (i+1<n && num[i]<=num[i+1])  i++;
                num.erase(i, 1);
                k--;
            }
            // trim leading zeros
            int s = 0;
            while (s<(int)num.size()-1 && num[s]=='0')  s++;
            num.erase(0, s);
            
            return num=="" ? "0" : num;
        }
    

The above algorithm is a bit inefficient because it frequently remove a
particular element from a string and has complexity O(k*n).

One can simulate the above procedure by using a stack, and obtain a O(n)
algorithm. Note, when the result stack (i.e. res) pop a digit, it is
equivalent as remove that "peak" digit.

    
    
    string removeKdigits(string num, int k) {
            string res;
            int keep = num.size() - k;
            for (int i=0; i<num.size(); i++) {
                while (res.size()>0 && res.back()>num[i] && k>0) {
                    res.pop_back();
                    k--;
                }
                res.push_back(num[i]);
            }
            res.erase(keep, string::npos);
            
            // trim leading zeros
            int s = 0;
            while (s<(int)res.size()-1 && res[s]=='0')  s++;
            res.erase(0, s);
            
            return res=="" ? "0" : res;
        }
    



