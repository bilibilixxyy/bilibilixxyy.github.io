---
layout: post
title: 49. Group Anagrams
---
### Question
Given an array of strings, group anagrams together.

 **Example:**

    
    
     **Input:** ["eat", "tea", "tan", "ate", "nat", "bat"],
    **Output:**
    [
      ["ate","eat","tea"],
      ["nat","tan"],
      ["bat"]
    ]

 **Note:**

  * All inputs will be in lowercase.
  * The order of your output does not matter.

### Solution 1
    
    
    public class Solution {
        public List<List<String>> groupAnagrams(String[] strs) {
            if (strs == null || strs.length == 0) return new ArrayList<List<String>>();
            Map<String, List<String>> map = new HashMap<String, List<String>>();
            for (String s : strs) {
                char[] ca = s.toCharArray();
                Arrays.sort(ca);
                String keyStr = String.valueOf(ca);
                if (!map.containsKey(keyStr)) map.put(keyStr, new ArrayList<String>());
                map.get(keyStr).add(s);
            }
            return new ArrayList<List<String>>(map.values());
        }
    }


### Solution 2
    
    
    public static List<List<String>> groupAnagrams(String[] strs) { 
       int[] prime = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101, 103};//最多10609个z
        
                List<List<String>> res = new ArrayList<>();
                HashMap<Integer, Integer> map = new HashMap<>();
                for (String s : strs) {
                    int key = 1;
                    for (char c : s.toCharArray()) {
                        key *= prime[c - 'a'];
                    }
                    List<String> t;
                    if (map.containsKey(key)) {
                        t = res.get(map.get(key));
                    } else {
                        t = new ArrayList<>();
                        res.add(t);
                        map.put(key, res.size() - 1);
                    }
                    t.add(s);
                }
                return res;
        }


### Solution 3
Use an `unordered_map` to group the strings by their sorted counterparts. Use
the sorted string as the key and all anagram strings as the value.

    
    
    class Solution {
    public:
        vector<vector<string>> groupAnagrams(vector<string>& strs) {
            unordered_map<string, vector<string>> mp;
            for (string s : strs) {
                string t = s; 
                sort(t.begin(), t.end());
                mp[t].push_back(s);
            }
            vector<vector<string>> anagrams;
            for (auto p : mp) { 
                anagrams.push_back(p.second);
            }
            return anagrams;
        }
    };
    

Moreover, since the string only contains lower-case alphabets, we can sort
them using counting sort to improve the time complexity.

    
    
    class Solution {
    public:
        vector<vector<string>> groupAnagrams(vector<string>& strs) {
            unordered_map<string, vector<string>> mp;
            for (string s : strs) {
                mp[strSort(s)].push_back(s);
            }
            vector<vector<string>> anagrams;
            for (auto p : mp) { 
                anagrams.push_back(p.second);
            }
            return anagrams;
        }
    private:
        string strSort(string s) {
            int counter[26] = {0};
            for (char c : s) {
                counter[c - 'a']++;
            }
            string t;
            for (int c = 0; c < 26; c++) {
                t += string(counter[c], c + 'a');
            }
            return t;
        }
    };
    



