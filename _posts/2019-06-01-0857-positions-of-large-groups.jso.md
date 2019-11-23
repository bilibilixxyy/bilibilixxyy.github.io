---
layout: post
title: 857. Positions of Large Groups
---
### Question
In a string `S` of lowercase letters, these letters form consecutive groups of
the same character.

For example, a string like `S = "abbxxxxzyy"` has the groups `"a"`, `"bb"`,
`"xxxx"`, `"z"` and `"yy"`.

Call a group _large_ if it has 3 or more characters.   We would like the
starting and ending positions of every large group.

The final answer should be in lexicographic order.



 **Example 1:**

    
    
     **Input:** "abbxxxxzzy"
    **Output:** [[3,6]]
    **Explanation** : "xxxx" is the single large group with starting  3 and ending positions 6.
    

**Example 2:**

    
    
    **Input:** "abc"
    **Output:** []
    **Explanation** : We have "a","b" and "c" but no large group.
    

**Example 3:**

    
    
    **Input:** "abcdddeeeeaabbbcd"
    **Output:** [[3,5],[6,9],[12,14]]



 **Note:  ** `1 <= S.length <= 1000`

### Solution 1
For every groups, find its start index `i` and end index `j - 1`  
Group length is `j - i`, if it's no less than 3, add `(i, j)` to result.

 **Java:**

    
    
        public List<List<Integer>> large GroupPositions(String S) {
            List<List<Integer>> res = new ArrayList<>();
            for (int i = 0, j = 0; i < S.length(); i = j) {
                while (j < S.length() && S.charAt(j) == S.charAt(i)) ++j;
                if (j - i >= 3)
                    res.add(Arrays.asList(i, j - 1));
            }
            return res;
        }
    

**C++:**

    
    
        vector<vector<int>> largeGroupPositions(string S) {
            vector<vector<int>> res;
             for (int i = 0, j = 0; i < S.size(); i = j) {
                while (j < S.size() && S[j] == S[i]) ++j;
                if (j - i >= 3)
                    res.push_back({i, j - 1});
            }
            return res;
        }
    

**Python:**

    
    
        def largeGroupPositions(self, S):
             i, j, N = 0, 0, len(S)
            res = []
            while i < N:
                while j < N and S[j] == S[i]: j += 1
                if j - i >= 3: res.append((i, j - 1))
                i = j
            return res
    


### Solution 2
Using finditer, first thing that came to my mind:

    
    
            return [[r.start(), r.end() - 1] for r in re.finditer(r'(\w)  
    {2,}', S)]
    


### Solution 3
I found the lexicographic order requirement ambiguous. That's fine in an
interview setting - the requirement might be underspecified on purpose, making
the candidate request clarification - but for a contest it seems sloppy.

Fortunately, one natural way to solve the problem produces results in sorted
order by start index, and that (apparently) meets the order requirement.



