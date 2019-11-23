---
layout: post
title: 60. Permutation Sequence
---
### Question
The set `[1,2,3,..., _n_ ]` contains a total of _n_! unique permutations.

By listing and labeling all of the permutations in order, we get the following
sequence for _n_ = 3:

  1. `"123"`
  2. `"132"`
  3. `"213"`
  4. `"231"`
  5. `"312"`
  6. `"321"`

Given _n_ and _k_ , return the _k_ th permutation sequence.

 **Note:**

  * Given _n_ will be between 1 and 9 inclusive.
  * Given  _k_  will be between 1 and _n_! inclusive.

 **Example 1:**

    
    
     **Input:** n = 3, k = 3
    **Output:** "213"
    

**Example 2:**

    
    
    **Input:** n = 4, k = 9
    **Output:** "2314"
    

### Solution 1
I'm sure somewhere can be simplified so it'd be nice if anyone can let me
know. The pattern was that:

say n = 4, you have {1, 2, 3, 4}

If you were to list out all the permutations you have

1 + (permutations of 2, 3, 4)  
  
2 + (permutations of 1, 3, 4)  
  
3 + (permutations of 1, 2, 4)  
  
4 + (permutations of 1, 2, 3)

  
We know how to calculate the number of permutations of n numbers... n! So each
of those with permutations of 3 numbers means there are 6 possible
permutations. Meaning there would be a total of 24 permutations in this
particular one. So if you were to look for the (k = 14) 14th permutation, it
would be in the

3 + (permutations of 1, 2, 4) subset.

To programmatically get that, you take k = 13 (subtract 1 because of things
always starting at 0) and divide that by the 6 we got from the factorial,
which would give you the index of the number you want. In the array {1, 2, 3,
4}, k/(n-1)! = 13/(4-1)! = 13/3! = 13/6 = 2. The array {1, 2, 3, 4} has a
value of 3 at index 2. So the first number is a 3.

Then the problem repeats with less numbers.

The permutations of {1, 2, 4} would be:

1 + (permutations of 2, 4)  
  
2 + (permutations of 1, 4)  
  
4 + (permutations of 1, 2)

But our k is no longer the 14th, because in the previous step, we've already
eliminated the 12 4-number permutations starting with 1 and 2. So you subtract
12 from k.. which gives you 1. Programmatically that would be...

k = k - (index from previous) * (n-1)! = k - 2*(n-1)! = 13 - 2*(3)! = 1

In this second step, permutations of 2 numbers has only 2 possibilities,
meaning each of the three permutations listed above a has two possibilities,
giving a total of 6. We're looking for the first one, so that would be in the
1 + (permutations of 2, 4) subset.

Meaning: index to get number from is k / (n - 2)! = 1 / (4-2)! = 1 / 2! = 0..
from {1, 2, 4}, index 0 is 1

  
so the numbers we have so far is 3, 1... and then repeating without
explanations.

  
{2, 4}  
  
k = k - (index from pervious) * (n-2)! = k - 0 * (n - 2)! = 1 - 0 = 1;  
  
third number's index = k / (n - 3)! = 1 / (4-3)! = 1/ 1! = 1... from {2, 4},
index 1 has 4  
  
Third number is 4

  
{2}  
  
k = k - (index from pervious) * (n - 3)! = k - 1 * (4 - 3)! = 1 - 1 = 0;  
  
third number's index = k / (n - 4)! = 0 / (4-4)! = 0/ 1 = 0... from {2}, index
0 has 2  
  
Fourth number is 2

  
Giving us 3142. If you manually list out the permutations using DFS method, it
would be 3142. Done! It really was all about pattern finding.

    
    
    public class Solution {
    public String getPermutation(int n, int k) {
        int pos = 0;
        List<Integer> numbers = new ArrayList<>();
        int[] factorial = new int[n+1];
        StringBuilder sb = new StringBuilder();
        
        // create an array of factorial lookup
        int sum = 1;
        factorial[0] = 1;
        for(int i=1; i<=n; i++){
            sum *= i;
            factorial[i] = sum;
        }
        // factorial[] = {1, 1, 2, 6, 24, ... n!}
        
        // create a list of numbers to get indices
        for(int i=1; i<=n; i++){
            numbers.add(i);
        }
        // numbers = {1, 2, 3, 4}
        
        k--;
        
        for(int i = 1; i <= n; i++){
            int index = k/factorial[n-i];
            sb.append(String.valueOf(numbers.get(index)));
            numbers.remove(index);
            k-=index*factorial[n-i];
        }
        
        return String.valueOf(sb);
    }
    

}


### Solution 2
Recursion will use more memory, while this problem can be solved by iteration.
I solved this problem before, but I didn't realize that using k = k-1 would
avoid dealing with case k%(n-1)!==0. Rewrote this code, should be pretty
concise now.

Only thing is that I have to use a list to store the remaining numbers,
neither linkedlist nor arraylist are very efficient, anyone has a better idea?

The logic is as follows: for n numbers the permutations can be divided to
(n-1)! groups, for n-1 numbers can be divided to (n-2)! groups, and so on.
Thus k/(n-1)! indicates the index of current number, and k%(n-1)! denotes
remaining index for the remaining n-1 numbers.  
We keep doing this until n reaches 0, then we get n numbers permutations that
is kth.

    
    
    public String getPermutation(int n, int k) {
            List<Integer> num = new LinkedList<Integer>();
            for (int i = 1; i <= n; i++) num.add(i);
            int[] fact = new int[n];  // factorial
            fact[0] = 1;
            for (int i = 1; i < n; i++) fact[i] = i*fact[i-1];
            k = k-1;
            StringBuilder sb = new StringBuilder();
            for (int i = n; i > 0; i--){
                int ind = k/fact[i-1];
                k = k%fact[i-1];
                sb.append(num.get(ind));
                num.remove(ind);
            }
            return sb.toString();
        }


### Solution 3
The idea is as follow:

For permutations of n, the first (n-1)! permutations start with 1, next (n-1)!
ones start with 2, ... and so on. And in each group of (n-1)! permutations,
the first (n-2)! permutations start with the smallest remaining number, ...

take n = 3 as an example, the first 2 (that is, (3-1)! ) permutations start
with 1, next 2 start with 2 and last 2 start with 3. For the first 2
permutations (123 and 132), the 1st one (1!) starts with 2, which is the
smallest remaining number (2 and 3). So we can use a loop to check the region
that the sequence number falls in and get the starting digit. Then we adjust
the sequence number and continue.

    
    
    import math
    class Solution:
        # @param {integer} n
        # @param {integer} k
        # @return {string}
        def getPermutation(self, n, k):
            numbers = range(1, n+1)
            permutation = ''
            k -= 1
            while n > 0:
                n -= 1
                # get the index of current digit
                index, k = divmod(k, math.factorial(n))
                permutation += str(numbers[index])
                # remove handled number
                numbers.remove(numbers[index])
    
            return permutation



