---
layout: post
title: 973. Stamping The Sequence
---
### Question
You want to form a `target` string of **lowercase letters**.

At the beginning, your sequence is `target.length` `'?'` marks.  You also have
a `stamp` of lowercase letters.

On each turn, you may place the stamp over the sequence, and replace every
letter in the sequence with the corresponding letter from the stamp.  You can
make up to `10 * target.length` turns.

For example, if the initial sequence is "?????", and your stamp is `"abc"`,
then you may make "abc??", "?abc?", "??abc" in the first turn.  (Note that the
stamp must be fully contained in the boundaries of the sequence in order to
stamp.)

If the sequence is possible to stamp, then return an array of the index of the
left-most letter being stamped at each turn.  If the sequence is not possible
to stamp, return an empty array.

For example, if the sequence is "ababc", and the stamp is `"abc"`, then we
could return the answer `[0, 2]`, corresponding to the moves "?????" ->
"abc??" -> "ababc".

Also, if the sequence is possible to stamp, it is guaranteed it is possible to
stamp within `10 * target.length` moves.  Any answers specifying more than
this number of moves will not be accepted.



 **Example 1:**

    
    
     **Input:** stamp = "abc", target = "ababc"
    **Output:** [0,2]
    ([1,0,2] would also be accepted as an answer, as well as some other answers.)
    

**Example 2:**

    
    
    **Input:** stamp = "abca", target = "aabcaca"
    **Output:** [3,0,1]
    



 **Note:**

  1. `1 <= stamp.length <= target.length <= 1000`
  2. `stamp` and `target` only contain lowercase letters.

### Solution 1
What I basiclly did here is trying to reverse the whole operations.  
The operation token later will be more apperant than the operation token
before. The letters which stamped later will cover the letters stamped before
and we really don't care about the letters which are covered.

    
    
     *  *  *  *  *  *  *
     *  *  * |a  b  c  a|
    |a  b  c  a| b  c  a
     a |a  b  c  a| c  a
    

We just try to match the stamp with the target. Since we do not care about the
letters which are coverd by others, so we can apply a `*` match any letters.
For example:

    
    
    "aabcaca" -> "a****ca" -> "*****ca"->"*******"
    

It's just my contest code, needed to be revised.

    
    
    class Solution {
    public:
        vector<int> movesToStamp(string stamp, string target) {
            vector<int> ans;
            vector<int> output;
            string str = target;
            string aim(target.length(),'*');
            while(str != aim){
                int tmp = remove(str,stamp);
                if(tmp == str.length()) return output;
                ans.push_back(tmp);
            }
            for(int iter=ans.size()-1;iter>=0;--iter) output.push_back(ans[iter]);
            return output;
        }
        int remove(string& str, string stamp){
            for(int iter=0;iter<str.length();++iter){
                int jter=0,tmp=iter;
                bool flag=false;
                while(jter<stamp.length() && tmp<str.length() && (str[tmp]=='*' || str[tmp]==stamp[jter])){
                    if(str[tmp]==stamp[jter]) flag=true;
                    tmp++;
                    jter++;
                }
                if(jter==stamp.length() && flag){
                    for(int kter=0;kter<stamp.length();++kter)
                        str[iter+kter]='*';
                    return iter;
                }
            }
            return str.length();
        }
    };
    


### Solution 2
It's better explained through an example. Let's say the target is `'aabccbc'`,
and stamp is `'abc'`. We first try to find `'abc'` and replace it with
`'***'`. After there are no more replacements, we will try `'*bc'` and
`'ab*'`, and so on. Now, turn by turn:

    
    
    'aabccbc' ? 'abc' = [1]
    'a***cbc' ? '*bc' = []
    'a***cbc' ? 'ab*' = []
    'a***cbc' ? 'a**' = [0]
    '****cbc' ? '*b*' = []
    '****cbc' ? '**c' = [2]
    '*****bc' ? '*bc' = [4]
    

The result is: [4, 2, 0, 1]. It feels to me that this greedy solution produces
the minumum possible number of stamps, though I cannot provide a strict proof
:)

    
    
    vector<int> movesToStamp(string stamp, string target) {
      vector<int> res;
      auto total_stamp = 0, turn_stamp = -1;
      while (turn_stamp) {
          turn_stamp = 0;
          for (int sz = stamp.size(); sz > 0; --sz) 
              for (auto i = 0; i <= stamp.size() - sz; ++i) {
                  auto new_stamp = string(i, '*') + stamp.substr(i, sz) + string(stamp.size() - sz - i, '*');
                  auto pos = target.find(new_stamp);
                  while (pos != string::npos) {
                      res.push_back(pos);
                      turn_stamp += sz;
                      fill(begin(target) + pos, begin(target) + pos + stamp.size(), '*');
                      pos = target.find(new_stamp);
                  }
              }
          total_stamp += turn_stamp;
      }
      reverse(begin(res), end(res));
      return total_stamp == target.size() ? res : vector<int>();
    }
    


### Solution 3
The idea is the same as the most upvoted post. Think reversely!  
Let's take an example to illustrate.  
`Stamp` = "abc", `Target` = "ababcbc`

  1. Target = `ab***bc`
  2. Target = `ab*****`
  3. Target = `*******`

We will go through the whole `Target` string to find if there exists any
substring equals to `Stamp`. And then replace the whole substring with `*`.
You can see in the step `1`, we replace substring `abc` with `***`. Then we
keep doing the same thing. In the step `2`, you will find we replace substring
`*bc` to `***`. `*` can match to any character because `*` will be override by
the next stamp. Finally we get the result and output the reversed result. When
`# of stars` equals to `target.length()`, we will return the result. If during
one scan, we are not able to replace even one substring with `*`, directly
return empty array.

`Comments` for two helper functions:  
`canReplace(char[] T, int p, char[] S)` is used to check if any substring from
Target is existing to be replaced with `*`.  
`doReplace(char[] T, int p, int len, int count)` is used to replace the
substring with `*` and return the total number of `*` we have now.

    
    
    class Solution {
        public int[] movesToStamp(String stamp, String target) {
            char[] S = stamp.toCharArray();
            char[] T = target.toCharArray();
            List<Integer> res = new ArrayList<>();
            boolean[] visited = new boolean[T.length];
            int stars = 0;
            
            while (stars < T.length) {
                boolean doneReplace = false;
                for (int i = 0; i <= T.length - S.length; i++) {
                    if (!visited[i] && canReplace(T, i, S)) {
                        stars = doReplace(T, i, S.length, stars);
                        doneReplace = true;
                        visited[i] = true;
                        res.add(i);
                        if (stars == T.length) {
                            break;
                        }
                    }
                }
                
                if (!doneReplace) {
                    return new int[0];
                }
            }
            
            int[] resArray = new int[res.size()];
            for (int i = 0; i < res.size(); i++) {
                resArray[i] = res.get(res.size() - i - 1);
            }
            return resArray;
        }
        
        private boolean canReplace(char[] T, int p, char[] S) {
            for (int i = 0; i < S.length; i++) {
                if (T[i + p] != '*' && T[i + p] != S[i]) {
                    return false;
                }
            }
            return true;
        }
        
        private int doReplace(char[] T, int p, int len, int count) {
            for (int i = 0; i < len; i++) {
                if (T[i + p] != '*') {
                    T[i + p] = '*';
                    count++;
                }
            }
            return count;
        } 
    }
    



