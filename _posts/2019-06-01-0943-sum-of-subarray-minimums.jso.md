---
layout: post
title: 943. Sum of Subarray Minimums
---
### Question
Given an array of integers `A`, find the sum of `min(B)`, where `B` ranges
over every (contiguous) subarray of `A`.

Since the answer may be large, **return the answer modulo`10^9 + 7`.**



 **Example 1:**

    
    
     **Input:** [3,1,2,4]
    **Output:** 17
    **Explanation:** Subarrays are [3], [1], [2], [4], [3,1], [1,2], [2,4], [3,1,2], [1,2,4], [3,1,2,4]. 
    Minimums are 3, 1, 2, 4, 1, 1, 2, 1, 1, 1.   Sum is 17.



 **Note:**

  1. `1 <= A.length <= 30000`
  2. `1 <= A[i] <= 30000`

### Solution 1
Before diving into the solution, we first introduce a very important stack
type, which is called **monotone stack** .

####  **What is monotonous increase stack?**

Roughly speaking, the elements in the an monotonous increase stack keeps an
increasing order.

####  **The typical paradigm for monotonous increase stack** :

    
    
    for(int i = 0; i < A.size(); i++){
      while(!in_stk.empty() && in_stk.top() > A[i]){
        in_stk.pop();
      }
      in_stk.push(A[i]);
    }
    

#### **What can monotonous increase stack do?**

##### (1) find the **previous less** element of each element in a vector
**with O(n) time** :

  * What is the previous less element of an element?  
For example:  
[3, 7, 8, 4]  
The previous less element of 7 is 3.  
The previous less element of 8 is 7.  
**The previous less element of 4 is 3**.  
There is no previous less element for 3.

For simplicity of notation, we use abbreviation **PLE** to denote **P**
revious **L** ess **E** lement.

  * C++ code (by slitghly modifying the paradigm):  
Instead of directly pushing the element itself, here for simplicity, we push
the **index**.  
We do some record when the index is pushed into the stack.

    
    
     // previous_less[i] = j means A[j] is the previous less element of A[i].
    // previous_less[i] = -1 means there is no previous less element of A[i].
    vector<int> previous_less(A.size(), -1);
    for(int i = 0; i < A.size(); i++){
      while(!in_stk.empty() && A[in_stk.top()] > A[i]){
        in_stk.pop();
      }
      previous_less[i] = in_stk.empty()? -1: in_stk.top();
      in_stk.push(i);
    }
    

##### (2) find the **next less** element of each element in a vector with
**O(n) time** :

  * What is the next less element of an element?  
For example:  
[3, 7, 8, 4]  
The next less element of 8 is 4.  
**The next less element of 7 is 4**.  
There is no next less element for 3 and 4.

For simplicity of notation, we use abbreviation **NLE** to denote **N** ext
**L** ess **E** lement.

  * C++ code (by slighly modifying the paradigm):  
We do some record when the index is poped out from the stack.

    
    
    // next_less[i] = j means A[j] is the next less element of A[i].
    // next_less[i] = -1 means there is no next less element of A[i].
    vector<int> previous_less(A.size(), -1);
    for(int i = 0; i < A.size(); i++){
      while(!in_stk.empty() && A[in_stk.top()] > A[i]){
        auto x = in_stk.top(); in_stk.pop();
        next_less[x] = i;
      }
      in_stk.push(i);
    }
    

* * *

#### **How can the monotonous increase stack be applied to this problem?**

For example:  
Consider the element `3` in the following vector:

    
    
                                [2, 9, 7, 8, 3, 4, 6, 1]
    			     |                    |
    	             the previous less       the next less 
    	                element of 3          element of 3
    
    

After finding both **NLE** and **PLE** of `3`, we can determine the  
distance between `3` and `2`(previous less) , and the distance between `3` and
`1`(next less).  
In this example, the distance is `4` and `3` respectively.

**How many subarrays with 3 being its minimum value?**  
The answer is `4*3`.

    
    
     9 7 8 3 
    9 7 8 3 4 
    9 7 8 3 4 6 
    7 8 3 
    7 8 3 4 
    7 8 3 4 6 
    8 3 
    8 3 4 
    8 3 4 6 
    3 
    3 4 
    3 4 6
    

**How much the element 3 contributes to the final answer?**  
It is `3*(4*3)`.  
**What is the final answer?**  
Denote by `left[i]` the distance between element `A[i]` and its **PLE**.  
Denote by `right[i]` the distance between element `A[i]` and its **NLE**.

The final answer is,  
`sum(A[i]*left[i]*right[i] )`

#### **The solution (One pass)**

    
    
     class Solution {
    public:
      int sumSubarrayMins(vector<int>& A) {
        stack<pair<int, int>> in_stk_p, in_stk_n;
        // left is for the distance to previous less element
        // right is for the distance to next less element
        vector<int> left(A.size()), right(A.size());
    		
        //initialize
        for(int i = 0; i < A.size(); i++) left[i] =  i + 1;
        for(int i = 0; i < A.size(); i++) right[i] = A.size() - i;
    		
        for(int i = 0; i < A.size(); i++){
          // for previous less
          while(!in_stk_p.empty() && in_stk_p.top().first > A[i]) in_stk_p.pop();
          left[i] = in_stk_p.empty()? i + 1: i - in_stk_p.top().second;
          in_stk_p.push({A[i],i});
    			
          // for next less
          while(!in_stk_n.empty() && in_stk_n.top().first > A[i]){
            auto x = in_stk_n.top();in_stk_n.pop();
            right[x.second] = i - x.second;
          }
          in_stk_n.push({A[i], i});
        }
    
        int ans = 0, mod = 1e9 +7;
        for(int i = 0; i < A.size(); i++){
          ans = (ans + A[i]*left[i]*right[i])%mod;
        }
        return ans;
      }
    };
    
    

#### **The last thing that needs to be mentioned for handling duplicate
elements** :

 **Method** : Set **strict less** and **non-strict less** (less than **or
equal to** ) for finding **NLE** and **PLE** respectively. The order doesn't
matter.

For example, the above code for finding **NLE** is **strict less** , while
**PLE** is actually **non-strict less**.  
 **Remark** : Although in both loop conditions the signs are set as `>`, for
NLE, we make records **inside** the loop, while for PLE, records are done
**outside** the loop.

* * *

####  **More** :

  * What can monotonous **decrease** stack do?
  * Some applications of monotone (increase/decrease) stack in leetcode:  
[Next Greater Element II](https://leetcode.com/problems/Next-Greater-Element-
II/description/) (a very basic one)  
[Largest Rectangle in Histogram](https://leetcode.com/problems/Largest-
Rectangle-in-Histogram/description/)(almost the same as this problem)  
[Maximal Rectangle](https://leetcode.com/problems/Maximal-
Rectangle/description/)(please do this problem after you solve the above one)  
[Trapping Rain Water](https://leetcode.com/problems/Trapping-Rain-
Water/description/) (challenge)  
[Remove Duplicate Letters](https://leetcode.com/problems/remove-duplicate-
letters/description/)(challenge)  
[Remove K Digits](https://leetcode.com/problems/remove-k-digits/description/)  
[Create Maximum Number](https://leetcode.com/problems/create-maximum-
number/description/)  
[132
Pattern](https://leetcode.com/problems/132-pattern/description/)(challenge,
instead of focusing on the elements in the stack, this problem focuses on the
elements poped from the monotone stack)  
[sliding window maximum](https://leetcode.com/problems/sliding-window-
maximum/description/)(challenge, monotone **queue** )  
[Max Chunks To Make Sorted II](https://leetcode.com/problems/Max-Chunks-To-
Make-Sorted-II/description/)

Hope this helps.


### Solution 2
##  **Intuition** :

I guess this is a general intuition for most solution.  
`res = sum(A[i] * f(i))`  
where `f(i)` is the number of subarrays,  
in which `A[i]` is the minimum.

To get `f(i)`, we need to find out:  
`left[i]`, the length of strict bigger numbers on the left of `A[i]`,  
`right[i]`, the length of bigger numbers on the right of `A[i]`.

 **Then,**  
`left[i] + 1` equals to  
the number of subarray ending with `A[i]`,  
and `A[i]` is single minimum.

`right[i] + 1` equals to  
the number of subarray starting with `A[i]`,  
and `A[i]` is the first minimum.

Finally `f(i) = (left[i] + 1) * (right[i] + 1)`

 **For`[3,1,2,4]` as example:**  
`left + 1 = [1,2,1,1]`  
`right + 1 = [1,3,2,1]`  
`f = [1,6,2,1]`  
`res = 3 * 1 + 1 * 6 + 2 * 2 + 4 * 1 = 17`

##  **Explanation** :

To calculate `left[i]` and `right[i]`,  
we use two **increasing stacks**.

It will be easy if you can refer to this problem and my post:  
[901\. Online Stock Span](https://leetcode.com/problems/online-stock-
span/discuss/168311/)  
I copy some of my codes from this solution.

##  **More** :

For some more similar problem, I suggest  
[828\. Unique Letter String](https://leetcode.com/problems/unique-letter-
string/discuss/128952/)  
[891\. Sum of Subsequence Widths](https://leetcode.com/problems/sum-of-
subsequence-widths/discuss/161267/)

##  **Complexity** :

All elements will be pushed twice and popped at most twice  
`O(N)` time, `O(N)` space

 **C++:**

    
    
        int sumSubarrayMins(vector<int> A) {
            int res =  0, n = A.size(), mod = 1e9 + 7;
            vector<int> left(n), right(n);
            stack<pair<int, int>> s1,s2;
            for (int i = 0; i < n; ++i) {
                int count = 1;
                while (!s1.empty() && s1.top().first > A[i]) {
                    count += s1.top().second;
                    s1.pop();
                }
                s1.push({A[i], count});
                left[i] = count;
            }
            for (int i = n - 1; i >= 0; --i) {
                int count = 1;
                while (!s2.empty() && s2.top().first >= A[i]) {
                    count += s2.top().second;
                    s2.pop();
                }
                s2.push({A[i], count});
                right[i] = count;
            }
            for (int i = 0; i < n; ++i)
                res = (res + A[i] * left[i] * right[i]) % mod;
            return res;
        }
    

**Java:**

    
    
         public int sumSubarrayMins(int[] A) {
            int res = 0, n = A.length, mod = (int)1e9 + 7;
            int[] left = new int[n], right = new int[n];
            Stack<int[]> s1 = new Stack<>(), s2 = new Stack<>();
            for (int i = 0; i < n; ++i) {
                int count = 1;
                while (!s1.isEmpty() && s1.peek()[0] > A[i])
                    count += s1.pop()[1];
                s1.push(new int[] {A[i], count});
                left[i] = count;
            }
            for (int i = n - 1; i >= 0; --i) {
                int count = 1;
                while (!s2.isEmpty() && s2.peek()[0] >= A[i])
                    count += s2.pop()[1];
                s2.push(new int[] {A[i], count});
                right[i] = count;
            }
            for (int i = 0; i < n; ++i)
                res = (res + A[i] * left[i] * right[i]) % mod;
            return res;
        }
    

**Python:**

    
    
        def sumSubarrayMins(self, A):
            n,  mod = len(A), 10**9 + 7
            left, right, s1, s2 = [0] * n, [0] * n, [], []
            for i in range(n):
                count = 1
                while s1 and s1[-1][0] > A[i]: count += s1.pop()[1]
                left[i] = count
                s1.append([A[i], count])
            for i in range(n)[::-1]:
                count = 1
                while s2 and s2[-1][0] >= A[i]: count += s2.pop()[1]
                right[i] = count
                s2.append([A[i], count])
            return sum(a * l * r for a, l, r in zip(A, left, right)) % mod
    

## **Improvement**

1.Here I record `(A[i], count)` in the stack.  
We can also only record index.  
2\. For left part and right part, the logic is same.  
So for each, I used one stack and one pass.  
This process can be optimized to one pass using one stack in total.

**C++:**

    
    
        int sumSubarrayMins(vector<int> A) {
            int res =  0, n = A.size(), mod = 1e9 + 7, j, k;
            stack<int> s;
            for (int i = 0; i <= n; ++i) {
                while (!s.empty() && A[s.top()] > (i == n ? 0 : A[i])) {
                    j = s.top(), s.pop();
                    k = s.empty() ? -1 : s.top();
                    res = (res + A[j] * (i - j) * (j - k)) % mod;
                }
                s.push(i);
            }
            return res;
        }
    

**Java:**

    
    
        public int sumSubarrayMins(int[] A) {
            Stack<Integer> s = new Stack<>();
            int n = A. length, res = 0, mod = (int)1e9 + 7, j,k;
            for (int i = 0; i <= n; i++) {
                while (!s.isEmpty() && A[stack.peek()] > (i == n ? 0 : A[i])) {
                    j = stack.pop();
                    k = stack.isEmpty() ? -1 : stack.peek();
                    res = (res + A[j] * (i - j) * (j - k)) % mod;
                }
                stack.push(i);
            }
            return res;
        }
    

**Python:**

    
    
        def sumSubarrayMins(self, A):
            res =  0
            s = []
            A = [0] + A + [0]
            for i, x in enumerate(A):
                while s and A[s[-1]] > x:
                    j = s.pop()
                    k = s[-1]
                    res += A[j] * (i - j) * (j - k)
                s.append(i)
            return res % (10**9 + 7)
    


### Solution 3
I use a monotonous non-decreasing stack to store the left boundary and right
boundary where a number is the minimal number in the sub-array

e.g. given [3,1,2,4],  
For 3, the boudary is: | 3 | ...  
For 1, the boudray is: | 3 1 2 4 |  
For 2, the boudray is: ... | 2 4 |  
For 4, the boudary is: ... | 4 |

The times a number _`n`_ occurs in the minimums is `|left_bounday-indexof(n)|
* |right_bounday-indexof(n)|`

The total sum is `sum([n * |left_bounday - indexof(n)| * |right_bounday -
indexof(n)| for n in array])`

After a number _`n`_ pops out from an increasing stack, the current stack top
is _`n`_ 's left_boundary, the number forcing _`n`_ to pop is _`n`_ 's
right_boundary.

A tricky here is to add MIN_VALUE at the head and end.

    
    
    class Solution:
        def sumSubarrayMins(self, A):
            res = 0
            stack = []  #  non-decreasing 
            A = [float('-inf')] + A + [float('-inf')]
            for i, n in enumerate(A):
                while stack and A[stack[-1]] > n:
                    cur = stack.pop()
                    res += A[cur] * (i - cur) * (cur - stack[-1]) 
                stack.append(i)
            return res % (10**9 + 7)
    



