---
layout: post
title: 837. Most Common Word
---
### Question
Given a paragraph and a list of banned words, return the most frequent word
that is not in the list of banned words.  It is guaranteed there is at least
one word that isn't banned, and that the answer is unique.

Words in the list of banned words are given in lowercase, and free of
punctuation.  Words in the paragraph are not case sensitive.  The answer is in
lowercase.



 **Example:**

    
    
     **Input:** 
    paragraph = "Bob hit a ball, the hit BALL flew far after it was hit."
    banned = ["hit"]
    **Output:** "ball"
    **Explanation:** 
    "hit" occurs 3 times, but it is a banned word.
    "ball" occurs twice (and no other word does), so it is the most frequent non-banned word in the paragraph. 
    Note that words in the paragraph are not case sensitive,
    that punctuation is ignored (even if adjacent to words, such as "ball,"), 
    and that "hit" isn't the answer even though it occurs more because it is banned.
    



 **Note:**

  * `1 <= paragraph.length <= 1000`.
  * `0 <= banned.length <= 100`.
  * `1 <= banned[i].length <= 10`.
  * The answer is unique, and written in lowercase (even if its occurrences in `paragraph` may have uppercase symbols, and even if it is a proper noun.)
  * `paragraph` only consists of letters, spaces, or the punctuation symbols `!?',;.`
  * There are no hyphens or hyphenated words.
  * Words only consist of letters, never apostrophes or other punctuation symbols.

### Solution 1
The description doesn't clearify the case like "word,word,word".  
And it doesn't have this kind of corner cases at first.  
The test cases and OJ may not be perfect on this problem.

Anyway I didn't bother thinking this issue.  
In my opinion, the problem demands to extract words with only letters.  
All I did is just extract words with only letters.

4 steps:

  1. remove all punctuations
  2. change to lowercase
  3. words count for each word not in banned set
  4. return the most common word

 **C++:**

    
    
         string mostCommonWord(string p, vector<string>& banned) {
            unordered_set<string> ban(banned.begin(), banned.end());
            unordered_map<string, int> count;
            for (auto & c: p) c = isalpha(c) ? tolower(c) : ' ';
            istringstream iss(p);
            string w;
            pair<string, int> res ("", 0);
            while (iss >> w)
                if (ban.find(w) == ban.end() && ++count[w] > res.second)
                    res = make_pair(w, count[w]);
            return res.first;
        }
    

**Java:**

    
    
        public String most CommonWord(String p, String[] banned) {
            Set<String> ban = new HashSet<>(Arrays.asList(banned));
            Map<String, Integer> count = new HashMap<>();
            String[] words = p.replaceAll("\W+" , " ").toLowerCase().split("\s+");
            for (String w : words) if (!ban.contains(w)) count.put(w, count.getOrDefault(w, 0) + 1);
            return Collections.max(count.entrySet(), Map.Entry.comparingByValue()).getKey();
        }
    

**Python:**  
Thanks to @sirxudi I change one line from  
`words = re.sub(r'[^a-zA-Z]', ' ', p).lower().split()`  
to  
`words = re.findall(r'\w+', p.lower())`

    
    
         def mostCommonWord(self, p, banned):
            ban = set(banned)
            words = re.findall(r'\w+', p.lower())
            return collections.Counter(w for w in words if w not in ban).most_common(1)[0][0]
    


### Solution 2
Input:  
"a, a, a, a, b,b,b,c, c"  
["a"]  
Output:  
"b"  
Expected:  
"b,b,b,c"

That's not what the original description says。


### Solution 3
    
    
        public String mostCommonWord(String paragraph, String[] banned) {
            String[] words = paragraph.toLowerCase().split("[ !?',;.]+");
            HashMap<String, Integer> map = new HashMap<>();
            for(String word : words) map.put(word, map.getOrDefault(word, 0) + 1);
            for(String word : banned) if(map.containsKey(word)) map.remove(word);
            String res = null;
            for(String word : map.keySet())
                if(res == null || map.get(word) > map.get(res))
                    res = word;
            return res;
        }
    



