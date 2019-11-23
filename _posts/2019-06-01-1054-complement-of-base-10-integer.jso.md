---
layout: post
title: 1054. Complement of Base 10 Integer
---
### Question
Every non-negative integer `N` has a binary representation.  For example, `5`
can be represented as `"101"` in binary, `11` as `"1011"` in binary, and so
on.  Note that except for `N = 0`, there are no leading zeroes in any binary
representation.

The _complement_  of a binary representation is the number in binary you get
when changing every `1` to a `0` and `0` to a `1`.  For example, the
complement of `"101"` in binary is `"010"` in binary.

For a given number `N` in base-10, return the complement of it's binary
representation as a base-10 integer.



 **Example 1:**

    
    
     **Input:** 5
    **Output:** 2
    **Explanation:** 5 is "101" in binary, with complement "010" in binary, which is 2 in base-10.
    

**Example 2:**

    
    
    **Input:** 7
    **Output:** 0
    **Explanation:** 7 is "111" in binary, with complement "000" in binary, which is 0 in base-10.
    

**Example 3:**

    
    
    **Input:** 10
    **Output:** 5
    **Explanation:** 10 is "1010" in binary, with complement "0101" in binary, which is 5 in base-10.
    



 **Note:**

  1. `0 <= N < 10^9`

### Solution 1
##  **Hints**

  1. what is the relationship between input and output
  2. input + output = 111....11 in binary format
  3. Is there any corner case?
  4. 0 is a corner case expecting 1, output > input

##  **Intuition**

Let's find the first number `X` that `X = 1111....1 > N`  
And also, it has to be noticed that,  
`N = 0` is a corner case expecting`1` as result.

  

##  **Solution 1** :

`N + bitwiseComplement(N) = 11....11 = X`  
Then `bitwiseComplement(N) = X - N`

 **Java:**

    
    
         public int bitwiseComplement(int N) {
            int X = 1;
            while (N > X) X = X * 2 + 1;
            return X - N;
        }
    

**C++:**

    
    
         int bitwiseComplement(int N) {
            int X = 1;
            while (N > X) X = X * 2 + 1;
            return X - N;
        }
    

**Python:**

    
    
         def bitwiseComplement(self, N):
            X = 1
            while N > X: X = X * 2 + 1
            return X - N
    

  

## **Solution 2** :

`N ^ bitwiseComplement(N) = 11....11 = X`  
`bitwiseComplement(N) = N ^ X`

**Java:**

    
    
         public int bitwiseComplement(int N) {
            int X = 1;
            while (N > X) X = X * 2 + 1;
            return N ^ X;
        }
    

**C++:**

    
    
         int bitwiseComplement(int N) {
            int X = 1;
            while (N > X) X = X * 2 + 1;
            return N ^ X;
        }
    

**Python:**

    
    
        def bitwiseComplement(self, N):
             X = 1
            while N > X: X = X * 2 + 1;
            return N ^ X
    

  

## **Complexity**

`O(logN)` Time  
`O(1)` Space  
  

## Python 1-lines

Use `bin`

    
    
        def bitwiseComplement(self, N):
            return (1 << len(bin(N)) >> 2) - N - 1
    

Use `translate`

    
    
        def bitwiseComplement(self, N):
            return int(bin(N)[2:].translate(string.maketrans('01', '10')), 2)
    


### Solution 2
**Claim** \------ The _XOR_ operation evaluates the difference in the
individual bits, i.e it gives information about whether the bits are identical
or not.  
 **Proof** \------ It's easy once you know the definition of _XOR_. `0^0` =
`1^1` = `0` (as the bits don't differ), whereas `0^1` = `1^0` = `1` (as the
bits are different).

 **Claim** \------- _XOR_ of identical numbers is zero.  
 **Proof** \------- As argued above, the bits of identical numbers do not
differ at any position. Hence, _XOR_ is zero.

 **Claim** \------- `0` _XOR_ any number is the number itself.  
 **Proof** \------- _XOR_ gives us the bit difference. Since all the bits in
`0` are unset, therefore the difference in bits is the number itself.

 **Claim** \-------- _XOR_ of a number with its complement results in a number
with all set bits.  
 **Proof** \-------- This is trivial, since bits of a number and its
complement differ at every position(according to the definition of
complement).

So, `number` ^ `complement` = `all_set_bits` ==> `number` ^ `number` ^
`complement` = `number` ^ `all_set_bits` ===> `0` ^ `complement` = `number` ^
`all_set_bits`

So, `complement` = `number` ^ `all_set_bits`.

So, we find out the number containing all the set bits and _XOR_ with the
original number to get the answer.

    
    
     class Solution
    {
    public:
        int bitwiseComplement(int n);
    };
    
    int Solution :: bitwiseComplement(int num)
    {
        int all_bits_set = pow(2,0);
        for(int i=1; all_bits_set<num; i++)
            all_bits_set += pow(2,i);
        
        return num xor all_bits_set;
    }
    

[JavaScript Implementation](https://github.com/Just-A-
Visitor/Coding/tree/master/LeetCode%20Contests/LeetCode%20Contest%20%23128/%231012%20Complement%20of%20a%20Base%2010%20Integer)


### Solution 3
Nearly the same as <https://leetcode.com/problems/number-complement/>



