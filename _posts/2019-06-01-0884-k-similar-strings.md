---
layout: post
title: 884. K-Similar Strings
---
### Question
Strings `A` and `B` are `K`-similar (for some non-negative integer `K`) if we
can swap the positions of two letters in `A` exactly `K` times so that the
resulting string equals `B`.

Given two anagrams `A` and `B`, return the smallest `K` for which `A` and `B`
are `K`-similar.

 **Example 1:**

    
    
     **Input:** A = "ab", B = "ba"
    **Output:** 1
    

**Example 2:**

    
    
    **Input:** A = "abc", B = "bca"
    **Output:** 2
    

**Example 3:**

    
    
    **Input:** A = "abac", B = "baca"
    **Output:** 2
    

**Example 4:**

    
    
    **Input:** A = "aabc", B = "abca"
    **Output:** 2

 **Note:**

  1. `1 <= A.length == B.length <= 20`
  2. `A` and `B` contain only lowercase letters from the set `{'a', 'b', 'c', 'd', 'e', 'f'}`

### Solution 1
The trick is that you just need to swap the first (a,b)->(b,a) pair every
round, instead of other pairs like (c,d)->(d,c) (otherwise it would cause
TLE), and BFS would guarantee the shortest path.  
credit to @sijian2001

    
    
    class Solution {
        public int kSimilarity(String A, String B) {
            if (A.equals(B)) return 0;
            Set<String> vis= new HashSet<>();
            Queue<String> q= new LinkedList<>();
            q.add(A);
            vis.add(A);
            int res=0;
            while(!q.isEmpty()){
                res++;
                for (int sz=q.size(); sz>0; sz--){
                    String s= q.poll();
                    int i=0;
                    while (s.charAt(i)==B.charAt(i)) i++;
                    for (int j=i+1; j<s.length(); j++){
                        if (s.charAt(j)==B.charAt(j) || s.charAt(i)!=B.charAt(j) ) continue;
                        String temp= swap(s, i, j);
                        if (temp.equals(B)) return res;
                        if (vis.add(temp)) q.add(temp);
                    }
                }
            }
            return res;
        }
        public String swap(String s, int i, int j){
            char[] ca=s.toCharArray();
            char temp=ca[i];
            ca[i]=ca[j];
            ca[j]=temp;
            return new String(ca);
        }
    }
    

![image](https://s3-lc-
upload.s3.amazonaws.com/users/caraxin/image_1529267603.png)  
**update** sorry I just realized that my explaination has some flaw (the code
is correct though).  
I turned  
`if (s.charAt(j)==B.charAt(j) || s.charAt(i)!=B.charAt(j) ) continue;`  
into  
`if (s.charAt(j)==B.charAt(j) || s.charAt(i)!=B.charAt(j) ||
s.charAt(j)!=B.charAt(i) ) continue;`  
and it can't pass all test case, which means we are not actually swapping the
direct reversed pair, we are just trying to fix the some wrong character in
each loop.  
To make it better understood, I suggest we change this line to:  
`if (s.charAt(j)==B.charAt(j) || s.charAt(j)!=B.charAt(i) ) continue;`  
which means we only swap the i-th and j-th character when j-th character is
wrong and it happends to be the target character of i-th position.  
So that in each round we will repair the **first unordered character** and as
a result move forward at least 1 step.  
Sorry again for misleading so many people.


### Solution 2
Simple and straightforward. Swap the characters in `A` using backtracking to
get close to `B`.  
Use a `map` to memorize.

    
    
    class Solution {
        public int kSimilarity(String A, String B) {
            Map<String, Integer> map = new HashMap<>();
            return backtrack(A.toCharArray(), B, map, 0);
        }
        private int backtrack(char[] A, String B, Map<String, Integer> map, int i) {
            String sa = new String(A);
            if (sa.equals(B)) {
                return 0;
            }
            if (map.containsKey(sa)) {
                return map.get(sa);
            }
            int min = Integer.MAX_VALUE;
            while (i < A.length && A[i] == B.charAt(i)) {
                i++;
            }
            for (int j = i + 1; j < B.length(); j++) {
                if (A[j] == B.charAt(i)) {
                    swap(A, i, j);
                    int next = backtrack(A, B, map, i + 1);
                    if (next != Integer.MAX_VALUE) {
                        min = Math.min(min, next + 1);
                    }
                    swap(A, i, j);
                }
            }
            map.put(sa, min);
            return min;
        }
        private void swap(char[] cs, int i, int j) {
            char temp = cs[i];
            cs[i] = cs[j];
            cs[j] = temp;
        }
    }
    

**Update:**

Basically, this problem seems like a **cyclic swapping** problem. I get some
hints from [this post](https://leetcode.com/problems/couples-holding-
hands/discuss/113362/JavaC++-O\(N\)-solution-using-cyclic-swapping).  
The intuition behind this is, in order to get the minimum swaps, we need to
divide the string into **groups** as many as possible. Then for each group
with the size of `k`, we need `k - 1` swaps, for example:  
Consider `A = "abcde"`, `B = "bcaed"`, we can divide the `A` into `abc` and
`de`, the total swaps is `2 + 1 = 3`.  
Look into each group, it's a cyclic swapping, which means for **each** swap,
we put a char into the right place. In this situation, we can use the strategy
in my post to simply get the first right char at A[i].  
Basically, what the backtracking do is simulate all the possible swapping,
trying to find the minimum swaps.


### Solution 3
We employ a greedy recursive algorithm based on the following observations:

  1. Let n be the size of anagrams, at most n (actually n-1) swaps would make them identical.
  2. In the optimal strategy, each swap should contribute to at least 1 match of characters. We also note the best we can achieve in a single swap is 2 matches, which is guaranteed to be optimal (although it might not be the only one). Therefore, if A[i]!=B[i], we should try our luck and look for a position j, such that A[j]==B[i] && A[i]==B[j], if one is found, then we achieve a optimal double match and proceed to match the remaining. Otherwise, we look for a position j, such that A[j]==B[i], and MAKE SURE IT IS NOT A MATCH ALREADY (i.e., A[j]!=B[j], otherwise you simply push the mismatch in position i to position j and don't make any real progress). There might be multiple positions j satisfying such condition, and we need to go over each one of them, as certain j might give rise to a double match later on, and therefore outperform others.

    
    
    class Solution {
    public:
        int kSimilarity(string A, string B) {
          for (int i=0; i<A.size(); i++) {
            if (A[i]==B[i]) continue;
            vector<int> matches;
            for (int j=i+1;j<A.size();j++) { 
              if (A[j]==B[i] && A[j]!=B[j]) {
                matches.push_back(j);
                if (A[i]==B[j]) {
                  swap(A[i],A[j]);
                  return 1+kSimilarity(A.substr(i+1),B.substr(i+1));
                }
              }
            }
            int best=A.size()-1;
            for (int j: matches) {
                swap(A[i],A[j]);
                best = min(best, 1+kSimilarity(A.substr(i+1),B.substr(i+1)));
                swap(A[i],A[j]);
            }
            return best;
          }
          return 0;
        }
    };
    



