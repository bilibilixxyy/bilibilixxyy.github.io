---
layout: post
title: 1160. Letter Tile Possibilities
---
### Question
You have a set of `tiles`, where each tile has one letter `tiles[i]` printed
on it.  Return the number of possible non-empty sequences of letters you can
make.



 **Example 1:**

    
    
     **Input:** "AAB"
    **Output:** 8
    **Explanation:** The possible sequences are "A", "B", "AA", "AB", "BA", "AAB", "ABA", "BAA".
    

**Example 2:**

    
    
    **Input:** "AAABBC"
    **Output:** 188
    



 **Note:**

  1. `1 <= tiles.length <= 7`
  2. `tiles` consists of uppercase English letters.

### Solution 1
    
    
         public int numTilePossibilities(String tiles) {
            int[] count = new int[26];
            for (char c : tiles.toCharArray()) count[c - 'A']++;
            return dfs(count);
        }
        
        int dfs(int[] arr) {
            int sum = 0;
            for (int i = 0; i < 26; i++) {
                if (arr[i] == 0) continue;
                sum++;
                arr[i]--;
                sum += dfs(arr);
                arr[i]++;
            }
            return sum;
        }
    

**Thoughts**

input: AAB  
count: A -> 2, B -> 1

For sequence of length 1:

  * We can pick either A, or B.
  * So we have "A", "B".

For sequence of length 2:

  * We build it based on "sequence of length 1"
  * For "A":
    * count: A -> 1, B -> 1
    * We can still pick either A, or B
    * So we have "AA", "AB"
  * For "B":
    * count: A -> 2, B -> 0
    * We can only pick A
    * So we have "BA"

For sequence of length 3: blablabla

 **Implementation**

  1. We don't need to keep track of the sequence. We only need count
  2. If we implement the above idea by each level (Count all sequence of length 1, then count all sequence of length 2, etc), we have to remember previous sequence.
  3. So we use recursion instead. Just remember to add the count back (`arr[i]++`).


### Solution 2
# Intuition

If we have a string of size `n` with `i` unique characters, and each character
repeats `m[i]` times, the number of unique permutations is:

    
    
    n! / (m[1]! * m[2]! * .. * m[i]!)
    

So, we need to find all combinations of letters for sizes `1...n`, and
aggregate permutations of each combination.

# Solution

For a combination, we do not care about the order of the characters. To
generate all combinations, we can sort the input string and run DFS. Note that
we need to use a hash set to track duplicates.

For each unique combination of letters, add the number of unique permutations
(`uniquePerms`).

Interesting fact, I initially used `tgamma` to get a factorial; it worked
correctly on my machine but failed in OJ. So just added a pre-computed `fact`.

    
    
    int fact[8] = { 1, 1, 2, 6, 24, 120, 720, 5040 };
    unordered_set<string> st;
    int uniquePerms(string& s) {
      int cnt[26] = {};
      for (auto ch : s) ++cnt[ch - 'A'];
      auto res = fact[s.size()];
      for (auto n : cnt) res /= fact[n];
      return res;
    }
    int dfs(string& s, string seq = "", int pos = 0) {
      if (pos >= s.size()) {
        return st.insert(seq).second ? uniquePerms(seq) : 0;
      }
      return dfs(s, seq, pos + 1) + dfs(s, seq + s[pos], pos + 1);
    }
    int numTilePossibilities(string tiles) {
      sort(begin(tiles), end(tiles));
      return dfs(tiles) - 1;
    }
    

# Complexity Analysis

**Runtime:** for a string of size `m`, we will generate `n! / (m! * (n - m)!)`
combinations, where `n` is the size of the input string. The complexity to
calculate the number of permutations is O(m).

Since `m` is in range `[1..n]`, the total complexity is:

    
    
    n
    Σ n! / ((m - 1)! * (n - m)!)
    m = 1
    

**Memory:** to track unique combinations, we need:

    
    
    n
    Σ n! / (m! * (n - m)!)
    m =  1
    


### Solution 3
This is a mathematical problem.  
Get the frequencies of different characters [f_0, f_1, ..., f_n].  
For each possible choice of frequency [i_0, i_1, ..., i_n] (0 <= i_k <= f_k, k
= 0, 1, ..., n),  
the number of distinct sequences is (i_0 + i_1 + ... + i_n)! / ( i_0! * i_1! *
... * i_n!).

    
    
    import collections, math
    class Solution:
        def numTilePossibilities(self, tiles: str) -> int:
            freq = collections.Counter(tiles)
            prod = 1
            for f in freq.values():
                prod *= f + 1
            res = 0
            for i in range(1, prod):
                digits = []
                for f in freq.values():
                    digits.append(i % (f + 1))
                    i = i // (f + 1)
                tmp = math.factorial(sum(digits))
                for d in digits:
                    tmp //= math.factorial(d)
                res += tmp
            return res
    



