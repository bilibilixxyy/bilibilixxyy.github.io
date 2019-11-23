---
layout: post
title: 934. Bitwise ORs of Subarrays
---
### Question
We have an array `A` of non-negative integers.

For every (contiguous) subarray `B = [A[i], A[i+1], ..., A[j]]` (with `i <=
j`), we take the bitwise OR of all the elements in `B`, obtaining a result
`A[i] | A[i+1] | ... | A[j]`.

Return the number of possible results.  (Results that occur more than once are
only counted once in the final answer.)



 **Example 1:**

    
    
     **Input:** [0]
    **Output:** 1
    **Explanation:**
    There is only one possible result: 0.
    

**Example 2:**

    
    
    **Input:** [1,1,2]
    **Output:** 3
    **Explanation:**
    The possible subarrays are [1], [1], [2], [1, 1], [1, 2], [1, 1, 2].
    These yield the results 1, 1, 2, 1, 3, 3.
    There are 3 unique values, so the answer is 3.
    

**Example 3:**

    
    
    **Input:** [1,2,4]
    **Output:** 6
    **Explanation:**
    The possible results are 1, 2, 3, 4, 6, and 7.
    



 **Note:**

  1. `1 <= A.length <= 50000`
  2. `0 <= A[i] <= 10^9`

### Solution 1
 **Intuition** :  
Assume `B[i][j] = A[i] | A[i+1] | ... | A[j]`  
Hash set `cur` stores all wise `B[0][i]`, `B[1][i]`, `B[2][i]`, `B[i][i]`.

When we handle the `A[i+1]`, we want to update `cur`  
So we need operate bitwise OR on all elements in `cur`.  
Also we need to add `A[i+1]` to `cur`.

In each turn, we add all elements in `cur` to `res`.

 **Time Complexity** :  
`O(30N)`

Normally this part is easy.  
But for this problem, time complexity matters a lot.

The solution is straight forward,  
while you may worry about the time complexity up to `O(N^2)`  
However, it's not the fact.  
This solution has only `O(30N)`

The reason is that, `B[0][i] >= B[1][i] >= ... >= B[i][i]`.  
`B[0][i] covers all bits of B[1][i]`  
`B[1][i] covers all bits of B[2][i]`  
....

There are at most 30 bits for a positive number `0 <= A[i] <= 10^9`.  
So there are at most 30 different values for `B[0][i]`, `B[1][i]`, `B[2][i]`,
..., `B[i][i]`.  
Finally `cur.size() <= 30` and `res.size() <= 30 * A.length()`

In a worst case, `A = {1,2,4,8,16,..., 2 ^ 29}`  
And all `B[i][j]` are different and `res.size() == 30 * A.length()`

 **C++:**

    
    
         int subarrayBitwiseORs(vector<int> A) {
            unordered_set<int> res, cur, cur2;
            for (int i: A) {
                cur2 = {i};
                for (int j: cur) cur2.insert(i|j);
                cur = cur2;
                for (int j: cur) res.insert(j);
            }
            return res.size();
        }
    

**Java:**

    
    
         public int subarrayBitwiseORs(int[] A) {
            Set<Integer> res = new HashSet<>(), cur = new HashSet<>(), cur2;
            for (Integer i: A) {
                cur2 = new HashSet<>();
                cur2.add(i);
                for (Integer j: cur) cur2.add(i|j);
                res.addAll(cur = cur2);
            }
            return res.size();
        }
    

**Python:**

    
    
        def subarrayBitwiseORs(self, A):
            res, cur =  set(), set()
            for i in A:
                cur = {i | j for j in cur} | {i}
                res |= cur
            return len(res)
    


### Solution 2
For example, an array is [001, 011, 100, 110, 101] (in binary).

All the subarrays are:

    
    
    [001]
    [001 011] [011]
    [001 011 100] [011 100] [100]
    [001 011 100 110] [011 100 110] [100 110] [110]
    [001 011 100 110 101] [011 100 110 101] [100 110 101] [110 101] [101]
    

In each line, we add a new number to each array of the previous line.

Calculate the OR operations for each subarray:

    
    
    001
    011 011
    111 111 100
    111 111 110 110
    111 111 111 111 101
    

There are O(N^2) numbers in total, which is not acceptable.

After removing duplicate numbers in each line, it becomes:

    
    
    001
    011
    111 100
    111 110
    111 101
    

In each line `t`, for any two numbers `t[i]` and `t[j]` (i < j), `t[i]` must
have more `1`s than `t[j]`. So the length of each line will not exceed 32. So
if we remove duplicate numbers, the complexity would not be very large.

The complexity is O(kN), where k is a constant depends on the bitwise length
of input numbers. (32 in this problem)

Here is my code:

    
    
    class Solution {
    public:
        int subarrayBitwiseORs(vector<int>& A) {
            unordered_set<int> s;
            set<int> t;
            for (int i : A) {
                set<int> r;
                r.insert(i);
                for (int j : t) r.insert(i | j);
                t = r;
                for (int j : t) s.insert(j);
            }
            return s.size();
        }
    };
    


### Solution 3
There are **O(n²)** possible sets. A brute force algorithm that checks each of
these sets would be **O(n³)** , or **O(n²)** with a few optimisations.
However, this isn't fast enough. The question is then... how can we do less
doubling up of work?

One way is to go through the array once, attempting to OR the current number
with all subsets that included the number before it. The number of subsets in
the previous number isn't **n** , it's actually never going to be bigger than
the number of bits in the largest numbers of the array. This is because each
step we go back from the previous number could add at least one more bit in,
or none at all. It's impossible to subtract them.

For example, let's say we have the following (5 bit) numbers:  
`[4, 5, 10, 2, 16, 4, 5, 2, 1, 3]`  
In binary, these are:  
`[00100, 00101, 01010, 00010, 10000, 00100, 00101, 00010, 00001, 00011].`

The last number in the list is `00011`. Let's look at what happens when we
make increasingly larger sublists with the numbers before it and get the
result of OR'ing all those numbers.

    
    
    [00011] ==> 00011
    [00001, 00011] ==>  00011
    [00010, 00001, 00011]  ==>  00011
    [00101, 00010, 00001, 00011] ==>  00111
    [00100, 00101, 00010, 00001, 00011] ==>  00111
    [10000, 00100, 00101, 00010, 00001, 00011] ==> 10111
    [00010, 10000, 00100, 00101, 00010, 00001, 00011] ==> 10111
    [01010, 00010, 10000, 00100, 00101, 00010, 00001, 00011] ==> 11111
    [00101, 01010, 00010, 10000, 00100, 00101, 00010, 00001, 00011] ==> 11111
    [00100, 00101, 01010, 00010, 10000, 00100, 00101, 00010, 00001, 00011] ==> 11111
    

Notice how a lot of these are the same? Once a bit appears in a particular
position, it's impossible for it to disappear! What this means, is that we
only need to OR the last number with previous ones that are actually unique.
This is proportional to the number of bits in the largest number rather than
n. That is, it's a lot smaller!

So, for each number in the array, we start with a set containing just that
number (because that's what we'd get OR'ing it with itself). We then add all
the possibilities we can get by OR'ing the number with results that include
the previous number.

And here's an algorithm that uses that idea.

    
    
    class Solution:
        def subarrayBitwiseORs(self, A):
            # Tabulation is a list of sets, one for each number in A. 
            # Each set, at position i, is initialised to containing the element at A[i]
            tabulation = [set([A[i]]) for i in range(len(A))]
            
            # And now we need to go through, updating the sets based on the previous set.
            for i in range(1, len(A)):
                for previous_result in tabulation[i - 1]: 
                    tabulation[i].add(A[i] | previous_result)  
            
            # Return the number of unique numbers in the tabulation list.
            return len(set.union(*tabulation)) if len(A) > 0 else 0
    



