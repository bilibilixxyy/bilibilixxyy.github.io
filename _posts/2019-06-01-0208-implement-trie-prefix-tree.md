---
layout: post
title: 208. Implement Trie (Prefix Tree)
---
### Question
Implement a trie with `insert`, `search`, and `startsWith` methods.

 **Example:**

    
    
    Trie trie = new Trie();
    
    trie.insert("apple");
    trie.search("apple");   // returns true
    trie.search("app");     // returns false
    trie.startsWith("app"); // returns true
    trie.insert("app");   
    trie.search("app");     // returns true
    

**Note:**

  * You may assume that all inputs are consist of lowercase letters `a-z`.
  * All inputs are guaranteed to be non-empty strings.

### Solution 1
Detailed explanation after code!

    
    
    class TrieNode {
        public char val;
        public boolean isWord; 
        public TrieNode[] children = new TrieNode[26];
        public TrieNode() {}
        TrieNode(char c){
            TrieNode node = new TrieNode();
            node.val = c;
        }
    }
    
    public class Trie {
        private TrieNode root;
        public Trie() {
            root = new TrieNode();
            root.val = ' ';
        }
    
        public void insert(String word) {
            TrieNode ws = root;
            for(int i = 0; i < word.length(); i++){
                char c = word.charAt(i);
                if(ws.children[c - 'a'] == null){
                    ws.children[c - 'a'] = new TrieNode(c);
                }
                ws = ws.children[c - 'a'];
            }
            ws.isWord = true;
        }
    
        public boolean search(String word) {
            TrieNode ws = root; 
            for(int i = 0; i < word.length(); i++){
                char c = word.charAt(i);
                if(ws.children[c - 'a'] == null) return false;
                ws = ws.children[c - 'a'];
            }
            return ws.isWord;
        }
    
        public boolean startsWith(String prefix) {
            TrieNode ws = root; 
            for(int i = 0; i < prefix.length(); i++){
                char c = prefix.charAt(i);
                if(ws.children[c - 'a'] == null) return false;
                ws = ws.children[c - 'a'];
            }
            return true;
        }
    }
    

With my solution I took the simple approach of giving each TrieNode a 26
element array of each possible child node it may have. I only gave 26 children
nodes because we are only working with lowercase 'a' - 'z'. If you are
uncertain why I made the root of my Trie an empty character this is a
standard/typical approach for building out a Trie it is somewhat arbitrary
what the root node is.

For insert I used the following algorithm. Loop through each character in the
word being inserted check if the character is a child node of the current
TrieNode i.e. check if the array has a populated value in the index of this
character. If the current character ISN'T a child node of my current node add
this character representation to the corresponding index location then set
current node equal to the child that was added. However if the current
character IS a child of the current node only set current node equal to the
child. After evaluating the entire String the Node we left off on is marked as
a _word_ this allows our Trie to know which words exist in our "dictionary"

For search I simply navigate through the Trie if I discover the current
character isn't in the Trie I return false.  
After checking each Char in the String I check to see if the Node I left off
on was marked as a word returning the result.

Starts with is identical to search except it doesn't matter if the Node I left
off was marked as a word or not if entire string evaluated i always return
true;


### Solution 2
    
    
    class TrieNode
    {
    public:
        TrieNode *next[26];
        bool is_word;
        
        // Initialize your data structure here.
        TrieNode(bool b = false)
        {
            memset(next, 0, sizeof(next));
            is_word = b;
        }
    };
    
    class Trie
    {
        TrieNode *root;
    public:
        Trie()
        {
            root = new TrieNode();
        }
    
        // Inserts a word into the trie.
        void insert(string s)
        {
            TrieNode *p = root;
            for(int i = 0; i < s.size(); ++ i)
            {
                if(p -> next[s[i] - 'a'] == NULL)
                    p -> next[s[i] - 'a'] = new TrieNode();
                p = p -> next[s[i] - 'a'];
            }
            p -> is_word = true;
        }
    
        // Returns if the word is in the trie.
        bool search(string key)
        {
            TrieNode *p = find(key);
            return p != NULL && p -> is_word;
        }
    
        // Returns if there is any word in the trie
        // that starts with the given prefix.
        bool startsWith(string prefix)
        {
            return find(prefix) != NULL;
        }
    
    private:
        TrieNode* find(string key)
        {
            TrieNode *p = root;
            for(int i = 0; i < key.size() && p != NULL; ++ i)
                p = p -> next[key[i] - 'a'];
            return p;
        }
    };


### Solution 3
    
    
    class TrieNode:
    # Initialize your data structure here.
    def __init__(self):
        self.children = collections.defaultdict(TrieNode)
        self.is_word = False
    
    class Trie:
    
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word):
        current = self.root
        for letter in word:
            current = current.children[letter]
        current.is_word = True
    
    def search(self, word):
        current = self.root
        for letter in word:
            current = current.children.get(letter)
            if current is None:
                return False
        return current.is_word
    
    def startsWith(self, prefix):
        current = self.root
        for letter in prefix:
            current = current.children.get(letter)
            if current is None:
                return False
        return True



