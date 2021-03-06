---
layout: post
title: 795. K-th Symbol in Grammar
---
### Question
On the first row, we write a `0`. Now in every subsequent row, we look at the
previous row and replace each occurrence of `0` with `01`, and each occurrence
of `1` with `10`.

Given row `N` and index `K`, return the `K`-th indexed symbol in row `N`. (The
values of `K` are 1-indexed.) (1 indexed).

    
    
     **Examples:**
    **Input:** N = 1, K = 1
    **Output:** 0
    
    **Input:** N = 2, K = 1
    **Output:** 0
    
    **Input:** N = 2, K = 2
    **Output:** 1
    
    **Input:** N = 4, K = 5
    **Output:** 1
    
    **Explanation:**
    row 1: 0
    row 2: 01
    row 3: 0110
    row 4: 01101001
    

**Note:**

  1. `N` will be an integer in the range `[1, 30]`.
  2. `K` will be an integer in the range `[1, 2^(N-1)]`.

### Solution 1
The whole structure can be viewed a binary tree, when a node is 0, their two
children nodes are 0 and 1, similarly, when a node is 1, two children nodes
are 1 and 0. We can know whether the position of K is a left node or a right
node by dividing 2. If K is even, current node is right child, and its parent
is the (K/2)th node in previous row; else if K is odd, current node is left
child and its parent is the ((K+1)/2)th node in previous row.  
The value of current node depends on its parent node, without knowing its
parent node value, we still cannot determine current node value. That's why we
need recursion, we keep going previous row to find the parent node until reach
the first row. Then all the parent node value will be determined after the
recursion function returns.

    
    
    class Solution {
    public:
        int kthGrammar(int N, int K) {
    	if (N == 1) return 0;
    	if (K % 2 == 0) return (kthGrammar(N - 1, K / 2) == 0) ? 1 : 0;
    	else return (kthGrammar(N - 1, (K + 1) / 2) == 0) ? 0 : 1;
        }
    };


### Solution 2
    
    
    public int kthGrammar(int N, int K) {
        return Integer.bitCount(K-1) & 1;
    }
    

_**Updates**_ : `(all index discussed below are 0-based)`  
 _ **Observation 1**_ : **N** does not matter as long as "K will be an integer
in the range [1, 2^(N-1)]". We can ignore **N**.

 _ **Observation 2**_ : let `f(k)` be the value of `k`th position (0-based),
then:  
`f(2 * k) = 0 {if f(k) = 0} or, 1 {if f(k) = 1}` => `f(2 * k) = f(k) xor 0`  
`f(2 * k + 1) = 0 {if f(k) = 1} or 1 {if f(k) = 0}` => `f(2 * k + 1) = f(k)
xor 1`

 _ **Obervation 3**_ : if binary string of **k** is used, let **k = 1001010**
, then we have:  
`f(1001010) = f(100101) ^ 0 = f(10010) ^ 1 ^ 0 = f(1001) ^ 0 ^ 1 ^ 0 = ... =
f(0) ^ 1 ^ 0 ^ 0 ^1 ^ 0 ^ 1 ^ 0 = 1 ^ 0 ^ 0 ^1 ^ 0 ^ 1 ^ 0`  
So, the result is the **xor** operation on all bits of **k**. Since 0 does not
change **xor** result, we can ignore all 0s.  
`f(1001010) = 1 ^ 1 ^ 1 = (1^1) ^ 1 = 0 ^ 1 = 1`  
`f(11110011) = 1 ^ 1^ 1 ^ 1 ^ 1 ^1 = (1 ^ 1) ^ (1 ^ 1) ^ (1 ^1) = 0`  
Now, it's easy to tell `f(k) = 0` if **k** has **even** number of 1s in binary
representation, and `f(k) = 1` when **k** has **odd** number of 1s


### Solution 3
 **First** , you can easily find that prefix of every row is exactly the same.  
It not difficult to understand.  
Beacause every `2 * L` fisrt letters are generated by the same `L` fist
letters.

 **Then** , we know that every row is the start part of the same long
sequence.  
Moreover K is guaranteed to be an integer in the `range[1, 2 ^ (N - 1)]`.  
So result depends only on value `K`

Suppose `2 ^ L < K <= 2 ^ (L + 1)`.  
`2 ^ (L - 1)` is the biggest `2`'s power smaller than `K`.  
`Kth` number is generated from `K - 2 ^ (L - 1)`  
`Kth` number is also different from `K - 2 ^ (L - 1)`  
So we toggle `K` to `K - 2 ^ L` by subtracting `2 ^ L`

We repeat the process until we toggle `K` to `1`, which mean we need to
substract `K - 1` in total.  
What we should do is transfer `K - 1` in binary so that we can easily find how
many times we need to toggle.

  

 **For example`K = 8`**  
we need to substract `K - 1 = 7 = 111` in binary.  
So we need to substract `100`, `10` and `1`, which are `4, 2, 1` in decimal.  
We toggle `K` from `8` to `4`, `2` and finally `1`.

 **For example`K = 9`**  
we need to substract `K - 1 = 8 = 1000` in binary.  
So we need to substract `1000`, which is `8` in decimal.  
We toggle `K` from `9` to `1` directly.

  

 **Conclusion**  
we turn this problem to just count `1bits`.  
We can observe that the answer depend on whether the number of `1 bits` in
binary `K-1` is odd or even.  
Now you may have many ways to solve this problem.  
In Python, I transfer K to binary.  
In Java, bitCount is ready for use.  
In C++, I use the trick `K & (K - 1)` to drops the lowest bit.

  

 **Here is another idea**  
<https://leetcode.com/problems/k-th-symbol-in-
grammar/discuss/121544/C++JavaPython-Another-Solution>

  

 **Java**

    
    
    	 public int kthGrammar(int N, int K) {
    		return Integer.bitCount(K - 1) & 1;
    	}
    

**C++**

    
    
         int kthGrammar(int N, int K) {
            int n;
            for (n = 0, K -= 1; K ; K &= (K - 1)) n++;
            return n & 1;
        }
    

**Python**

    
    
         def kthGrammar(self, N, K):
            return bin(K - 1).count('1') & 1
    



