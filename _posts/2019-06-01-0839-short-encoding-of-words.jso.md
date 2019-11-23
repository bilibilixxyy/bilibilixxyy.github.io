---
layout: post
title: 839. Short Encoding of Words
---
### Question
Given a list of words, we may encode it by writing a reference string `S` and
a list of indexes `A`.

For example, if the list of words is `["time", "me", "bell"]`, we can write it
as `S = "time#bell#"` and `indexes = [0, 2, 5]`.

Then for each index, we will recover the word by reading from the reference
string from that index until we reach a `"#"` character.

What is the length of the shortest reference string S possible that encodes
the given words?

 **Example:**

    
    
     **Input:** words = ["time", "me", "bell"]
    **Output:** 10
    **Explanation:** S = "time#bell#" and indexes = [0, 2, 5].
    



 **Note:**

  1. `1 <= words.length <= 2000`.
  2. `1 <= words[i].length <= 7`.
  3. Each word has only lowercase letters.

### Solution 1
 **Deacription analyse**  
Let’s get the deacription clear:  
Given string list L1, construct a another string list L2, making every word in
L1 be a suffix of a word in L2.  
Return the minimum possible total length of words in L2  
Input L1: [“time”,“me”,“bell”]  
L2: [“time”,“bell”]

 **Explanation of solution**  
Base on @awice's idea. This solution is not my intuition but it is really
simple to write, compared with Trie solution.

  1. Build a set of words.
  2. Iterate on all words and remove all suffixes of every word from the set.
  3. Finally the set will the set of all encoding words.
  4. Iterate on the set and return `sum(word's length + 1 for every word in the set)`

 **Complexity**  
`O(NK^2)` for time and 'O(NK)' for space.  
It is really kind of `K` with `K <= 7`, almost ignorable.  
I should have suggested for bigger 'K' cases.  
I believe it will take more time for most people to solve this problem if we
have a big `K`.

 **C++:**

    
    
         int minimumLengthEncoding(vector<string>& words) {
            unordered_set<string> s(words.begin(), words.end());
            for (string w : s)
                for (int i = 1; i < w.size(); ++i)
                    s.erase(w.substr(i));
            int res = 0;
            for (string w : s) res += w.size() + 1;
            return res;
        }
    

**Java:**

    
    
         public int minimumLengthEncoding(String[] words) {
            Set<String> s = new HashSet<>(Arrays.asList(words));
            for (String w : words)
                for (int i = 1; i < w.length(); ++i)
                    s.remove(w.substring(i));
            int res = 0;
            for (String w : s) res += w.length() + 1;
            return res;
        }
    
    

**Python:**

    
    
        def minimumLengthEncoding(self,  words):
            s = set(words)
            for w in words:
                for i in range(1, len(w)):
                    s.discard(w[i:])
            return sum(len(w) + 1 for w in s)
    
    


### Solution 2
Let's get the deacription clear:  
Given string list L1, construct a another string list L2, making every word in
L1 be a suffix of a word in L2.  
Return the minimum possible total length of words in L2  
Input L1: ["time","me","bell"]  
L2: ["time","bell"]

So the idea of trie is really straightforward. It will be even more obvious if
it is sufix.  
What we should do here is insert all reversed words to a trie.  
Here are three steps:

 **1\. Define a trie.**

 **C++:**

    
    
     class TrieNode {
    public:
        unordered_map<char, TrieNode *> next;
    };
    
    

**Java:**

    
    
     class TrieNode {
        HashMap<Character, TrieNode> next = new HashMap<>();
        int depth;
    }
    

For python we can use `defaultdict` to realize a trie, however I just used
`dict`.  
Then we initialize a trie root.  
We can take the root node as the stop character `#` and it has depth 1.

**2\. Insert all reversed words to the trie.**  
For example, for word "time", we insert 'e', 'm', 'i', 't' successively.

**3\. Return the sum of depth of all leaves.**  
One way is to write a simple recursive function to do it.  
Here I save all last nodes of words and its depth to a list and all leaves
should be in this list.  
I iterate this list and all nodes without children are leaved.

**C++:**

    
    
     class Solution {
    public:
        int minimumLengthEncoding(vector<string>& words) {
            TrieNode *root = new TrieNode;
            vector<pair<TrieNode *, int>> leaves;
            for (auto & w : unordered_set<string> (words.begin(), words.end())) {
                TrieNode *cur = root;
                for (int i = w.length() - 1; i >= 0; --i) {
                    if (cur->next.count(w[i]) == 0) cur->next[w[i]] = new TrieNode;
                    cur = cur->next[w[i]];
                }
                leaves.push_back(make_pair(cur, w.length() + 1));
            }
            int res = 0;
            for (auto leaf : leaves) if ((leaf.first->next).size() == 0) res += leaf.second;
            return res;
        }
    };
    

**Java:**

    
    
     class Solution {
        public int minimumLengthEncoding(String[] words) {
            TrieNode root = new TrieNode();
            List<TrieNode> leaves = new  ArrayList<TrieNode>();
            for (String w : new HashSet<>(Arrays.asList(words))) {
                TrieNode cur = root;
                for (int i = w.length() - 1; i >= 0; --i) {
                    char j = w.charAt(i);
                    if (!cur.next.containsKey(j)) cur.next.put(j, new TrieNode());
                    cur = cur.next.get(j);
                }
                cur.depth = w.length() + 1;
                leaves.add(cur);
            }
            int res = 0;
            for (TrieNode leaf : leaves) if (leaf.next.isEmpty()) res += leaf.depth;
            return res;
        }
    }
    
    

**Python:**

    
    
        def minimumLengthEncoding(self,  words):
            root = dict()
            leaves = []
            for word in set(words):
                cur = root
                for i in word[::-1]:
                    cur[i] = cur = cur.get(i, dict())
                leaves.append((cur, len(word) + 1))
            return sum(depth for node, depth in leaves if len(node) == 0)
    
    


### Solution 3
Came up with a solution that failed one case. After studying the solution, I
realized that you do not need to keep the order of the words. Please add
necessary clarifications!



