---
layout: post
title: 768. Partition Labels
---
### Question
A string `S` of lowercase letters is given. We want to partition this string
into as many parts as possible so that each letter appears in at most one
part, and return a list of integers representing the size of these parts.

 **Example 1:**  

    
    
     **Input:** S = "ababcbacadefegdehijhklij"
    **Output:** [9,7,8]
    **Explanation:**
    The partition is "ababcbaca", "defegde", "hijhklij".
    This is a partition so that each letter appears in at most one part.
    A partition like "ababcbacadefegde", "hijhklij" is incorrect, because it splits S into less parts.
    

**Note:**  

  1. `S` will have length in range `[1, 500]`.
  2. `S` will consist of lowercase letters (`'a'` to `'z'`) only.

### Solution 1
  1. traverse the string record the last index of each char.
  2. using pointer to record end of the current sub string.

    
    
    public List<Integer> partitionLabels(String S) {
            if(S == null || S.length() == 0){
                return null;
            }
            List<Integer> list = new ArrayList<>();
            int[] map = new int[26];  // record the last index of the each char
    
            for(int i = 0; i < S.length(); i++){
                map[S.charAt(i)-'a'] = i;
            }
            // record the end index of the current sub string
            int last = 0;
            int start = 0;
            for(int i = 0; i < S.length(); i++){
                last = Math.max(last, map[S.charAt(i)-'a']);
                if(last == i){
                    list.add(last - start + 1);
                    start = last + 1;
                }
            }
            return list;
        }
    


### Solution 2
    
    
    def partitionLabels(self, S):
        sizes = []
        while S:
            i = 1
            while set(S[:i]) & set(S[i:]):
                i += 1
            sizes.append(i)
            S = S[i:]
        return sizes


### Solution 3
First pass we record the last position for each letter. Second pass we keep
the maximum position of the letters we have seen so far. If the pointer
exceeds the maximum position, we found the part.

    
    
    vector<int> partitionLabels(string S) {
      vector<int> res, pos(26, 0);  
      for (auto i = 0; i < S.size(); ++i) pos[S[i] - 'a'] = i;
      for (auto i = 0, idx = INT_MIN, last_i = 0; i < S.size(); ++i) {
        idx = max(idx, pos[S[i] - 'a']);
        if (idx == i) res.push_back(i - exchange(last_i, i + 1) + 1);
      }
      return res;
    }
    

# Complexity Analysis

Runtime: O(n). We analyze each character twice.  
Memory: O(1). We use 26 elements for the algorithm. Additional memory is used
to store the result; the algorithm does not need it.



