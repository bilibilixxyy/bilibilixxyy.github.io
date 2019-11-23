---
layout: post
title: 1071. Binary Prefix Divisible By 5
---
### Question
Given an array `A` of `0`s and `1`s, consider `N_i`: the i-th subarray from
`A[0]` to `A[i]` interpreted as a binary number (from most-significant-bit to
least-significant-bit.)

Return a list of booleans `answer`, where `answer[i]` is `true` if and only if
`N_i` is divisible by 5.

 **Example 1:**

    
    
     **Input:** [0,1,1]
    **Output:** [true,false,false]
    **Explanation:**
    The input numbers in binary are 0, 01, 011; which are 0, 1, and 3 in base-10.  Only the first number is divisible by 5, so answer[0] is true.
    

**Example 2:**

    
    
    **Input:** [1,1,1]
    **Output:** [false,false,false]
    

**Example 3:**

    
    
    **Input:** [0,1,1,1,1,1]
    **Output:** [true,false,false,false,true,false]
    

**Example 4:**

    
    
    **Input:** [1,1,1,0,1]
    **Output:** [false,false,false,false,false]
    



 **Note:**

  1. `1 <= A.length <= 30000`
  2. `A[i]` is `0` or `1`

### Solution 1
 **Intuition**

  * First, let us see how to append an extra bit at the end of a binary number

  * Any binary number **a kak-1...a0** has the form a0*20 \+ a1*21 \+ ........ a0*2k

  * To free up an extra slot at the end, we can just multiply the original number by 2.

  * Initially, the contribution of each **a i** is 2i. If the number is multiplied by 2, the contribution of each ai become 2i+1.

  * The resulting number is 0*20 \+ a0*21 \+ a1*22 \+ ........ a0*2k+1

  * Consider the number **a kak-1...a00**. As per the above definition, it is clear that the resulting number is just the decimal representation of this number

* * *

  * We've seen how to append an extra bit at the end of a binary number.
  * The extra bit appended by the above method is unset (0).
  * How do we append a set bit? Well, we can just free up a slot at the end (by appending 0) and then add 1 to the resulting number.
  * Since the current rightmost bit is zero, therefore there won't be any carry-effects. Hence the new number would be **a kak-1...a01**

* * *

 **Conclusion**

  * For appending a digit **d** at the end of a binary number **old_number** = **a kak-1...a0**, we can just do **new_number** = old_number*2 + **d**.
  * This gives us the number with binary representation **a kak-1...a0d**

* * *

 **Modular Arithemtic**

  * A number is divisible by 5 **iff** the number is equivalent to **0** in the modular arithemtic of 5.

* * *

 **Naive_Algorithm**

  * Since we know how to append any digit at the end, we start with 0 and keep appending digits. We can get all the numbers in this manner.
  * At each step we can take the modulo with respect to 5. If the modulo is zero, we append **true** to our answer.

* * *

 **Problems**

  * The problem with the above approach is that the number can overflow easily. (As the largest integer that can be accomodated is of 31 bits in C++).

* * *

 **Optimizations**

  * Observe that we only care about the remainder, not the actual number.
  * Use the fact that (a _b + c)%d is same as ((a%d)_ (b%d) + c%d)%d.
  * We now have the relation **new_number** %5 = ((old_number%5)*2 + d)%5;
  * This tells us the if we provide the modulo of the old_number instead of the original number, we'll get the modulo of the new number.
  * This would prevent overflows, since **new_number** is the equivalent representation of the original number in the modular arithemtic of 5.

* * *

 **Optimized Algorithm**

  * Start with **num** =0.
  * For each valid **i** update **num** as **num** = (num*2 + a[i])%5
  * At each stage, if **num** is zero, the substring ending at **i** is divisible by 5.

* * *

 **Time Complexity**

  * We pass each element exactly once. Hence _O(n)_.

    
    
     class Solution
    {
    public:
        vector<bool> prefixesDivBy5(vector<int>& a);
    };
    
    vector<bool> Solution :: prefixesDivBy5(vector<int>& a)
    {
        vector<bool> answer;
        int num=0;
        for(int i=0; i<a.size(); i++)
        {
            num = (num*2 + a[i])%5;
            answer.push_back(num==0);
        }
        return answer;
    }
    


### Solution 2
Since A includes only `0s` and `1s`, we can imitate binary operations.

    
    
        public List<Boolean> prefixesDivBy5(int[] A) {
            int k = 0;
            List<Boolean> ans = new ArrayList<>();
            for (int a : A) {
                k = (k << 1 | a) % 5; // left shift k by 1 bit and plus current element a is the binary number.
                ans.add(k == 0); 
            }
            return ans;
        }
    

**Analysis:**

Time & space: O(n), where n = A.length;

**Q & A:**

Q:  
why do we need to mod 5 there?  
A:  
The problem ask if the binary numbers are divisible by 5.  
Also, mod 5 in each iteration will prevent potential int overflow.

Q:  
can you give me the explanation for why after mod 5 the value of k plus a can
still represent the original value ？  
A:

Rewrite original code as follows, ignore the overflow test cases.

    
    
        public List<Boolean> prefixesDivBy5(int[] A) {
            int[] k = new int[A.length + 1];
            List<Boolean> ans = new ArrayList<>();
            for (int i = 0; i < A.length; ++i) {
                k[i + 1] = (k[i] << 1 | a); // left shift k by 1 bit and plus current element a is the binary number.
                ans.add(k[i + 1] % 5 == 0); 
            }
            return ans;
        }
    

i = 0,

    
    
    Let F0 = k[0 + 1] / 5,
        r0  = k[0 + 1] % 5,
    Denote k[0 + 1] (= A[0]) = 5 * F0 + r0, 
           k[0 + 1] % 5 = r0
    
    similarly,
    i = 1,
    

k[1 + 1] = 2 * (5 * F0 + r0) + A[1]  
= 2 * 5 * F0 + ( **2 * r0 + A[1]** )  
= 2 * 5 * F0 + (5 * F1 + r1)  
k[1 + 1] % 5 = **(2 * r0 + A[1]) % 5**  
= r1

    
    
     i = 2,
    

k[2 + 1] = 2 * (2 * 5 * F0 + (5 * F1 + r1)) + A[2]  
= (4 * 5 * F0 + 2 * 5 * F1) + ( **2 * r1 + A[2]** )  
= 5 * (4 * F0 + 2 * F1) + (5 * F2 + r2)  
k[2 + 1] % 5 = ( **2 * r1 + A[2]** ) % 5  
= r2

    
    
    repeat the above till the end of the array...
    
    Look at the bold part of the derivation, which is consistent with the original code. Let me know if it answers your question.
    


### Solution 3
I changed the input. Maybe you don't like it.  
 **Python:**

    
    
        def prefixesDivBy5(self, A):
             for i in xrange(1, len(A)):
                A[i] += A[i - 1] * 2 % 5
            return [a % 5 == 0 for a in A]
    



