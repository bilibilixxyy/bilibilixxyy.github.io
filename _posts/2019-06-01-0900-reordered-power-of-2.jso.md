---
layout: post
title: 900. Reordered Power of 2
---
### Question
Starting with a positive integer `N`, we reorder the digits in any order
(including the original order) such that the leading digit is not zero.

Return `true` if and only if we can do this in a way such that the resulting
number is a power of 2.



 **Example 1:**

    
    
     **Input:** 1
    **Output:** true
    

**Example 2:**

    
    
    **Input:** 10
    **Output:** false
    

**Example 3:**

    
    
    **Input:** 16
    **Output:** true
    

**Example 4:**

    
    
    **Input:** 24
    **Output:** false
    

**Example 5:**

    
    
    **Input:** 46
    **Output:** true
    



 **Note:**

  1. `1 <= N <= 10^9`

### Solution 1
`counter` will counter the number of digits 9876543210 in the given number.  
Then I just compare `counter(N)` with all `counter(power of 2)`.  
`1 <= N <= 10^9`, so up to 8 same digits.  
If `N > 10^9`, we can use a hash map.

 **C++:**

    
    
         bool reorderedPowerOf2(int N) {
            long c = counter(N);
            for (int i = 0; i < 32; i++)
                if (counter(1 << i) == c) return true;
            return false;
        }
    
        long counter(int N) {
            long res = 0;
            for (; N; N /= 10) res += pow(10, N % 10);
            return res;
        }
    

**Java:**

    
    
         public boolean reorderedPowerOf2(int N) {
            long c = counter(N);
            for (int i = 0; i < 32; i++)
                if (counter(1 << i) == c) return true;
            return false;
        }
        public long counter(int N) {
            long res = 0;
            for (; N > 0; N /= 10) res += (int)Math.pow(10, N % 10);
            return res;
        }
    

**Python:**

    
    
        def reordered PowerOf2(self, N):
            c = collections.Counter(str(N))
            return any(c == collections.Counter(str(1 << i)) for i in xrange(30))
    

**Python 1-line**  
suggested by @urashima9616, bests 80%

    
    
         def reorderedPowerOf2(self, N):
            return sorted(str(N)) in [sorted(str(1 << i)) for i in range(30)]
    


### Solution 2
The idea here is similar to that of group Anagrams problem (Leetcode #49).

First, we convert the input number (N) into a string and sort the string.
Next, we get the digits that form the power of 2 (by using 1 << i and vary i),
convert them into a string, and then sort them. As we convert the powers of 2
(and there are only 31 that are <= 10^9), for each power of 2, we compare if
the string is equal to that of string based on N. If the two strings are
equal, then we return true.

    
    
    class Solution {
        public boolean reorderedPowerOf2(int N) {
            char[] a1 = String.valueOf(N).toCharArray();
            Arrays.sort(a1);
            String s1 = new String(a1);
            
            for (int i = 0; i < 31; i++) {
                char[] a2 = String.valueOf((int)(1 << i)).toCharArray();
                Arrays.sort(a2);
                String s2 = new String(a2);
                if (s1.equals(s2)) return true;
            }
            
            return false;
        }
    }
    


### Solution 3
    
    
    class Solution {
        public boolean reorderedPowerOf2(int N) {
            char[] ca=(N+"").toCharArray();
            return helper(ca, 0, new boolean[ca.length]);
        }
        public boolean helper(char[] ca, int cur, boolean[] used){
            if (cur!=0 && (cur+"").length()==ca.length){
                if (Integer.bitCount(cur)==1) return true;
                return false;
            }
            for (int i=0; i<ca.length; i++){
                if (used[i]) continue;
                used[i]=true;
                if (helper(ca, cur*10+ca[i]-'0', used)) return true;
                used[i]=false;
            }
            return false;
        }
    }
    

It would be faster if you use memo to prune.

    
    
    class Solution {
        public boolean reorderedPowerOf2(int N) {
            char[] ca=(N+"").toCharArray();
            return helper(ca, 0, new boolean[ca.length], new HashSet<Integer>());
        }
        public boolean helper(char[] ca, int cur, boolean[] used, HashSet<Integer> vis){
            if (!vis.add(cur)) return false;
            if (cur!=0 && (cur+"").length()==ca.length){
                if (Integer.bitCount(cur)==1) return true;
                return false;
            }
            for (int i=0; i<ca.length; i++){
                if (used[i]) continue;
                used[i]=true;
                if (helper(ca, cur*10+ca[i]-'0', used, vis)) return true;
                used[i]=false;
            }
            return false;
        }
    }
    



