---
layout: post
title: 920. Uncommon Words from Two Sentences
---
### Question
We are given two sentences `A` and `B`.  (A _sentence_  is a string of space
separated words.  Each _word_ consists only of lowercase letters.)

A word is _uncommon_  if it appears exactly once in one of the sentences, and
does not appear in the other sentence.

Return a list of all uncommon words.

You may return the list in any order.



 **Example 1:**

    
    
     **Input:** A = "this apple is sweet", B = "this apple is sour"
    **Output:** ["sweet","sour"]
    

**Example 2:**

    
    
    **Input:** A = "apple apple", B = "banana"
    **Output:** ["banana"]
    



 **Note:**

  1. `0 <= A.length <= 200`
  2. `0 <= B.length <= 200`
  3. `A` and `B` both contain only spaces and lowercase letters.

### Solution 1
 **Intuition** :  
No matter how many sentences,  
 **uncommon word = words that appears only once**

I recall another similar problem:  
[819\. Most Common Word](https://leetcode.com/problems/most-common-
word/discuss/123854/)  
So I open my solutions there and copy some codes.

 **Explanation** :

Two steps:  
Count words occurrence to a HashMap<string, int> `count`.  
Loop on the hashmap, find words that appears only once.

 **C++:**

    
    
         vector<string> uncommonFromSentences(string A, string B) {
            unordered_map<string, int> count;
            istringstream iss(A + " " + B);
            while (iss >> A) count[A]++;
            vector<string> res;
            for (auto w: count)
                if (w.second == 1)
                    res.push_back(w.first);
            return res;
        }
    

**Java:**

    
    
         public String[] uncommonFromSentences(String A, String B) {
            Map<String, Integer> count = new HashMap<>();
            for (String w : (A + " " + B).split(" "))
                count.put(w, count.getOrDefault(w, 0) + 1);
            ArrayList<String> res = new ArrayList<>();
            for (String w : count.keySet())
                if (count.get(w) == 1)
                    res.add(w);
            return res.toArray(new String[0]);
        }
    

**Python:**

    
    
        def uncommonFromSentences( self, A, B):
            c = collections.Counter((A + " " + B).split())
            return [w for w in c if c[w] == 1]
    


### Solution 2
**Method 1:**

  1. Count the number of each String in A and B;
  2. Filter out those with occurrence more than 1.

    
    
        public String[] uncommonFromSentences(String A, String B) {
            Map<String, Integer> count = new HashMap<>();
            for (String s : (A + " " + B).split("\s")) { count.put(s, count.getOrDefault(s, 0) + 1); }
            return count.entrySet().stream().filter(e -> e.getValue() == 1).map(e -> e.getKey()).toArray(String[]::new);
        }
    

**Method 2:**

Use 2 HashSets to store distinct and common Strings.

  1. If encounter a String that is not in either Set, add it to distinct;
  2. Otherwise, remove it from the distinct and add it to the common one.

In other words:  
a) first check if the com Set contains the String s,  
if yes, go to b);  
if no, check if distinct Set can add s, if yes, no further operation; if no,
go to b).

b) remove s from distinct and add it the com Set.

    
    
        public String[] uncommonFromSentences(String A, String B) {
            Set<String> distinct = new HashSet<>(), com = new HashSet<>();
            for (String s : (A + " " + B).split("\s")) {
                if (com.contains(s) || !distinct.add(s)) { distinct.remove(s); com.add(s); }
            }
            return distinct.toArray(new String[0]);
        }
    


### Solution 3
Combine two strings separated with a string, istringstream and getline split
up the string so that we can count the occurrences in an unordered_map, then
simply list the entries with an occurrence of 1 in a vector.

    
    
    class Solution {
    public:
        vector<string> uncommonFromSentences(string A, string B) {
            // combine the two strings for simplicity and construct an istringstream
            istringstream combined(A + " " + B);
            
            // our word will be put in this variable by getline
            string word;
            
            // keep track of word occurrences
            unordered_map<string, int> counts;
            
            // get the next word and increase its count in our map
            while (getline(combined, word, ' '))
                counts[word] += 1;
            
            // put the words with an occurrence of 1 in our result vector
            vector<string> result;
            for(auto &p : counts) {
                if(p.second == 1)
                    result.push_back(p.first);
            }
            
            return result;
        }
    };
    



