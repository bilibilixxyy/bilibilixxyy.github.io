---
layout: post
title: 808. Number of Matching Subsequences
---
### Question
Given string `S` and a dictionary of words `words`, find the number of
`words[i]` that is a subsequence of `S`.

    
    
     **Example :**
    **Input:** 
    S = "abcde"
    words = ["a", "bb", "acd", "ace"]
    **Output:** 3
    **Explanation:** There are three words in words that are a subsequence of S: "a", "acd", "ace".
    

**Note:**

  * All words in `words` and `S` will only consists of lowercase letters.
  * The length of `S` will be in the range of `[1, 50000]`.
  * The length of `words` will be in the range of `[1, 5000]`.
  * The length of `words[i]` will be in the range of `[1, 50]`.

### Solution 1
 **Runtime** is linear in the total size of the input (`S` and all of
`words`).  
 **Explanation** below the code.

## Solutions:

Variations of the same algorithm described at the end.

### Python:

Using iterators to represent words and their progress:

    
    
    def numMatchingSubseq(self, S, words):
        waiting = collections.defaultdict(list)
        for w in words:
            waiting[w[0]].append(iter(w[1:]))
        for c in S:
            for it in waiting.pop(c, ()):
                waiting[next(it, None)].append(it)
        return len(waiting[None])
    

A version with cleaner initialization, using iterators iterating the whole
word instead of a copy of the word after its first letter:

    
    
    def numMatchingSubseq(self, S, words):
        waiting = collections.defaultdict(list)
        for it in map(iter, words):
            waiting[next(it)].append(it)
        for c in S:
            for it in waiting.pop(c, ()):
                waiting[next(it, None)].append(it)
        return len(waiting[None])
    

Another version, make all words waiting for a space character and prepend a
space character to S:

    
    
    def numMatchingSubseq(self, S, words):
        waiting = collections.defaultdict(list, {' ': map(iter, words)})
        for c in ' ' + S:
            for it in waiting.pop(c, ()):
                waiting[next(it, None)].append(it)
        return len(waiting[None])
    

This avoids some code duplication and now the solution would also handle empty
words if they were allowed (the problem says all words are non-empty).

### Ruby:

Using (word,index) pairs to keep track of words and their progress:

    
    
    def num_matching_subseq(s, words)
      waiting = Hash.new { |h, k| h[k] = [] }
      words.each { |w| waiting[w[0]] << [w, 1] }
      s.each_char { |c|
        waiting.delete(c)&.each { |w, i| waiting[w[i]] << [w, i+1] }
      }
      waiting[nil].size
    end
    

### Go:

Just keep slicing off letters from the front of the strings.

    
    
    func numMatchingSubseq(S string, words []string) (num int) {
        waiting := map[rune][]string{' ': words}
        for _, c := range " " + S {
            advance := waiting[c]
            delete(waiting, c)
            for _, word := range advance {
                if len(word) == 0 {
                    num++
                } else {
                    c := rune(word[0])
                    waiting[c] = append(waiting[c], word[1:])
                }
            }
        }
        return
    }
    

### C++:

Using char pointers to keep track of the waiting suffixes (thanks to @mzchen
for help, see our conversation for more):

    
    
    int numMatchingSubseq(string S, vector<string>& words) {
        vector<const char*> waiting[128];
        for (auto &w : words)
            waiting[w[0]].push_back(w.c_str());
        for (char c : S) {
            auto advance = waiting[c];
            waiting[c].clear();
            for (auto it : advance)
                waiting[*++it].push_back(it);
        }
        return waiting[0].size();
    }
    

Using index pairs (i,j) meaning word i is waiting for its letter j:

    
    
    int numMatchingSubseq(string S, vector<string>& words) {
        vector<pair<int, int>> waiting[128];
        for (int i = 0; i < words.size(); i++)
            waiting[words[i][0]].emplace_back(i, 1);
        for (char c : S) {
            auto advance = waiting[c];
            waiting[c].clear();
            for (auto it : advance)
                waiting[words[it.first][it.second++]].push_back(it);
        }
        return waiting[0].size();
    }
    

### Java:

Using `StringCharacterIterator` (requires `import
java.text.StringCharacterIterator;`):

    
    
    public int numMatchingSubseq(String S, String[] words) {
        List<StringCharacterIterator>[] waiting = new List[128];
        for (int c = 0; c <= 'z'; c++)
            waiting[c] = new ArrayList();
        for (String w : words)
            waiting[w.charAt(0)].add(new StringCharacterIterator(w));
        for (char c : S.toCharArray()) {
            List<StringCharacterIterator> advance = waiting[c];
            waiting[c] = new ArrayList();
            for (StringCharacterIterator it : advance)
                waiting[it.next() % it.DONE].add(it);
        }
        return waiting[0].size();
    }
    

Using index pairs (i,j) meaning word i is waiting for its letter j:

    
    
    public int numMatchingSubseq(String S, String[] words) {
        List<Integer[]>[] waiting = new List[128];
        for (int c = 0; c <= 'z'; c++)
            waiting[c] = new ArrayList();
        for (int i = 0; i < words.length; i++)
            waiting[words[i].charAt(0)].add(new Integer[]{i, 1});
        for (char c : S.toCharArray()) {
            List<Integer[]> advance = new ArrayList(waiting[c]);
            waiting[c].clear();
            for (Integer[] a : advance)
                waiting[a[1] < words[a[0]].length() ? words[a[0]].charAt(a[1]++) : 0].add(a);
        }
        return waiting[0].size();
    }
    

### C:

In C it's easy to cut off the first letter: Just add 1 to the pointer. For the
waiting lists I use arrays, the waiting list for character c is `waiting[c]`
and has length `wn[c]`.

    
    
    char* waiting[128][5000];
    
    int numMatchingSubseq(char* S, char** words, int wordsSize) {
        int wn[128] = {};
        while (wordsSize--) {
            char* word = *words++;
            waiting[*word][wn[*word]++] = word + 1;
        }
        while (*S) {
            char c = *S++;
            int n = wn[c];
            wn[c] = 0;
            for (int i=0; i<n; i++) {
                char* it = waiting[c][i];
                waiting[*it][wn[*it]++] = it + 1;
            }
        }
        return wn[0];
    }
    

  

## Explanation:

I go through S once, and while I'm doing that, I move through all words
accordingly. That is, I keep track of how much of each word I've already seen,
and with each letter of S, I advance the words waiting for that letter. To
quickly find the words waiting for a certain letter, I store each word (and
its progress) in a list of words waiting for that letter. Then for each of the
lucky words whose current letter just occurred in S, I update their progress
and store them in the list for their next letter.

Let's go through the given example:

    
    
    S = "abcde"
    words = ["a", "bb", "acd", "ace"]
    

I store that `"a"`, `"acd"` and `"ace"` are waiting for an `'a'` and `"bb"` is
waiting for a `'b'` (using parentheses to show how far I am in each word):

    
    
    'a':  ["(a)", "(a)cd", "(a)ce"]
    'b':  ["(b)b"]
    

Then I go through S. First I see `'a'`, so I take the list of words waiting
for `'a'` and store them as waiting under their next letter:

    
    
    'b':  ["(b)b"]
    'c':  ["a(c)d", "a(c)e"]
    None: ["a"]
    

You see `"a"` is already waiting for nothing anymore, while `"acd"` and
`"ace"` are now waiting for `'c'`. Next I see `'b'` and update accordingly:

    
    
    'b':  ["b(b)"]
    'c':  ["a(c)d", "a(c)e"]
    None: ["a"]
    

Then `'c'`:

    
    
    'b':  ["b(b)"]
    'd':  ["ac(d)"]
    'e':  ["ac(e)"]
    None: ["a"]
    

Then `'d'`:

    
    
    'b':  ["b(b)"]
    'e':  ["ac(e)"]
    None: ["a", "acd"]
    

Then `'e'`:

    
    
    'b':  ["b(b)"]
    None: ["a", "acd", "ace"]
    

And now I just return how many words aren't waiting for anything anymore.


### Solution 2
    
    
    class Solution {
        public int numMatchingSubseq(String S, String[] words) {
            Map<Character, Deque<String>> map = new HashMap<>();
            for (char c = 'a'; c <= 'z'; c++) {
                map.putIfAbsent(c, new LinkedList<String>());
            }
            for (String word : words) {
                map.get(word.charAt(0)).addLast(word);
            }
    
            int count = 0;
            for (char c : S.toCharArray()) {
                Deque<String> queue = map.get(c);
                int size = queue.size();
                for (int i = 0; i < size; i++) {
                    String word = queue.removeFirst();
                    if (word.length() == 1) {
                        count++;
                    } else {
                        map.get(word.charAt(1)).addLast(word.substring(1));
                    }
                }
            }
            return count;
        }
    }
    

substring() is time consuming and you could also use an array as a map to
further improve this solution.


### Solution 3
The idea is to use the String S and build a dictionary of indexes for each
character in the string.  
Then for each word, we can go character by character and see if it is a
subsequence, by using the corresponding index dictionary, but just making sure
that the index of the current character occurs after the index where the
previous character was seen. To speed up the processing, we should use binary
search in the index dictionary.

As an example for S = "aacbacbde"  
the  
dict_idxs =  
{a: [0, 1, 4]  
b: [3, 6]  
c: [2, 5]  
d: [7]  
e: [8]  
}  
Now for the word say "abcb", starting with d_i = 0,  
a => get list for a in the dict_idxs which is [0, 1, 4], and in the list, find
the index of a >= d_i which is 0. After this update d_i to +1 => 1  
b => get list for b in the dict_idxs [3, 6], and in the list, find the index
of b >= d_i => >= 1, which is at index 0 => 3, after this update d_i to 4+1 =>
4  
c => in the list for c, [3, 5], find the index of c >= 4, which is 5. update
d_i to 5+1 = 6  
b => in the list for b, [3, 6], fund the index of b >= 6, which is 6, update
d_i to 7, since this is the end of the word, and we have found all characters
in the word in the index dictionary, we return True for this word.

    
    
        def numMatchingSubseq(self, S, words):
            def isMatch(word, w_i, d_i):
                if w_i == len(word): return True
                l = dict_idxs[word[w_i]]
                if len(l) == 0 or d_i > l[-1]: return False
                i = l[bisect_left(l, d_i)]
                return isMatch(word, w_i + 1, i + 1)
    
            dict_idxs = defaultdict(list)
            for i in range(len(S)):
                dict_idxs[S[i]].append(i)
            return sum(isMatch(word, 0, 0) for word in words)
    



