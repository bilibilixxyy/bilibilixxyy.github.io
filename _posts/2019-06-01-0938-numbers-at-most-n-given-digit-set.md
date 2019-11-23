---
layout: post
title: 938. Numbers At Most N Given Digit Set
---
### Question
We have a **sorted** set of digits `D`, a non-empty subset of
`{'1','2','3','4','5','6','7','8','9'}`.  (Note that `'0'` is not included.)

Now, we write numbers using these digits, using each digit as many times as we
want.  For example, if `D = {'1','3','5'}`, we may write numbers such as
`'13', '551', '1351315'`.

Return the number of positive integers that can be written (using the digits
of `D`) that are less than or equal to `N`.



 **Example 1:**

    
    
     **Input:** D = ["1","3","5","7"], N = 100
    **Output:** 20
    **Explanation:**
    The 20 numbers that can be written are:
    1, 3, 5, 7, 11, 13, 15, 17, 31, 33, 35, 37, 51, 53, 55, 57, 71, 73, 75, 77.
    

**Example 2:**

    
    
    **Input:** D = ["1","4","9"], N = 1000000000
    **Output:** 29523
    **Explanation:**
    We can write 3 one digit numbers, 9 two digit numbers, 27 three digit numbers,
    81 four digit numbers, 243 five digit numbers, 729 six digit numbers,
    2187 seven digit numbers, 6561 eight digit numbers, and 19683 nine digit numbers.
    In total, this is 29523 integers that can be written using the digits of D.



 **Note:**

  1. `D` is a subset of digits `'1'-'9'` in sorted order.
  2. `1 <= N <= 10^9`

### Solution 1
**Python:**

    
    
        def atMostNGivenDigitSet(self, D,  N):
            N = str(N)
            n = len(N)
            res = sum(len(D) ** i for i in range(1, n))
            i = 0
            while i < len(N):
                res += sum(c < N[i] for c in D) * (len(D) ** (n - i - 1))
                if N[i] not in D: break
                i += 1
            return res + (i == n)
    


### Solution 2
Take N = 2563, D = {"1", "2", "6"} as an example,  
The first loop handles the count of x, xx, xxx which x belongs to D. the sum
is 3^1 + 3^2 + 3^3.  
The second loop handles xxxx from left most digit.  
For example,  
count of 1xxx is 3^3  
count of 21xx is 3^2  
count of 22xx is 3^2

If the elements of D can compose entired N, answer + 1  
Although it could be coded in a single loop, the logic would be unclear to me.
I keep them seperated.

    
    
    class Solution {
    public:
        int atMostNGivenDigitSet(vector<string>& D, int N) {
            string NS = to_string(N);
            int digit = NS.size(), dsize = D.size(), rtn = 0;
            
            for (int i = 1 ; i < digit ; ++i)
                rtn += pow(dsize, i);
            
            for (int i = 0 ; i < digit ; ++i) {
                bool hasSameNum = false;
                for (string &d : D) {
                    if (d[0] < NS[i]) 
                        rtn += pow(dsize, digit - i - 1);
                    else if (d[0] == NS[i]) 
                        hasSameNum = true;
                }
                if (!hasSameNum) return rtn;
            }               
            return rtn+1;
        }
    };
    


### Solution 3
Explanation:

  1. Breaking the problem to finding the count of digits with exactly K digits. Iterating form K=1 to number of digits in N
  2. Now comes checking the count of K digits
    1. If number of digits is less than K, then answer is all possible permutations which would be `Math.pow(D.length,K)`
    2. If number of digits is `0` return `0`
    3. If number of digits is exactly equal to K, then check the number of digits that can be formed by placing `D[i]` at highest position and count of K-1 digits.

My java solution -

    
    
    class Solution {
        public int atMostNGivenDigitSet(String[] D, int N) {
            int result = 0;
            for(int i=1;i<=Integer.toString(N).length();i++){
                result += helper(D,i,Integer.toString(N));
            }
            return result;
        }
        
        //exactly N digit
        public int helper(String[] D, int K, String smax){
            if (smax.equals("0")) {
                return 0;
            }
            // String smax = Integer.toString(max);
            if(smax.length()>K){
                return (int)Math.pow(D.length,K);
            }
            int count=0;
            for(int i=0;i<D.length;i++){
                int char0 = Integer.parseInt(smax.charAt(0));
                if(Integer.parseInt(D[i])<char0){
                    count += helper(D,K-1,smax);
                }
                if(Integer.parseInt(D[i])==char0){
                    if (smax.length() > 1) {
                        int charRem = Integer.parseInt(smax.substring(1, 2)) == 0 ? 0 : Integer.parseInt(smax.substring(1));
                        count += helper(D, K - 1, Integer.toString(charRem));
                    } else {
                        count++;
                    }
    
                }
            }
            return count;
        }
    }
    



