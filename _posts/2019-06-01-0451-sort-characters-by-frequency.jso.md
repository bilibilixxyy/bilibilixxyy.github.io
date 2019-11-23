---
layout: post
title: 451. Sort Characters By Frequency
---
### Question
Given a string, sort it in decreasing order based on the frequency of
characters.

 **Example 1:**

    
    
     **Input:**
    "tree"
    
    **Output:**
    "eert"
    
    **Explanation:**
    'e' appears twice while 'r' and 't' both appear once.
    So 'e' must appear before both 'r' and 't'. Therefore "eetr" is also a valid answer.
    

**Example 2:**

    
    
    **Input:**
    "cccaaa"
    
    **Output:**
    "cccaaa"
    
    **Explanation:**
    Both 'c' and 'a' appear three times, so "aaaccc" is also a valid answer.
    Note that "cacaca" is incorrect, as the same characters must be together.
    

**Example 3:**

    
    
    **Input:**
    "Aabb"
    
    **Output:**
    "bbAa"
    
    **Explanation:**
    "bbaA" is also a valid answer, but "Aabb" is incorrect.
    Note that 'A' and 'a' are treated as two different characters.
    

### Solution 1
The logic is very similar to NO.347 and here we just use a map a count and
according to the frequency to put it into the right bucket. Then we go through
the bucket to get the most frequently character and append that to the final
stringbuilder.

    
    
    public class Solution {
        public String frequencySort(String s) {
            Map<Character, Integer> map = new HashMap<>();
            for (char c : s.toCharArray()) 
                map.put(c, map.getOrDefault(c, 0) + 1);
    						
            List<Character> [] bucket = new List[s.length() + 1];
            for (char key : map.keySet()) {
                int frequency = map.get(key);
                if (bucket[frequency] == null) bucket[frequency] = new ArrayList<>();
                bucket[frequency].add(key);
            }
    				
            StringBuilder sb = new StringBuilder();
            for (int pos = bucket.length - 1; pos >= 0; pos--)
                if (bucket[pos] != null)
                    for (char c : bucket[pos])
                        for (int i = 0; i < map.get(c); i++)
                            sb.append(c);
    
            return sb.toString();
        }
    }
    
    

And we have normal way using PriorityQueue as follows:  
according to user "orxanb", O(nlogm), m is the distinguish character, can be
O(1) since only 26 letters. So the overall time complexity should be O(n), the
same as the buck sort with less memory use.

    
    
    public class Solution {
        public String frequencySort(String s) {
            Map<Character, Integer> map = new HashMap<>();
            for (char c : s.toCharArray())
                map.put(c, map.getOrDefault(c, 0) + 1);
    						
            PriorityQueue<Map.Entry<Character, Integer>> pq = new PriorityQueue<>((a, b) -> b.getValue() - a.getValue());
            pq.addAll(map.entrySet());
    				
            StringBuilder sb = new StringBuilder();
            while (!pq.isEmpty()) {
                Map.Entry e = pq.poll();
                for (int i = 0; i < (int)e.getValue(); i++) 
                    sb.append(e.getKey());
            }
            return sb.toString();
        }
    }
    


### Solution 2
    
    
    class Solution {
    public:
        string frequencySort(string s) {
            unordered_map<char,int> freq;
            vector<string> bucket(s.size()+1, "");
            string res;
            
            //count frequency of each character
            for(char c:s) freq[c]++;
            //put character into frequency bucket
            for(auto& it:freq) {
                int n = it.second;
                char c = it.first;
                bucket[n].append(n, c);
            }
            //form descending sorted string
            for(int i=s.size(); i>0; i--) {
                if(!bucket[i].empty())
                    res.append(bucket[i]);
            }
            return res;
        }
    };
    


### Solution 3
  1. Build a map of characters to the number of times it occurs in the string
  2. Create an array where the index of the array represents how many times that character occurred in the String
  3. Iterate from the end of the array to the beginning, and at each index, append each character to the return string that number of times.

    
    
    public String frequencySort(String s) {
        if (s == null) {
            return null;
        }
        Map<Character, Integer> map = new HashMap();
        char[] charArray = s.toCharArray();
        int max = 0;
        for (Character c : charArray) {
            if (!map.containsKey(c)) {
                map.put(c, 0);
            }
            map.put(c, map.get(c) + 1);
            max = Math.max(max, map.get(c));
        }
    
        List<Character>[] array = buildArray(map, max);
    
        return buildString(array);
    }
    
    private List<Character>[] buildArray(Map<Character, Integer> map, int maxCount) {
        List<Character>[] array = new List[maxCount + 1];
        for (Character c : map.keySet()) {
            int count = map.get(c);
            if (array[count] == null) {
                array[count] = new ArrayList();
            }
            array[count].add(c);
        }
        return array;
    }
    
    private String buildString(List<Character>[] array) {
        StringBuilder sb = new StringBuilder();
        for (int i = array.length - 1; i > 0; i--) {
            List<Character> list = array[i];
            if (list != null) {
                for (Character c : list) {
                    for (int j = 0; j < i; j++) {
                        sb.append(c);
                    }
                }
            }
        }
        return sb.toString();
    }
    



