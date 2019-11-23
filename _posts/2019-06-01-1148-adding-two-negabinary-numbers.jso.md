---
layout: post
title: 1148. Adding Two Negabinary Numbers
---
### Question
Given two numbers `arr1` and `arr2` in base **-2** , return the result of
adding them together.

Each number is given in _array format_ :  as an array of 0s and 1s, from most
significant bit to least significant bit.  For example, `arr = [1,1,0,1]`
represents the number `(-2)^3 + (-2)^2 + (-2)^0 = -3`.  A number `arr` in
_array format_ is also guaranteed to have no leading zeros: either  `arr ==
[0]` or `arr[0] == 1`.

Return the result of adding `arr1` and `arr2` in the same format: as an array
of 0s and 1s with no leading zeros.



 **Example 1:**

    
    
     **Input:** arr1 = [1,1,1,1,1], arr2 = [1,0,1]
    **Output:** [1,0,0,0,0]
    **Explanation:** arr1 represents 11, arr2 represents 5, the output represents 16.
    



 **Note:**

  1. `1 <= arr1.length <= 1000`
  2. `1 <= arr2.length <= 1000`
  3. `arr1` and `arr2` have no leading zeros
  4. `arr1[i]` is `0` or `1`
  5. `arr2[i]` is `0` or `1`

### Solution 1
##  **Intuition** :

Quite similar to [1017\. Convert to Base
-2](https://leetcode.com/problems/convert-to-
base-2/discuss/265507/JavaC++Python-2-lines-Exactly-Same-as-Base-2)

  1. Maybe write a base2 addition first?
  2. Add minus `-`?
  3. Done.  
  

##  **Complexity**

Time `O(N)`, Space `O(N)`  
  

 **C++:**

    
    
        vector< int> addBinary(vector<int>& A, vector<int>& B) {
            vector<int> res;
            int carry = 0, i = A.size() - 1, j = B.size() - 1;
            while (i >= 0 || j >= 0 || carry) {
                if (i >= 0) carry += A[i--];
                if (j >= 0) carry += B[j--];
                res.push_back(carry & 1);
                carry = (carry >> 1);
            }
            reverse(res.begin(), res.end());
            return res;
        }
    
        vector<int> addNegabinary(vector<int>& A, vector<int>& B) {
            vector<int> res;
            int carry = 0, i = A.size() - 1, j = B.size() - 1;
            while (i >= 0 || j >= 0 || carry) {
                if (i >= 0) carry += A[i--];
                if (j >= 0) carry += B[j--];
                res.push_back(carry & 1);
                carry = -(carry >> 1);
            }
            while (res.size() > 1 && res.back() == 0)
                res.pop_back();
            reverse(res.begin(), res.end());
            return res;
        }
    

**Python:**

    
    
        def addBinary(self, A, B):
             res = []
            carry = 0
            while A or B or carry:
                carry += (A or [0]).pop() + (B or [0]).pop()
                res.append(carry & 1)
                carry = carry >> 1
            return res[::-1]
    
        def addNegabinary(self, A, B):
            res = []
            carry = 0
            while A or B or carry:
                carry += (A or [0]).pop() + (B or [0]).pop()
                res.append(carry & 1)
                carry = -(carry >> 1)
            while len(res) > 1 and res[-1] == 0:
                res.pop()
            return res[::-1]
    


### Solution 2
See [Wikipedia's Negative Base
Addition](https://en.wikipedia.org/wiki/Negative_base#Addition).

We start from the least signifficant bit, and do the binary summation as
usual. However, our carry can have three states: -1, 0, and 1.

If the sum for the current bit is greater than 2, the carry becomes -1, not 1
as in the base 2 case. Because of that, our sum can become -1. In this case,
the carry should be set to 1.

    
    
    vector<int> addNegabinary(vector<int>& a1, vector<int>& a2) {
      vector<int> res;
      int bit = 0, carry = 0, sz = max(a1.size(), a2.size());
      for (auto bit = 0; bit < sz || carry != 0; ++bit) {
        auto b1 = bit < a1.size() ? a1[a1.size() - bit - 1] : 0;
        auto b2 = bit < a2.size() ? a2[a2.size() - bit - 1] : 0;
        auto sum = b1 + b2 + carry;
        res.push_back(abs(sum) % 2); 
        carry = sum < 0 ? 1 : sum > 1 ? -1 : 0;
      }
      while (res.size() > 1 && res.back() == 0) res.pop_back();
      reverse(begin(res), end(res));
      return res;
    }
    


### Solution 3
题意：给两个以`-2`为基数的数字，求两个数字之和。

思路：对于大整数加法，第一步是翻转字符串，使得低位在前面，高位在后面。  
第二步是高位补零。  
第三步是相加。由于`-2`基数的特殊性，这里我们暂时不管进位问题。  
第四步是处理进位逻辑。  
第五步去前导零。  
第六步翻转结果，即得到答案。

这里最关键的一步是如何处理进位。  
如果当前位的值是`0`和`1`，那就不需要处理。  
如果当前位大于等于`2`，且下一位大于`0`，那么可以抵消，即`2*(-2)^k + (-2)^(k+1) = 0`。  
如果当前位是`2`且下一位为0，则可以转化为后面两位的运算结果，即`2 * (-2)^k = (-2)^(k+1) + (-2)^(k+2)`。

证明：  
当`k`是偶数时，最终结果是`2^(k+1)`，那公式展开`-2^(k+1) + 2^(k+2) = 2^(k+1)`。  
当`k`是奇数时，最终结果是`-2^(k+1)`，那公式展开`2^(k+1) - 2^(k+2) = -2^(k+1)`。

由此，则可以完美的处理所有的进位了。

    
    
    class Solution {
    public:
        vector<int> addNegabinary(vector<int>& arr1, vector<int>& arr2) {
            std::reverse(arr1.begin(), arr1.end());
            std::reverse(arr2.begin(), arr2.end());
            
            int maxSize = 5 + max(arr1.size(), arr2.size());
            vector<int> ans(maxSize, 0);
            
            arr1.resize(maxSize, 0);
            arr2.resize(maxSize, 0);
            
            for(int i=0;i<ans.size();i++){
                ans[i] = arr1[i] + arr2[i];
            }
            
            for(int i=0;i<ans.size();i++){
                while(ans[i] >= 2 && ans[i+1] > 0){
                    ans[i] -= 2;
                    ans[i+1]--;
                }
                
                while(ans[i] >= 2){
                    ans[i] -= 2;
                    ans[i+1]++;
                    ans[i+2]++;
                }
            }
            
            while(ans.size()>1 && ans.back() == 0){
                ans.pop_back();
            }
            
            std::reverse(ans.begin(), ans.end());
            return ans;
        }
    };
    



