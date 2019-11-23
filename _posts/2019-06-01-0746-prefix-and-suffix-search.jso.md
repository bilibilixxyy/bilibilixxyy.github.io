---
layout: post
title: 746. Prefix and Suffix Search
---
### Question
Given many `words`, `words[i]` has weight `i`.

Design a class `WordFilter` that supports one function, `WordFilter.f(String
prefix, String suffix)`. It will return the word with given `prefix` and
`suffix` with maximum weight. If no word exists, return -1.

 **Examples:**

    
    
     **Input:**
    WordFilter(["apple"])
    WordFilter.f("a", "e") // returns 0
    WordFilter.f("b", "") // returns -1
    



 **Note:**

  1. `words` has length in range `[1, 15000]`.
  2. For each test case, up to `words.length` queries `WordFilter.f` may be made.
  3. `words[i]` has length in range `[1, 10]`.
  4. `prefix, suffix` have lengths in range `[0, 10]`.
  5. `words[i]` and `prefix, suffix` queries consist of lowercase letters only.

### Solution 1
Before solving this problem, we need to know which operation is called the
most.

If `f()` is more frequently than `WordFilter()`, use method 1.  
If **space complexity** is concerned, use method 2.  
If the input string array changes frequently, i.e., `WordFilter()` is more
frequently than `f()`, use method 3.

 **< Method 1  >**  
WordFilter: Time = O(NL^2)  
f: Time = O(1)  
Space = O(NL^2)  
Note: N is the size of input array and L is the max length of input strings.

    
    
    class WordFilter {
        HashMap<String, Integer> map = new HashMap<>();
    
        public WordFilter(String[] words) {
            for(int w = 0; w < words.length; w++){
                for(int i = 0; i <= 10 && i <= words[w].length(); i++){
                    for(int j = 0; j <= 10 && j <= words[w].length(); j++){
                        map.put(words[w].substring(0, i) + "#" + words[w].substring(words[w].length()-j), w);
                    }
                }
            }
        }
    
        public int f(String prefix, String suffix) {
            return (map.containsKey(prefix + "#" + suffix))? map.get(prefix + "#" + suffix) : -1;
        }
    }
    
    

**< Method 2  >**  
WordFilter: Time = O(NL)  
f: Time = O(N)  
Space = O(NL)

    
    
    class WordFilter {
        HashMap<String, List<Integer>> mapPrefix = new HashMap<>();
        HashMap<String, List<Integer>> mapSuffix = new HashMap<>();
        
        public WordFilter(String[] words) {
            
            for(int w = 0; w < words.length; w++){
                for(int i = 0; i <= 10 && i <= words[w].length(); i++){
                    String s = words[w].substring(0, i);
                    if(!mapPrefix.containsKey(s)) mapPrefix.put(s, new ArrayList<>());
                    mapPrefix.get(s).add(w);
                }
                for(int i = 0; i <= 10 && i <= words[w].length(); i++){
                    String s = words[w].substring(words[w].length() - i);
                    if(!mapSuffix.containsKey(s)) mapSuffix.put(s, new ArrayList<>());
                    mapSuffix.get(s).add(w);
                }
            }
    
        public int f(String prefix, String suffix) {
            
            if(!mapPrefix.containsKey(prefix) || !mapSuffix.containsKey(suffix)) return -1;
            List<Integer> p = mapPrefix.get(prefix);
            List<Integer> s = mapSuffix.get(suffix);
            int i = p.size()-1, j = s.size()-1;
            while(i >= 0 && j >= 0){
                if(p.get(i) < s.get(j)) j--;
                else if(p.get(i) > s.get(j)) i--;
                else return p.get(i);
            }
            return -1;
    

**< Method 3  >**  
WordFilter: Time = O(1)  
f: Time = O(NL)  
Space = O(1)

    
    
    class WordFilter {
        String[] input;
        public WordFilter(String[] words) {
            input = words;
        }
        public int f(String prefix, String suffix) {
            for(int i = input.length-1; i >= 0; i--){
                if(input[i].startsWith(prefix) && input[i].endsWith(suffix)) return i;
            }
            return -1;
        }
    }
    
    

Note: The format of input data:  
**First line** : `["WordFilter", "f", ..., "f"]`, depends on how many time
`f()` is called.  
**Second line** : First item is the input string array, following by the pairs
of `[prefix, suffix]`.  
ex:

    
    
    ["WordFilter","f", "f"]
    [[["apple"]],["a","e"], ["ap","ple"]]
    


### Solution 2
Take "apple" as an example, we will insert add "apple{apple", "pple{apple",
"ple{apple", "le{apple", "e{apple", "{apple" into the Trie Tree.  
If the query is: prefix = "app", suffix = "le", we can find it by querying our
trie for  
"le { app".  
We use '{' because in ASCii Table, '{' is next to 'z', so we just need to
create new TrieNode[27] instead of 26. Also, compared with tradition Trie, we
add the attribute weight in class TrieNode.

    
    
    class TrieNode {
        TrieNode[] children;
        int weight;
        public TrieNode() {
            children = new TrieNode[27]; // 'a' - 'z' and '{'. 'z' and '{' are neighbours in ASCII table
            weight = 0;
        }
    }
    
    public class WordFilter {
        TrieNode root;
        public WordFilter(String[] words) {
            root = new TrieNode();
            for (int weight = 0; weight < words.length; weight++) {
                String word = words[weight] + "{";
                for (int i = 0; i < word.length(); i++) {
                    TrieNode cur = root;
                    cur.weight = weight;
        // add "apple{apple", "pple{apple", "ple{apple", "le{apple", "e{apple", "{apple" into the Trie Tree
                    for (int j = i; j < 2 * word.length() - 1; j++) {
                        int k = word.charAt(j % word.length()) - 'a';
                        if (cur.children[k] == null)
                            cur.children[k] = new TrieNode();
                        cur = cur.children[k];
                        cur.weight = weight;
                    }
                }
            }
        }
        public int f(String prefix, String suffix) {
            TrieNode cur = root;
            for (char c: (suffix + '{' + prefix).toCharArray()) {
                if (cur.children[c - 'a'] == null) {
                    return -1;
                }
                cur = cur.children[c - 'a'];
            }
            return cur.weight;
        }
    }
    


### Solution 3
_By Yangshun_

#### Method 1

Store all the words corresponding to its prefixes and suffixes. For example,
for two words `bat` and `bar`, the `prefixes` and `suffixes` dictionary will
look as such:

    
    
    # prefixes
    {
      '': {'bat', 'bar'},
      'b': {'bat', 'bar'},
      'ba': {'bat', 'bar'},
      'bar': {'bar'},
      'bat': {'bat'},
    }
    
    # suffixes
    {
      't': {'bat'},
      'at': {'bat'},
      'bat': {'bat'},
      'r': {'bar'},
      'ar': {'bar'},
      'bar': {'bar'},
    }
    

`f('b', 'at')` => set intersection of `{'bat', 'bar'}` and `{'bat'}` =>
`'bat'`.

You can use a Trie to make it more space efficient as well.

    
    
    class WordFilter(object):
    
        def __init__(self, words):
            from collections import defaultdict
            self.prefixes = defaultdict(set)
            self.suffixes = defaultdict(set)
            self.weights = {}
            for index, word in enumerate(words):
                prefix, suffix = '', ''
                for char in [''] + list(word):
                    prefix += char
                    self.prefixes[prefix].add(word)
                for char in [''] + list(word[::-1]):
                    suffix += char
                    self.suffixes[suffix[::-1]].add(word)
                self.weights[word] = index
    
        def f(self, prefix, suffix):
            weight = -1
            for word in self.prefixes[prefix] & self.suffixes[suffix]:
                if self.weights[word] > weight:
                    weight = self.weights[word]
            return weight
    

#### Method 2

Directly save the prefix and suffix combinations for a word, where the value
is the weight. For a word such as `'bat'`, store all the prefix + suffix
combinations in a dictionary, delimited by a non-alphabet character such as
`'.'`. The delimiter is important so as to distinguish between prefix/suffix
pairs that would have been concatenated to give the same result if without -
`ab` \+ `c` and `a` \+ `bc` would both give `abc` if there wasn't a delimiter
present.

    
    
    {
      '.': 0,
      '.t': 0,
      '.at': 0,
      '.bat': 0,
      'b.': 0,
      'b.t': 0,
      'b.at': 0,
      'b.bat': 0,
      'ba.': 0,
      'ba.t': 0,
      'ba.at': 0,
      'ba.bat': 0,
      'bat.': 0,
      'bat.t': 0,
      'bat.at': 0,
      'bat.bat': 0,
    }
    

Later occurrences of the prefix + suffix combi will have its weight
overwritten, so we can simply look up the dictionary and return the answer.

    
    
    class WordFilter(object):
    
        def __init__(self, words):
            self.inputs = {}
            for index, word in enumerate(words):
                prefix = ''
                for char in [''] + list(word):
                    prefix += char
                    suffix = ''
                    for char in [''] + list(word[::-1]):
                        suffix += char
                        self.inputs[prefix + '.' + suffix[::-1]] = index
    
        def f(self, prefix, suffix):
            return self.inputs.get(prefix + '.' + suffix, -1)
    



