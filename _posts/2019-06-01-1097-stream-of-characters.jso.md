---
layout: post
title: 1097. Stream of Characters
---
### Question
Implement the `StreamChecker` class as follows:

  * `StreamChecker(words)`: Constructor, init the data structure with the given words.
  * `query(letter)`: returns true if and only if for some `k >= 1`, the last `k` characters queried (in order from oldest to newest, including this letter just queried) spell one of the words in the given list.



 **Example:**

    
    
    StreamChecker streamChecker = new StreamChecker(["cd","f","kl"]); // init the dictionary.
    streamChecker.query('a');          // return false
    streamChecker.query('b');          // return false
    streamChecker.query('c');          // return false
    streamChecker.query('d');          // return true, because 'cd' is in the wordlist
    streamChecker.query('e');          // return false
    streamChecker.query('f');          // return true, because 'f' is in the wordlist
    streamChecker.query('g');          // return false
    streamChecker.query('h');          // return false
    streamChecker.query('i');          // return false
    streamChecker.query('j');          // return false
    streamChecker.query('k');          // return false
    streamChecker.query('l');          // return true, because 'kl' is in the wordlist
    



 **Note:**

  * `1 <= words.length <= 2000`
  * `1 <= words[i].length <= 2000`
  * Words will only consist of lowercase English letters.
  * Queries will only consist of lowercase English letters.
  * The number of queries is at most 40000.

### Solution 1
<https://www.youtube.com/watch?v=sv028Ula0Ng>


### Solution 2
 **We say**  
`W = max(words.length)`,the maximum length of all words.  
`N = words.size`, the number of words  
`Q`, the number of calls of function `query`  
  

##  **Solution 1: Check all words (TLE)**

If we save the whole input character stream and compare with `words` one by
one,  
The time complexity for each `query` will be `O(NW)`,  
depending on the size of `words`.  
  

##  **Solution 2: Check Query Suffixes (Maybe AC, Maybe TLE)**

While the `words.size` can be really big,  
the number of the suffixes of query stream is bounded.

For example, if the query stream is `"abcd"`,  
the suffix can be `"abcd"`, `"bcd"`, `"cd"`, `"d"`.

We can save all hashed words to a set.  
For each query, we check query stream's all suffixes.  
The maximum length of words is `W`, we need to check `W` suffixes.

The time complexity for each `query` will be `O(W)` if we take the set search
as `O(1)`.  
The overall time is `O(WQ)`.  
  

##  **Solution 3: Trie (Accepted)**

Only a part of suffixes can be the prefix of a word,  
waiting for characters coming to form a complete word.  
Instead of checking all `W` suffixes in each query,  
we can just save those possible waiting prefixes in a `waiting` list.

 **Explanation** :  
Initialization:

  1. Construct a trie
  2. declare a global waiting list.

Query:

  1. for each node in the `waiting` list,  
check if there is child node for the new character.  
If so, add it to the new waiting list.

  2. return true if any node in the `waiting`list is the end of a word.

 **Time Complexity** :  
`waiting.size <= W`, where `W` is the maximum length of words.  
So that `O(query) = O(waiting.size) = O(W)`  
We will make `Q` queries, the overall time complexity is `O(QW)`

Note that it has same complexity in the worst case as solution 2 (like
"aaaaaaaa" for words and query),  
In general cases, it saves time checking all suffixes, and also the set search
in a big set.  
  

 **Space Complexity** :

`waiting.size <= W`, where `W` is the maximum length of words.  
`waiting` list will take `O(W)`

Assume we have initially `N` words, at most `N` leaves in the `trie`.  
The size of trie is `O(NW)`.  
  

 **Python:**

    
    
     class StreamChecker(object):
    
        def __init__(self, words):
            T = lambda: collections.defaultdict(T)
            self.trie = T()
            for w in words: reduce(dict.__getitem__, w, self.trie)['#'] = True
            self.waiting = []
    
        def query(self, letter):
            self.waiting = [node[letter] for node in self.waiting + [self.trie] if letter in node]
            return any("#" in node for node in self.waiting)
    

## Solution 4: Construct Trie with Reversed Words

Time: 600 ~ 700ms  
Time complexity: O(WQ)

    
    
        def __init__(self, words):
            T = lambda: collections.defaultdict(T)
            self.trie = T()
            for w in words: reduce(dict.__getitem__, w[::-1], self.trie)['#'] = True
            self.S = ""
            self.W = max(map(len, words))
    
        def query(self, letter):
            self.S = (letter + self.S)[:self.W]
            cur = self.trie
            for c in self.S:
                if c in cur:
                    cur = cur[c]
                    if cur['#'] == True:
                        return True
                else:
                    break
            return False
    


### Solution 3
    
    
    class StreamChecker:
    
        def __init__(self, words: List[str]):
            self.s = ''
            self.dic = collections.defaultdict(set)
            for w in words:
                self.dic[w[-1]].add(w)
                    
    
        def query(self, letter: str) -> bool:
            self.s += letter
            return any(self.s.endswith(w) for w in self.dic[letter])
    



