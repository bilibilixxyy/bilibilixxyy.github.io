---
layout: post
title: 869. Similar String Groups
---
### Question
Two strings `X` and `Y` are similar if we can swap two letters (in different
positions) of `X`, so that it equals `Y`.

For example, `"tars"` and `"rats"` are similar (swapping at positions `0` and
`2`), and `"rats"` and `"arts"` are similar, but `"star"` is not similar to
`"tars"`, `"rats"`, or `"arts"`.

Together, these form two connected groups by similarity: `{"tars", "rats",
"arts"}` and `{"star"}`.  Notice that `"tars"` and `"arts"` are in the same
group even though they are not similar.  Formally, each group is such that a
word is in the group if and only if it is similar to at least one other word
in the group.

We are given a list `A` of strings.  Every string in `A` is an anagram of
every other string in `A`.  How many groups are there?

 **Example 1:**

    
    
     **Input:** ["tars","rats","arts","star"]
    **Output:** 2

 **Note:**

  1. `A.length <= 2000`
  2. `A[i].length <= 1000`
  3. `A.length * A[i].length <= 20000`
  4. All words in `A` consist of lowercase letters only.
  5. All words in `A` have the same length and are anagrams of each other.
  6. The judging time limit has been increased for this question.

### Solution 1
    
    
    bool similar(string &a, string &b) {
        int n =  0;
        for (int i = 0; i < a.size(); i++)
            if (a[i] != b[i] && ++n > 2)
                return false;
        return true;
    }
    
    int numSimilarGroups(vector<string>& A) {
        disjoint_set ds(A.size());
        for (int i = 0; i < A.size(); i++)
            for (int j = i + 1; j < A.size(); j++)
                if (similar(A[i], A[j]))
                    ds.join(i, j);
        return ds.size();
    }
    

The standard disjoint_set class is defined below:

    
    
    class disjoint_set {
        vector<int> v;
        int sz;
    public:
        disjoint_set(int n) {
            makeset(n);
        }
    
        void makeset(int n) {
            v.resize(n);
            iota(v.begin(), v.end(), 0);
            sz = n;
        }
    
        int find(int i) {
            if (i != v[i])
                v[i] = find(v[i]);
            return v[i];
        }
        
        void join(int i, int j) {
            int ri = find(i), rj = find(j);
            if (ri != rj) {
                v[ri] = rj;
                sz--;
            }
        }
        
        int size() {
            return sz;
        }
    };
    


### Solution 2
    
    
    class Solution {
        public int numSimilarGroups(String[] A) {
            if(A.length < 2) return A.length;
            int res = 0;
            for(int i=0;i<A.length;i++){
                if(A[i] == null) continue;
                String str = A[i];
                A[i] = null;
                res++;
                dfs(A,str);
            }
            return res;
        }
        public void dfs(String[] arr,String str){
            for(int i=0;i<arr.length;i++){
                if(arr[i] == null) continue;
                if(helper(str,arr[i])){// both string str and arr[i] belong in same group
                    String s = arr[i];
                    arr[i] = null;
                    dfs(arr,s);
                }
            }
        }
        public boolean helper(String s,String t){
            int res = 0, i = 0;
            while(res <= 2 && i < s.length()){
                if(s.charAt(i) != t.charAt(i)) res++;
                i++;
            }
            return res == 2;
        }
    }
    


### Solution 3
    
    
    class Solution {
        public int numSimilarGroups(String[] A) {
            if( A == null || A.length == 0) {
                return 0;
            }
            
            boolean[] visited = new boolean[A.length];
            Queue<String> queue = new LinkedList<>();
            
            int ans = 0;
            for (int i = 0; i < A.length; i++) {
                if(visited[i]) {
                    continue;
                }
                
                visited[i] = true;
                ans++;
                queue.add(A[i]);
                while(!queue.isEmpty()) {
                    String sameGroup = queue.poll();
                    for (int j = 0; j < A.length; j++) {
                        if(visited[j]) {
                            continue;
                        }
                        int diff = 0;
                        for (int l = 0; l < A[j].length(); l++) {
                            if(sameGroup.charAt(l) != A[j].charAt(l)) {
                                diff++;
                            }
                        }
                        if( diff == 2) {
                            visited[j] = true;
                            queue.add(A[j]);
                        }
                    }
                }
                
            }
            return ans;
        }
    }
    



