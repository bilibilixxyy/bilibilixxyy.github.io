---
layout: post
title: 822. Unique Morse Code Words
---
### Question
International Morse Code defines a standard encoding where each letter is
mapped to a series of dots and dashes, as follows: `"a"` maps to `".-"`, `"b"`
maps to `"-..."`, `"c"` maps to `"-.-."`, and so on.

For convenience, the full table for the 26 letters of the English alphabet is
given below:

    
    
    [".-","-...","-.-.","-..",".","..-.","--.","....","..",".---","-.-",".-..","--","-.","---",".--.","--.-",".-.","...","-","..-","...-",".--","-..-","-.--","--.."]

Now, given a list of words, each word can be written as a concatenation of the
Morse code of each letter. For example, "cba" can be written as "-.-..--...",
(which is the concatenation "-.-." + "-..." + ".-"). We'll call such a
concatenation, the transformation of a word.

Return the number of different transformations among all words we have.

    
    
    **Example:**
    **Input:** words = ["gin", "zen", "gig", "msg"]
    **Output:** 2
    **Explanation:**
    The transformation of each word is:
    "gin" - > "--...-."
    "zen" -> "--...-."
    "gig" -> "--...--."
    "msg" -> "--...--."
    
    There are 2 different transformations, "--...-." and "--...--.".
    

**Note:**

  * The length of `words` will be at most `100`.
  * Each `words[i]` will have length in range `[1, 12]`.
  * `words[i]` will only consist of lowercase letters.

### Solution 1
the description of the problem gives a example.  
"a" maps to ".-", "b" maps to "-...", "c" maps to "-.-.",  
"cab" can be written as "-.-.-....-", (which is the concatenation "-.-." +
"-..." + ".-").  
shouldn't "cab" be mapped to "-.-..--...", ("-.-."+".-"+"-...")?!!!!


### Solution 2
C++:

    
    
        int uniqueMorseRepresentations(vector<string>& words) {
            vector<string> d = {".-", "-...", "-.-.", "-..", ".", "..-.", "--.", "....", "..", ".---", "-.-", ".-..", "--", "-.", "---", ".--.", "--.-", ".-.", "...", "-", "..-", "...-", ".--", "-..-", "-.--", "--.."};
            unordered_set<string> s;
            for (auto word : words) {
                string code;
                for (auto c : word) code += d[c - 'a'];
                s.insert(code);
            }
            return s.size();
        }
    

Java:

    
    
        public int uniqueMorseRepresentations(String[] words) {
            String[] d = {".-", "-...", "-.-.", "-..", ".", "..-.", "--.", "....", "..", ".---", "-.-", ".-..", "--", "-.", "---", ".--.", "--.-", ".-.", "...", "-", "..-", "...-", ".--", "-..-", "-.--", "--.."};
            HashSet<String> s = new HashSet<>();
            for (String word : words) {
                String code = "";
                for (char c : word.toCharArray()) code += d[c - 'a'];
                s.add(code);
            }
            return s.size();
        }
    

Python:

    
    
        def uniqueMorseRepresentations(self, words):
            d = [".-", "-...", "-.-.", "-..", ".", "..-.", "--.", "....", "..", ".---", "-.-", ".-..", "--",
                 "-.", "---", ".--.", "--.-", ".-.", "...", "-", "..-", "...-", ".--", "-..-", "-.--", "--.."]
            return len({''.join(d[ord(i) - ord('a')] for i in w) for w in words})


### Solution 3
    
    
    class Solution(object):
        def uniqueMorseRepresentations(self, words):
            """
            :type words: List[str]
            :rtype: int
            """
            map_=[".-","-...","-.-.","-..",".","..-.","--.","....","..",".---","-.-",".-..","--","-.","---",".--.","--.-",".-.","...","-","..-","...-",".--","-..-","-.--","--.."]
            
            res=set()
            
            for word in words:
                val=""
                for s in word:
                    val+=map_[ord(s)-ord('a')]
                res.add(val)
            
            return len(res)
    



