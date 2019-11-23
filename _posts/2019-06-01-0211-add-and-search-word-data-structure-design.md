---
layout: post
title: 211. Add and Search Word - Data structure design
---
### Question
Design a data structure that supports the following two operations:

    
    
    void addWord(word)
    bool search(word)
    

search(word) can search a literal word or a regular expression string
containing only letters `a-z` or `.`. A `.` means it can represent any one
letter.

**Example:**

    
    
    addWord("bad")
    addWord("dad")
    addWord("mad")
    search("pad") -> false
    search("bad") -> true
    search(".ad") -> true
    search("b..") -> true
    

**Note:**  
You may assume that all words are consist of lowercase letters `a-z`.

### Solution 1
Using backtrack to check each character of word to search.

    
    
    public class WordDictionary {
        public class TrieNode {
            public TrieNode[] children = new TrieNode[26];
            public String item = "";
        }
        
        private TrieNode root = new TrieNode();
    
        public void addWord(String word) {
            TrieNode node = root;
            for (char c : word.toCharArray()) {
                if (node.children[c - 'a'] == null) {
                    node.children[c - 'a'] = new TrieNode();
                }
                node = node.children[c - 'a'];
            }
            node.item = word;
        }
    
        public boolean search(String word) {
            return match(word.toCharArray(), 0, root);
        }
        
        private boolean match(char[] chs, int k, TrieNode node) {
            if (k == chs.length) return !node.item.equals("");   
            if (chs[k] != '.') {
                return node.children[chs[k] - 'a'] != null && match(chs, k + 1, node.children[chs[k] - 'a']);
            } else {
                for (int i = 0; i < node.children.length; i++) {
                    if (node.children[i] != null) {
                        if (match(chs, k + 1, node.children[i])) {
                            return true;
                        }
                    }
                }
            }
            return false;
        }
    }


### Solution 2
This is a vanilla application of Trie. The main difficulty lies in how to deal
with the `.` character. I use a naive solution in this place: each time when I
reach a `.`, I simply traverse all the children of the current node and
recursively search the remaining substring starting from each of those
children.

    
    
    class TrieNode {
    public:
        bool word;
        TrieNode* children[26];
        TrieNode() {
            word = false;
            memset(children, NULL, sizeof(children));
        }
    };
    
    class WordDictionary {
    public:
        /** Initialize your data structure here. */
        WordDictionary() {
            
        }
        
        /** Adds a word into the data structure. */
        void addWord(string word) {
            TrieNode* node = root;
            for (char c : word) {
                if (!node -> children[c - 'a']) {
                    node -> children[c - 'a'] = new TrieNode();
                }
                node = node -> children[c - 'a'];
            }
            node -> word = true;
        }
        
        /** Returns if the word is in the data structure. A word could contain the dot character '.' to represent any one letter. */
        bool search(string word) {
            return search(word.c_str(), root);
        }
    private:
        TrieNode* root = new TrieNode();
        
        bool search(const char* word, TrieNode* node) {
            for (int i = 0; word[i] && node; i++) {
                if (word[i] != '.') {
                    node = node -> children[word[i] - 'a'];
                } else {
                    TrieNode* tmp = node;
                    for (int j = 0; j < 26; j++) {
                        node = tmp -> children[j];
                        if (search(word + i + 1, node)) {
                            return true;
                        }
                    }
                }
            }
            return node && node -> word;
        }
    };
    


### Solution 3
    
    
    class TrieNode():
        def __init__(self):
            self.children = collections.defaultdict(TrieNode)
            self.isWord = False
        
    class WordDictionary(object):
        def __init__(self):
            self.root = TrieNode()
    
        def addWord(self, word):
            node = self.root
            for w in word:
                node = node.children[w]
            node.isWord = True
    
        def search(self, word):
            node = self.root
            self.res = False
            self.dfs(node, word)
            return self.res
        
        def dfs(self, node, word):
            if not word:
                if node.isWord:
                    self.res = True
                return 
            if word[0] == ".":
                for n in node.children.values():
                    self.dfs(n, word[1:])
            else:
                node = node.children.get(word[0])
                if not node:
                    return 
                self.dfs(node, word[1:])



