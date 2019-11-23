---
layout: post
title: 691. Stickers to Spell Word
---
### Question
We are given N different types of stickers. Each sticker has a lowercase
English word on it.

You would like to spell out the given `target` string by cutting individual
letters from your collection of stickers and rearranging them.

You can use each sticker more than once if you want, and you have infinite
quantities of each sticker.

What is the minimum number of stickers that you need to spell out the
`target`? If the task is impossible, return -1.

 **Example 1:**

Input:

    
    
    ["with", "example", "science"], "thehat"
    

Output:

    
    
    3
    

Explanation:

    
    
    We can use 2 "with" stickers, and 1 "example" sticker.
    After cutting and rearrange the letters of those stickers, we can form the target "thehat".
    Also, this is the minimum number of stickers necessary to form the target string.
    

**Example 2:**

Input:

    
    
    ["notice", "possible"], "basicbasic"
    

Output:

    
    
    -1
    

Explanation:

    
    
    We can't form the target "basicbasic" from cutting letters from the given stickers.
    

**Note:**

* `stickers` has length in the range `[1, 50]`.
* `stickers` consists of lowercase English words (without apostrophes).
* `target` has length in the range `[1, 15]`, and consists of lowercase English letters.
* In all test cases, all words were chosen _randomly_ from the 1000 most common US English words, and the target was chosen as a concatenation of two random words.
* The time limit may be more challenging than usual. It is expected that a 50 sticker test case can be solved within 35ms on average.

### Solution 1
There are potentially a lot of overlapping sub problems, but meanwhile we
don't exactly know what those sub problems are. DP with memoization works
pretty well in such cases. The workflow is like backtracking, but with
memoization. Here I simply use a sorted string of target as the key for the
unordered_map DP. A sorted target results in a unique sub problem for possibly
different strings.

    
    
    dp[s] is the minimum stickers required for string s (-1 if impossible). Note s is sorted.
    clearly, dp[""] = 0, and the problem asks for dp[target].
    

The DP formula is:

    
    
    dp[s] = min(1+dp[reduced_s]) for all stickers, 
    here reduced_s is a new string after certain sticker applied
    

Optimization: If the target can be spelled out by a group of stickers, at
least one of them has to contain character target[0]. So I explicitly require
next sticker containing target[0], which significantly reduced the search
space.

BTW, I am not good at Java. Looking forward to your revision!

C++

    
    
    class Solution {
    public:
        int minStickers(vector<string>& stickers, string target) {
            int m = stickers.size();
            vector<vector<int>> mp(m, vector<int>(26, 0));
            unordered_map<string, int> dp;
            // count characters a-z for each sticker 
            for (int i = 0; i < m; i++) 
                for (char c:stickers[i]) mp[i][c-'a']++;
            dp[""] = 0;
            return helper(dp, mp, target);
        }
    private:
        int helper(unordered_map<string, int>& dp, vector<vector<int>>& mp, string target) {
            if (dp.count(target)) return dp[target];
            int ans = INT_MAX, n = mp.size();
            vector<int> tar(26, 0);
            for (char c:target) tar[c-'a']++;
            // try every sticker
            for (int i = 0; i < n; i++) {
                // optimization
                if (mp[i][target[0]-'a'] == 0) continue; 
                string s;
                // apply a sticker on every character a-z
                for (int j = 0; j < 26; j++) 
                    if (tar[j]-mp[i][j] > 0) s += string(tar[j]-mp[i][j], 'a'+j);
                int tmp = helper(dp, mp, s);
                if (tmp!= -1) ans = min(ans, 1+tmp);
            }
            dp[target] = ans == INT_MAX? -1:ans;
            return dp[target];
        }
    };
    

Java

    
    
    class Solution {
        public int minStickers(String[] stickers, String target) {
            int m = stickers.length;
            int[][] mp = new int[m][26];
            Map<String, Integer> dp = new HashMap<>();
            for (int i = 0; i < m; i++) 
                for (char c:stickers[i].toCharArray()) mp[i][c-'a']++;
            dp.put("", 0);
            return helper(dp, mp, target);
        }
        private int helper(Map<String, Integer> dp, int[][] mp, String target) {
            if (dp.containsKey(target)) return dp.get(target);
            int ans = Integer.MAX_VALUE, n = mp.length;
            int[] tar = new int[26];
            for (char c:target.toCharArray()) tar[c-'a']++;
            // try every sticker
            for (int i = 0; i < n; i++) {
                // optimization
                if (mp[i][target.charAt(0)-'a'] == 0) continue;
                StringBuilder sb = new StringBuilder();
                // apply a sticker on every character a-z
                for (int j = 0; j < 26; j++) {
                    if (tar[j] > 0 ) 
                        for (int k = 0; k < Math.max(0, tar[j]-mp[i][j]); k++)
                            sb.append((char)('a'+j));
                }
                String s = sb.toString();
                int tmp = helper(dp, mp, s);
                if (tmp != -1) ans = Math.min(ans, 1+tmp);
            }
            dp.put(target, ans == Integer.MAX_VALUE? -1:ans);
            return dp.get(target);
        }
    }
    

Python

    
    
    class Solution(object):
        def minStickers(self, stickers, target):
            m = len(stickers)
            mp = [[0]*26 for y in range(m)] 
            for i in range(m):
                for c in stickers[i]:
                    mp[i][ord(c)-ord('a')] += 1    
            dp = {}
            dp[""] = 0
            
            def helper(dp, mp, target):
                if target in dp:
                    return dp[target]
                n = len(mp)
                tar = [0]*26
                for c in target:
                    tar[ord(c)-ord('a')] += 1   
                ans = sys.maxint
                for i in xrange(n):
                    if mp[i][ord(target[0])-ord('a')] == 0:
                        continue
                    s = ''
                    for j in range(26):
                        if tar[j] > mp[i][j]:
                            s += chr(ord('a')+j)*(tar[j] - mp[i][j]) 
                    tmp = helper(dp, mp, s)
                    if (tmp != -1): 
                        ans = min(ans, 1+tmp)    
                dp[target] = -1 if ans == sys.maxint else ans
                return dp[target]
                    
            return helper(dp, mp, target)
    


### Solution 2
@dreamoon's [contest solution](https://leetcode.com/contest/leetcode-weekly-
contest-53/ranking) is quite nice, but it uses lots of macros. I rewrote it
without them and also made it faster (about 130 ms vs 200 ms). Thanks to
@feng-peng-3597 for [pointing it
out](https://discuss.leetcode.com/topic/106337/dreamoon-s-code-with-comment-
added-by-me).

    
    
    int minStickers(vector<string>& stickers, string target) {
        int n = target.size(), N = 1 << n;
        vector<uint> dp(N, -1);
        dp[0] = 0;
        for (int i = 0; i < N; i++) if (dp[i] != -1) {
            for (string& s : stickers) {
                int now = i;
                for (char c : s) {
                    for (int r = 0; r < n; r++) {
                        if (target[r] == c && !((now >> r) & 1)) {
                            now |= 1 << r;
                            break;
                        }
                    }
                }
                dp[now] = min(dp[now], dp[i] + 1);
            }
        }
        return dp[N-1];
    }


### Solution 3
This is trying to explain @StefanPochmann 's post in here:  
<https://discuss.leetcode.com/topic/106368/rewrite-of-contest-winner-s-
solution>

  1. The big idea is to use unsigned number from `0` to `2^n-1` as bitmap to represent every `subset` of `target`;
  2. then populate all of these subset from `0` to `2^n-1` by trying to apply 1 sticker at each time.
  3. Eventually you might or might not get the ultimate result `2^n-1`, which is `target`, populated.
  4. If not, it is -1;

    
    
    class Solution {
    public:
        int minStickers(vector<string>& stickers, string target) {
            int n = target.size(), m = 1 << n; // if target has n chars, there will be m=2^n subset of characters in target
            vector<uint> dp(m, -1); // use index 0 - 2^n as bitmaps to represent each subset of all chars of target
            dp[0] = 0; // first thing we know is : dp[empty set] requires 0 stickers,
            for (int i = 0; i < m; i++) { // for every subset i, start from 00000...(emptyset) to 111111...(the target)
                if (dp[i] == -1) continue;
                for (string& s : stickers) { // try use each sticker as an char provider to populate a super-set of i,
                    int sup = i;
                    for (char c : s) { // to do that, for each char in the sticker, 
                        for (int r = 0; r < n; r++) {
                            if (target[r] == c && !((sup >> r) & 1)) { // try apply it on a missing char in the subset of target
                                sup |= 1 << r;
                                break;
                            }
                        }
                    }
                   // after you apply all possible chars in a sticker, you get an superset that take 1 extra sticker than subset
                   // would take, so you can try to update the superset's minsticker number with dp[sub]+1;
                    dp[sup] = min(dp[sup], dp[i] + 1); 
                }
            }
            return dp[m - 1]; // and the ultimate result
        }
    };
    

**Java**

    
    
     class Solution {
        public int minStickers(String[] stickers, String target) {
            int n = target.length(), m = 1 << n; // if target has n chars, there will be m=2^n subset of characters in target
            int[] dp = new int[m];
            for (int i = 0; i < m; i++) dp[i] = Integer.MAX_VALUE; // use index 0 - 2^n as bitmaps to represent each subset of all chars in target
            dp[0] = 0; // first thing we know is : dp[empty set] requires 0 stickers,
            for (int i = 0; i < m; i++) { // for every subset i, start from 000...000
                if (dp[i] == Integer.MAX_VALUE) continue;
                for (String s : stickers) { // try use each sticker as an char provider to populate 1 of its superset, to do that:
                    int sup = i;
                    for (char c : s.toCharArray()) { // for each char in the sticker, try apply it on a missing char in the subset of target
                        for (int r = 0; r < n; r++) {
                            if (target.charAt(r) == c && ((sup >> r) & 1) == 0) {
                                sup |= 1 << r;
                                break;
                            }
                        }
                    }
                   // after you apply all possible chars in a sticker, you get an superset that take 1 extra sticker than subset
                   // would take, so you can try to update the superset's minsticker number with dp[sub]+1;
                    dp[sup] = Math.min(dp[sup], dp[i] + 1);
                }
            }
            return dp[m - 1] != Integer.MAX_VALUE ? dp[m - 1] : -1;
        }
    }
    

Does these explanation make any sense?



