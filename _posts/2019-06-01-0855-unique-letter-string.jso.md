---
layout: post
title: 855. Unique Letter String
---
### Question
A character is unique in string `S` if it occurs exactly once in it.

For example, in string `S = "LETTER"`, the only unique characters are `"L"`
and `"R"`.

Let's define `UNIQ(S)` as the number of unique characters in string `S`.

For example, `UNIQ("LETTER") =  2`.

Given a string `S` with only uppercases, calculate the sum of
`UNIQ(substring)` over all non-empty substrings of `S`.

If there are two or more equal substrings at different positions in `S`, we
consider them different.

Since the answer can be very large, return the answer modulo `10 ^ 9 + 7`.



 **Example 1:**

    
    
     **Input:** "ABC"
    **Output:** 10
    **Explanation:** All possible substrings are: "A","B","C","AB","BC" and "ABC".
    Evey substring is composed with only unique letters.
    Sum of lengths of all substring is 1 + 1 + 1 + 2 + 2 + 3 = 10

**Example 2:**

    
    
    **Input:** "ABA"
    **Output:** 8
    **Explanation:** The same as example 1, except uni("ABA") = 1.
    



 **Note:** `0 <= S.length <= 10000`.

### Solution 1
 **Intuition:**

Let's think about how a character can be found as a unique character.

Think about string `"XAXAXXAX"` and focus on making the second `"A"` a unique
character.  
We can take `"XA(XAXX)AX"` and between `"()"` is our substring.  
We can see here, to make the second "A" counted as a uniq character, we need
to:

  1. insert `"("` somewhere between the first and second `A`
  2. insert `")"` somewhere between the second and third `A`

For step 1 we have `"A(XA"` and `"AX(A"`, 2 possibility.  
For step 2 we have `"A)XXA"`, `"AX)XA"` and `"AXX)A"`, 3 possibilities.

So there are in total `2 * 3 = 6` ways to make the second `A` a unique
character in a substring.  
In other words, there are only 6 substring, in which this `A` contribute 1
point as unique string.

 **Instead of** counting all unique characters and struggling with all
possible substrings,  
we can count for every char in S, how many ways to be found as a unique char.  
We count and sum, and it will be out answer.

 **Explanation:**

  1. `index[26][2]` record last two occurrence index for every upper characters.
  2. Initialise all values in `index` to `-1`.
  3. Loop on string S, for every character `c`, update its last two occurrence index to `index[c]`.
  4. Count when loop. For example, if "A" appears twice at index 3, 6, 9 seperately, we need to count:
    * For the first "A": (6-3) * (3-(-1))"
    * For the second "A": (9-6) * (6-3)"
    * For the third "A": (N-9) * (9-6)"

 **Complexity:**  
One pass, time complexity O(N).  
Space complexity O(1).

 **C++:**

    
    
         int uniqueLetterString(string S) {
            int index[26][2], res = 0, N = S.length(), mod = pow(10, 9) + 7;
            memset(index, -1, sizeof(int) * 52);
            for (int i = 0; i < N; ++i) {
                int c = S[i] - 'A';
                res = (res + (i - index[c][1]) * (index[c][1] - index[c][0]) % mod) % mod;
                index[c][0] = index[c][1];
                index[c][1] = i;
            }
            for (int c = 0; c < 26; ++c)
                res = (res + (N - index[c][1]) * (index[c][1] - index[c][0]) % mod) % mod;
            return res;
        }
    
    

**Java:**

    
    
         public int uniqueLetterString(String S) {
            int[][] index = new int[26][2];
            for (int i = 0; i < 26; ++i) Arrays.fill(index[i], -1);
            int res = 0, N = S.length(), mod = (int)Math.pow(10, 9) + 7;
            for (int i = 0; i < N; ++i) {
                int c = S.charAt(i) - 'A';
                res = (res + (i - index[c][1]) * (index[c][1] - index[c][0]) % mod) % mod;
                index[c] = new int[] {index[c][1], i};
            }
            for (int c = 0; c < 26; ++c)
                res = (res + (N - index[c][1]) * (index[c][1] - index[c][0]) % mod) % mod;
            return res;
        }
    
    

**Python:**

    
    
        def uniqueLetterString( self, S):
            index = {c: [-1, -1] for c in string.ascii_uppercase}
            res = 0
            for i, c in enumerate(S):
                k, j = index[c]
                res += (i - j) * (j - k)
                index[c] = [j, i]
            for c in index:
                k, j = index[c]
                res += (len(S) - j) * (j - k)
            return res % (10**9 + 7)
    


### Solution 2
In each loop, We caculate `cur[i]`, which represent the sum of Uniq() for all
substrings whose last char is S.charAt(i).

For example,  
`S = 'ABCBD'`  
When i = 2, `cur[2] = Uniq('ABC') + Uniq('BC') + Uniq('C')`  
When i = 3, `cur[3] = Uniq('ABCB') + Uniq('BCB') + Uniq('CB') + Uniq('B')`

Notice, we append char 'B' into each previous substrings. Only in substrings
'CB' and 'B', the char 'B' can be identified as uniq. The contribution of 'B'
from cur[2] to cur[3] is `i - showLastPosition['B']`. At the same time, in
substrings 'ABCB', 'BCB', the char 'B' can‘t’ be identified as uniq any more,
the previous contribution of 'B' should be removed.

So we have`'cur[i] = cur[i - 1] - contribution[S.charAt(i)] + (i -
showLastPosition[S.charAt(i)])`  
Then the new `contribution[S.charAt(i)] = i - showLastPosition[S.charAt(i)]`

The final result is the sum of all `cur[i]`.

Thanks [@wangzi6147](https://discuss.leetcode.com/user/wangzi6147) for this
picture:  
![image](https://s3-lc-
upload.s3.amazonaws.com/users/ankailiang/image_1525586587.png)

This is my code. I didn't do mod operation, but it still pass : )

    
    
        public int uniqueLetterString(String S) {
            
            int res = 0;
            if (S == null || S.length() == 0)
                return res;    
            int[] showLastPosition = new int[128];
            int[] contribution = new int[128];
            int cur = 0;
            for (int i = 0; i < S.length(); i++) {
                char x = S.charAt(i);
                cur -= contribution[x]; 
                contribution[x] = (i - (showLastPosition[x] - 1));
                cur += contribution[x]; 
                showLastPosition[x] = i + 1;
                res += cur;
            }   
            return res;
        }
    

The showLastPosition[x] represent the last showing positon of char x befor i.
(The initial showLastPosition[x] for all chars should be '-1'. For
convenience, I shift it to '0'. It may cause some confusion, notice I update
"showLastPosition[x] = i + 1" for each loop.)


### Solution 3
Let `dp[i]` is sum of unique char in all substring ending at `i`, then the
answer is `sum(dp[i]), i=[0..n-1]`. It's not difficult to get the recursive
formula:  
`dp[i] = dp[i-1] + (i - first_from_i(s[i])) - (first_from_i(s[i]) -
second_from_i(s[i]))`

Take the below example:

    
    
    BBBBBBBBBBBBBBBBBOABCDOABCOABC
                     ^    ^   ^
                     s    f   i
    
    dp[i] = dp[i-1] + (i-f) - (f-s)
    

When extending `s[i]` to all substrings ending with `s[i-1]`, there are
`(i-f)` more unique char `s[i]`, and `(f-s)` less unique char because of
duplicate of `s[i]`.

The implementation is quite simple. Here is Java version:

    
    
        public int uniqueLetterString(String s) {
                final int MOD = 1000000007;
                int res = 0, dp = 0;
                int[] first = new int[26], second = new int[26];
    
                for (int i = 0; i < s.length(); i++) {
                    int ci = s.charAt(i) - 'A';
                    dp = dp + 1 + i - first[ci] * 2 + second[ci];
                    res = (res + dp) % MOD;
    
                    second[ci] = first[ci];
                    first[ci] = i + 1;
                }
    
                return res;
        }
    

Please vote if you like :)



