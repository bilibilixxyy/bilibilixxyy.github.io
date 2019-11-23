---
layout: post
title: 166. Fraction to Recurring Decimal
---
### Question
Given two integers representing the numerator and denominator of a fraction,
return the fraction in string format.

If the fractional part is repeating, enclose the repeating part in
parentheses.

 **Example 1:**

    
    
     **Input:** numerator = 1, denominator = 2
    **Output:** "0.5"
    

**Example 2:**

    
    
    **Input:** numerator = 2, denominator = 1
    **Output:** "2"

**Example 3:**

    
    
    **Input:** numerator = 2, denominator = 3
    **Output:** "0.(6)"
    

### Solution 1
The important thing is to consider all edge cases while thinking this problem
through, including: negative integer, possible overflow, etc.

Use HashMap to store a remainder and its associated index while doing the
division so that whenever a same remainder comes up, we know there is a
repeating fractional part.

Please comment if you see something wrong or can be improved. Cheers!

    
    
    public class Solution {
        public String fractionToDecimal(int numerator, int denominator) {
            if (numerator == 0) {
                return "0";
            }
            StringBuilder res = new StringBuilder();
            // "+" or "-"
            res.append(((numerator > 0) ^ (denominator > 0)) ? "-" : "");
            long num = Math.abs((long)numerator);
            long den = Math.abs((long)denominator);
            
            // integral part
            res.append(num / den);
            num %= den;
            if (num == 0) {
                return res.toString();
            }
            
            // fractional part
            res.append(".");
            HashMap<Long, Integer> map = new HashMap<Long, Integer>();
            map.put(num, res.length());
            while (num != 0) {
                num *= 10;
                res.append(num / den);
                num %= den;
                if (map.containsKey(num)) {
                    int index = map.get(num);
                    res.insert(index, "(");
                    res.append(")");
                    break;
                }
                else {
                    map.put(num, res.length());
                }
            }
            return res.toString();
        }
    }


### Solution 2
    
    
    // upgraded parameter types
    string fractionToDecimal(int64_t n, int64_t d) {
        // zero numerator
        if (n == 0) return "0";
    
        string res;
        // determine the sign
        if (n < 0 ^ d < 0) res += '-';
    
        // remove sign of operands
        n = abs(n), d = abs(d);
    
        // append integral part
        res += to_string(n / d);
    
        // in case no fractional part
        if (n % d == 0) return res;
    
        res += '.';
    
        unordered_map<int, int> map;
    
        // simulate the division process
        for (int64_t r = n % d; r; r %= d) {
    
            // meet a known remainder
            // so we reach the end of the repeating part
            if (map.count(r) > 0) {
                res.insert(map[r], 1, '(');
                res += ')';
                break;
            }
    
            // the remainder is first seen
            // remember the current position for it
            map[r] = res.size();
    
            r *= 10;
    
            // append the quotient digit
            res += to_string(r / d);
        }
    
        return res;
    }


### Solution 3
For the decimal part to recur, **the remainder should recur**. So we maintain
the remainders we have seen. Once we see an existing remainder, we have
reached the end of the recurring part (enclose it with a `)`). Moreover,
insert the `(` to the starting index of the recurring part by maintaining a
mapping from each remainder to the index of the corresponding digit and using
it to retrieve the starting index.

For those without fractional parts or with non-recursive fractional points, we
may find them out by `%` or zero remainder. Some other problems that need to
be considered include the sign and overflow (`-2147483648 / -1`).

    
    
    class Solution {
    public:
        string fractionToDecimal(int numerator, int denominator) {
            if (!numerator) {
                return "0";
            }
            string ans;
            if (numerator > 0 ^ denominator > 0) {
                ans += '-';
            }
            long n = labs(numerator), d = labs(denominator), r = n % d;
            ans += to_string(n / d);
            if (!r) {
                return ans;
            }
            ans += '.';
            unordered_map<long, int> rs;
            while (r) {
                if (rs.find(r) != rs.end()) {
                    ans.insert(rs[r], "(");
                    ans += ')';
                    break;
                }
                rs[r] = ans.size();
                r *= 10;
                ans += to_string(r / d);
                r %= d;
            }
            return ans;
        }
    };
    



