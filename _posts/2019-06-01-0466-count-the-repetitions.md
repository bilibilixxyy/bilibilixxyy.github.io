---
layout: post
title: 466. Count The Repetitions
---
### Question
Define `S = [s,n]` as the string S which consists of n connected strings s.
For example, `["abc", 3]` ="abcabcabc".

On the other hand, we define that string s1 can be obtained from string s2 if
we can remove some characters from s2 such that it becomes s1. For example,
“abc” can be obtained from “abdbec” based on our definition, but it can not be
obtained from “acbbe”.

You are given two non-empty strings s1 and s2 (each at most 100 characters
long) and two integers 0 ≤ n1 ≤ 106 and 1 ≤ n2 ≤ 106. Now consider the strings
S1 and S2, where `S1=[s1,n1]` and `S2=[s2,n2]`. Find the maximum integer M
such that `[S2,M]` can be obtained from `S1`.

 **Example:**

    
    
    Input:
    s1="acb", n1=4
    s2="ab", n2=2
    
    Return:
    2
    

### Solution 1
I didn't come up with any good solution so I tried brute force. Key points:

  1. How do we know "string s2 can be obtained from string s1"? Easy, use two pointers iterate through s2 and s1. If chars are equal, move both. Otherwise only move pointer1.
  2. We repeat step 1 and go through s1 for n1 times and count how many times can we go through s2.
  3. Answer to this problem is times go through s2 divide by n2.

    
    
    public class Solution {
        public int getMaxRepetitions(String s1, int n1, String s2, int n2) {
            char[] array1 = s1.toCharArray(), array2 = s2.toCharArray();
            int count1 = 0, count2 = 0, i = 0, j = 0;
            
            while (count1 < n1) {
                if (array1[i] == array2[j]) {
                    j++;
                    if (j == array2.length) {
                        j = 0;
                        count2++;
                    }
                }
                i++;
                if (i == array1.length) {
                    i = 0;
                    count1++;
                }
            }
            
            return count2 / n2;
        }
    }
    


### Solution 2
_You can find more of my code/explanations in my github
repo[lzl124631x/LeetCode](https://github.com/lzl124631x/LeetCode)_

It's easy to come up with a brute force solution and to find that there will
be a **repetitive pattern** when matching `S2` through `S1`. The only problem
is how to use the repetitive pattern to save computation.

 **Fact:**  
If `s2` repeats in `S1` `R` times, then `S2` must repeats in `S1` `R / n2`
times.  
 **Conclusion:**  
We can simply count the repetition of `s2` and then divide the count by `n2`.

 **How to denote repetition:**  
We need to scan `s1` `n1` times. Denote each scanning of `s1` as an `s1`
segment.  
After each scanning of `i`-th `s1` segment, we will have

  1. The accumulative count of `s2` repeated in this `s1` segment.
  2. A `nextIndex` that `s2[nextIndex]` is the first letter you'll be looking for in the next `s1` segment.

> Suppose `s1="abc"`, `s2="bac"`, `nextIndex` will be `1`; `s1="abca"`,
`s2="bac"`, `nextIndex` will be `2`

It is the `nextIndex` that is the denotation of the repetitive pattern.

 **Example:**

    
    
    Input:
     s1="abacb", n1=6
    s2="bcaa", n2=1
    
    Return:
    3
    
    
    
                        0 1    2 3 0      1    2 3 0      1    2 3 0  
    S1 --------------> abacb | abacb | abacb | abacb | abacb | abacb 
    
    repeatCount ----->    0  |   1   |   1   |   2   |   2   |   3
    
    Increment of 
    repeatCount     ->    0  |   1   |   0   |   1   |   0   |   1
    
    nextIndex ------->    2  |   1   |   2   |   1   |   2   |   1
                                         ^
    									 |
    									 repetitive pattern found here (we've met 2 before)!
    									 The pattern repeated 3 times
    

The `nextIndex` has `s2.size()` possible values, ranging from `0` to
`s2.size() - 1`. Due to PigeonHole principle, you must find two same
`nextIndex` after scanning `s2.size() + 1` `s1` segments.

Once you meet a `nextIndex` you've met before, you'll know that the following
`nextIndex`s and increments of `repeatCount` will repeat a pattern.

So let's separate the `s1` segments into 3 parts:

  1. the prefix part before repetitive pattern
  2. the repetitive part
  3. the suffix part after repetitive pattern (incomplete repetitive pattern remnant)

All you have to do is add up the repeat counts of the 3 parts.

    
    
    // OJ: https://leetcode.com/problems/count-the-repetitions/
    // Author: github.com/lzl124631x
    // Time: O(|s1| * n1) where |s1| is the length of s1
    // Space: O(n1)
    class Solution {
    public:
        int getMaxRepetitions(string s1, int n1, string s2, int n2) {
            vector<int> repeatCount(n1 + 1, 0);
            vector<int> nextIndex(n1 + 1, 0);
            int j = 0, cnt = 0;
            for (int k = 1; k <= n1; ++k) {
                for (int i = 0; i < s1.size(); ++i) {
                    if (s1[i] == s2[j]) {
                        ++j;
                        if (j == s2.size()) {
                            j = 0;
                            ++cnt;
                        }
                    }
                }
                repeatCount[k] = cnt;
                nextIndex[k] = j;
                for (int start = 0; start < k; ++start) {
                    if (nextIndex[start] == j) { // see if you have met this nextIndex before
                        // if found, you can calculate the 3 parts
                        int prefixCount = repeatCount[start]; // prefixCount is the start-th repeatCount
                        // (repeatCount[k] - prefixCount) is the repeatCount of one occurrance of the pattern
                        // There are (n1 - start) / (k - start) occurrances of the pattern
                        // So (n1 - start) / (k - start) * (repeatCount[k] - prefixCount) is the repeatCount of the repetitive part
                        int patternCount = (n1 - start) / (k - start) * (repeatCount[k] - prefixCount);
                        // The suffix contains the incomplete repetitive remnant (if any)
                        // Its length is (n1 - start) % (k - start)
                        // So the suffix repeatCount should be repeatCount[start + (n1 - start) % (k - start)] - prefixCount
                        int suffixCount = repeatCount[start + (n1 - start) % (k - start)] - prefixCount;
                        return (prefixCount + patternCount + suffixCount) / n2;
                    }
                }
            }
            return repeatCount[n1] / n2;
        }
    };
    

* * *

Update 12/31/2018

  * `int patternCount = (repeatCount[k] - repeatCount[start]) * (n1 - start) / (k - start);` to  
`int patternCount = (repeatCount[k] - repeatCount[start]) * ((n1 - start) / (k
- start));` since `a * b / c` doesn't necessarily equal `a * (b / c)`. (the
old test cases didn't cover this case)

  * The size of `repeatCount` and `nextIndex` should be `n1 + 1`.  
Thanks for comments from @wxd_sjtu, @rjtsdl, @Rongch

Another version using `unordered_map` to save computation. Reduce runtime from
~80ms to ~4ms.

    
    
    // OJ: https://leetcode.com/problems/count-the-repetitions/
    // Author: github.com/lzl124631x
    // Time: O(|s1| * n1) where |s1| is the length of s1
    // Space: O(n1)
    class Solution {
    public:
        int getMaxRepetitions(string s1, int n1, string s2, int n2) {
            unordered_map<int, int> kToRepeatCount;
            unordered_map<int, int> nextIndexToK;
            kToRepeatCount[0] = 0;
            nextIndexToK[0] = 0;
            int j = 0, cnt = 0;
            for (int k = 1; k <= n1; ++k) {
                for (int i = 0; i < s1.size(); ++i) {
                    if (s1[i] == s2[j]) {
                        ++j;
                        if (j == s2.size()) {
                            j = 0;
                            ++cnt;
                        }
                    }
                }
                if (nextIndexToK.find(j) != nextIndexToK.end()) {
                    int start = nextIndexToK[j];
                    int prefixCount = kToRepeatCount[start];
                    int patternCount = (n1 - start) / (k - start) * (cnt - prefixCount);
                    int suffixCount = kToRepeatCount[start + (n1 - start) % (k - start)] - prefixCount;
                    return (prefixCount + patternCount + suffixCount) / n2;
                }
                kToRepeatCount[k] = cnt;
                nextIndexToK[j] = k;
            }
            return kToRepeatCount[n1] / n2;
        }
    };
    


### Solution 3
The key is, we just need to calculate _**what will remain after s1 obtains
s2**_.

That is, `(s1, s2) -> (sRemain, matchCnt)`; for example,  
`(abcd, ab) -> (cd, 1)`  
`(ababa, ab) -> (a, 2)`  
`(a, aaaa) -> (a, 0)`  
`(aabaabaab, aba) -> (ab, 2)` as `aabaaba` exactly matches `aba` twice.

And, each time we append `s1` to the `remain string`, to make a sequence:
(Using `[]` to mark the `remain string`)  
`(abcd, ab): abcd -> [cd]abcd -> [cd]abcd -> [cd]abcd -> ...`  
`(ababa, ab): ababa -> [a]ababa -> [a]ababa -> [a]ababa -> ...`  
`(a, aaaa): a -> [a]a -> [aa]a -> [aaa]a -> a -> [a]a -> [aa]a -> ...`  
`(aabaabaab, aba): aabaabaab -> [ab]aabaabaab -> [ab]aabaabaab -> ...`

Obviously, there will be a loop in the sequence, assume the length of loop is
`loop`, and the length before the loop is `k`.  
`(abcd, ab): loop=1, k=1`  
`(a, aaaa): loop=4, k=0`  
`(aabaabaab, aba): loop=1, k=1`

So, we just need to calculate `the count of each loop`, and `the count before
entering the loop`, and calculate the total.

Here is the code with detailed comment, 21ms.

    
    
    public class Solution {
        public int getMaxRepetitions(String s1, int n1, String s2, int n2) {
            if (!ableToObtain(s1, s2)) return 0; // check if [s1. ∞] obtains s2
            int cnt=0, k=-1;
            String s=s1;
            StringBuilder remainBuilder; // record `remain string`
            ArrayList<String> stringList=new ArrayList<>(); // record all the `remain string`
            ArrayList<Integer> countList=new ArrayList<>(); // record matching count from start to the current remain string 
            stringList.add(""); // record empty string
            countList.add(0);
            for (int i=0;i<=n1;i++) {
                remainBuilder=new StringBuilder();
                cnt+=getRemain(s, s2, remainBuilder); // get the next remain string, returns the count of matching
                String remain=remainBuilder.toString();
                if ((k=stringList.indexOf(remain))!=-1) break; // if there is a loop, break
                stringList.add(remain); // record the remain string into arraylist 
                countList.add(cnt);
                s=remain+s1; // append s1 to make a new string
            }
            // here, k is the beginning of the loop
            if (k==-1) return cnt/n2; // if there is no loop
            int countOfLoop=cnt-countList.get(k), loopLength=stringList.size()-k; // get matching count in the loop, and loop length
            cnt=countList.get(k);
            n1-=k;
            cnt+=countOfLoop*(n1/loopLength);
            n1%=loopLength; 
            cnt+=countList.get(k+n1)-countList.get(k);
            return cnt/n2;
        }
    
        // check if [s1. ∞] obtains s2
        private boolean ableToObtain(String s1, String s2) {
            boolean[] cnt=new boolean[26];
            for (char c: s1.toCharArray()) cnt[c-'a']=true;
            for (char c: s2.toCharArray()) {
                if (!cnt[c-'a']) return false;
            }
            return true;
        }
    
        // get remain string after s1 obtains s2, return the matching count
        private int getRemain(String s1, String s2, StringBuilder remain) {
            int cnt=0, lastMatch=-1, p2=0;
            for (int p1=0;p1<s1.length();p1++) {
                if (s1.charAt(p1)==s2.charAt(p2)) {
                    if (++p2==s2.length()) {
                        p2=0;
                        cnt++;
                        lastMatch=p1;
                    }
                }
            }
            remain.append(s1.substring(lastMatch+1));
            return cnt;
        }
    }
    



