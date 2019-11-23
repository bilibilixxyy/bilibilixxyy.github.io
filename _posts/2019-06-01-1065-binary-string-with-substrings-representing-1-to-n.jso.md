---
layout: post
title: 1065. Binary String With Substrings Representing 1 To N
---
### Question
Given a binary string `S` (a string consisting only of '0' and '1's) and a
positive integer `N`, return true if and only if for every integer X from 1 to
N, the binary representation of X is a substring of S.



 **Example 1:**

    
    
     **Input:** S = "0110", N = 3
    **Output:** true
    

**Example 2:**

    
    
    **Input:** S = "0110", N = 4
    **Output:** false
    



 **Note:**

  1. `1 <= S.length <= 1000`
  2. `1 <= N <= 10^9`

### Solution 1
# Solution 1

##  **Intuition** :

The construction of `S` is a NP problem,  
it's time consuming to construct a short `S`.  
I notice `S.length <= 1000`, which is too small to make a big `N`.

This intuition lead me to the following 1-line python solution,  
which can be implemented very fast.

  

##  **Explanation** :

Check if `S` contains binary format of `N`,  
If so, continue to check `N - 1`.  
  

##  **Time Complexity**

Have a look at the number 1001 ~ 2000 and their values in binary.

1001 0b1111101001  
1002 0b1111101010  
1003 0b1111101011  
...  
1997 0b11111001101  
1998 0b11111001110  
1999 0b11111001111  
2000 0b11111010000

The number 1001 ~ 2000 have 1000 different continuous 10 digits.  
The string of length `S` has at most `S - 9` different continuous 10 digits.  
So `S <= 1000`, the achievable `N <= 2000`.  
So `S * 2` is a upper bound for achievable `N`.  
If `N > S * 2`, we can return `false` directly in `O(1)`

Note that it's the same to prove with the numbers 512 ~ 1511, or even smaller
range.

`N/2` times check, `O(S)` to check a number in string `S`.  
The overall time complexity has upper bound `O(S^2)`.

 **Java:**

    
    
        public boolean query String(String S, int N) {
            for (int i = N; i > N / 2; --i)
                if (!S.contains(Integer.toBinaryString(i)))
                    return false;
            return true;
        }
    

**C++**

    
    
         bool queryString(string S, int N) {
            for (int i = N; i > N / 2;  --i) {
                string b = bitset<32>(i).to_string();
                if (S.find(b.substr(b.find("1"))) == string::npos)
                    return false;
            }
            return true;
        }
    

**Python:**

    
    
         def queryString(self, S, N):
            return all(bin(i)[2:] in S for i in xrange(N, N / 2, -1))
    

  

# Solution2

Continue to improve the above solution to `O(S)`

**Python:**

    
    
        def queryString(self, S, N):
             if N > len(S) * 2: return False
            L = len(bin(N)) - 2
            seen = {S[i:i + L] for i in xrange(len(S))} | {S[i:i + L - 1] for i in xrange(len(S))}
            return all(bin(i)[2:] in seen for i in xrange(N, N / 2, -1))
    

`seen` can be calculated by two passes of rolling hash.


### Solution 2
# Intuition

We can process the entire string and track all numbers [1..N] that we can
build.

OR

We can generate binary string representation for `[1..N]`, and search for it
in the string.

## Solution 1

  * For each non-zero position `i` in S, gradually build `num` while `num <= N`.
  * Track each `num` in `seen`, increment `X` for new `num`.
  * Return true if we built all numbers from 1 to N (`X == N`).

    
    
    bool queryString(string S, int N, int X = 0) {
      vector<bool> seen(N);
      for (auto i = 0; i < S.size() && X < N; ++i) {
        if (S[i] == '0') continue;
        for (auto j = i, num = 0; num <= N && j < S.size(); ++j) {
          num = (num << 1) + S[j] - '0';
          if (num > 0 && num <= N && !seen[num - 1]) {
            ++X;
            seen[num - 1] = true;
          }
        }
      }
      return X == N;
    }
    

## Complexity Analysis

Runtime: _O(S log N)_ , where `S` is the string size. For every position, we
analyze `log N` digits.  
Memory: _O(N)_ as we need to track all numbers from 1 to `N`.

# Solution 2

Iterate from N to 1. Build binary string and search.

    
    
    bool queryString(string S, int N) {
      while (N > 0) {
        auto s = bitset<32>(N--).to_string();
        if (S.find(s.substr(s.find("1"))) == string::npos) return false;
      }
      return true;
    }
    

## Complexity Analysis

Runtime: _O(N * (S + log N))_ , where `S` is the string size. We search N
times, and every search is _O(S + log N)_.  
Memory: _O(1)_.


### Solution 3
<https://www.youtube.com/watch?v=6tmSHvlSUJk>

岁月静好



